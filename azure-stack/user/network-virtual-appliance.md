---
title: Řešení problémů se síťovým virtuálním zařízením v centru Azure Stack
description: Řešení potíží s připojením k virtuálnímu počítači nebo VPN při použití síťového virtuálního zařízení (síťové virtuální zařízení) v centru Microsoft Azure Stack.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: f933e9c4e70f533d4194b48c7b9e4d6e4bf380b0
ms.sourcegitcommit: d5d89bbe8a3310acaff29a7a0cd7ac4f2cf5bfe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83554960"
---
# <a name="troubleshoot-network-virtual-appliance-problems"></a>Řešení potíží se síťovými virtuálními zařízení

Můžete se setkat s problémy s připojením k virtuálním počítačům nebo VPN, které používají síťové virtuální zařízení (síťové virtuální zařízení) v centru Azure Stack.

Tento článek popisuje kroky, které vám pomůžou ověřit požadavky na základní platformu centra Azure Stack pro konfigurace síťové virtuální zařízení.

Dodavatel síťové virtuální zařízení poskytuje technickou podporu pro síťové virtuální zařízení a jeho integraci s platformou centra Azure Stack.

> [!NOTE]
> Pokud máte potíže s připojením nebo směrováním, které zahrnuje síťové virtuální zařízení, měli byste [se obrátit přímo na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) .

Pokud tento článek neřeší váš problém síťové virtuální zařízení pomocí centra Azure Stack, vytvořte [lístek podpory Azure Stack centra](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-an-nva-vendor"></a>Kontrolní seznam pro řešení potíží s dodavatelem síťové virtuální zařízení

- Aktualizace pro software síťové virtuální zařízení VM
- Nastavení a funkce účtu služby.
- Trasy definované uživatelem (udr) v podsítích virtuální sítě, které směrují provoz na síťové virtuální zařízení.
- Udr v podsítích virtuální sítě, které směrují provoz z síťové virtuální zařízení.
- Směrovací tabulky a pravidla v rámci síťové virtuální zařízení (například z NIC1 na NIC2).
- Trasování na síťových kartách síťové virtuální zařízení, aby se ověřilo přijímání a odesílání síťových přenosů.

## <a name="basic-troubleshooting-steps"></a>Základní kroky pro řešení potíží

1. Ověřte základní konfiguraci.
1. Zkontroluje výkon síťové virtuální zařízení.
1. Pokročilé řešení potíží se sítí.

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Ověřit minimální požadavky na konfiguraci pro síťová virtuální zařízení v Azure

Každý síťové virtuální zařízení musí splňovat základní požadavky na konfiguraci pro správné fungování v Azure Stackovém centru. V této části se dozvíte o krocích při ověřování těchto základních konfigurací. Další informace získáte [od dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Pokud pakety používají tunel S2S, jsou dále zapouzdřeny pomocí dalších hlaviček. Tento zapouzdření zvyšuje celkovou velikost každého paketu.
>
> V tomto scénáři je nutné, aby byl port TCP MSS na 1 350 bajtů. Pokud zařízení VPN nepodporují svorky MSS, můžete místo toho nastavit jednotku MTU v rozhraní tunelu na 1 400 bajtů.
>
> Další informace najdete v tématu [ladění výkonu virtuální sítě TCP/IP](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-the-nva"></a>Ověřte, jestli je na síťové virtuální zařízení povolené předávání IP.

#### <a name="use-the-azure-stack-hub-portal"></a>Použití portálu centra Azure Stack

1. Na portálu centra Azure Stack vyhledejte prostředek síťové virtuální zařízení, vyberte **sítě**a pak vyberte síťové rozhraní.
1. Na stránce **síťové rozhraní** vyberte **Konfigurace protokolu IP**.
1. Ujistěte se, že je povoleno předávání IP.

#### <a name="use-powershell"></a>Použití prostředí PowerShell

1. Spusťte následující příkaz. Hodnoty v lomených závorkách nahraďte vašimi informacemi.

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

1. Ověřte vlastnost **EnableIPForwarding** .
1. Pokud nepovolíte předávání IP, spusťte následující příkazy:

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-traffic-can-be-routed-to-the-nva"></a>Ověřte, jestli je možné směrovat provoz do síťové virtuální zařízení.

1. Vyhledejte virtuální počítač, který je nakonfigurovaný pro přesměrování provozu do síťové virtuální zařízení.
1. Pokud chcete ověřit, že síťové virtuální zařízení je dalším segmentem směrování, spusťte privátní IP adresu **příkazu tracert pro \< \> síťové virtuální zařízení** pro Windows nebo **traceroute \< Private IP adresa síťové virtuální zařízení \> **.
1. Pokud síťové virtuální zařízení není uveden jako další segment směrování, vyhledejte a aktualizujte směrovací tabulky centra Azure Stack.

Některé operační systémy na úrovni hosta můžou mít zavedeny zásady brány firewall pro blokování provozu protokolu ICMP. Aktualizujte pravidla brány firewall pro předchozí příkazy, aby fungovaly.

### <a name="check-whether-traffic-can-reach-the-nva"></a>Ověřte, jestli přenosy můžou dosáhnout pro síťové virtuální zařízení.

1. Vyhledejte virtuální počítač, který by měl mít připojení k síťové virtuální zařízení.
1. Ověřte, jestli některé skupiny zabezpečení sítě (skupin zabezpečení sítě) zablokují provoz. Pro Windows spusťte **příkaz Test** -síťové virtuální zařízení ** \< \> (ICMP) nebo test-NetConnection privátní IP adresa** (TCP). Pro Linux spusťte **Tcpping \< privátní IP adresu síťové virtuální zařízení \> **.
1. Pokud vaše skupin zabezpečení sítě blok přenosů, upravte je tak, aby umožňovaly provoz.

### <a name="check-whether-the-nva-and-vms-are-listening-for-expected-traffic"></a>Ověřte, jestli síťové virtuální zařízení a virtuální počítače naslouchají očekávanému provozu.

1. Připojte se k síťové virtuální zařízení pomocí protokolu RDP nebo SSH a pak spusťte následující příkaz:

   **Windows**

   ```shell
   netstat -an
   ```

   **Linux**

   ```shell
   netstat -an | grep -i listen
   ```

1. Vyhledejte porty TCP používané softwarem síťové virtuální zařízení, který je uvedený ve výsledcích. Pokud je nevidíte, nakonfigurujte aplikaci na síťové virtuální zařízení a virtuálním počítači tak, aby naslouchaly a reagovaly na provoz, který dosahuje těchto portů. [Požádejte o pomoc dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Kontrolovat výkon síťové virtuální zařízení

### <a name="validate-vm-cpu-usage"></a>Ověřit využití procesoru virtuálního počítače

Pokud se využití CPU blíží 100%, může docházet k problémům, které mají vliv na zahození síťového paketu.

Během špičky procesoru Zjistěte, který proces na virtuálním počítači hosta způsobuje vysoké využití procesoru. Pokud je to možné, zmírnit využití.

Možná budete muset změnit velikost virtuálního počítače na větší velikost SKU nebo pro sadu škálování virtuálního počítače zvýšit počet instancí.

Pokud potřebujete pomoc, [obraťte se na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Ověřit statistiku sítě virtuálních počítačů

Pokud síť virtuálních počítačů využívá špičky nebo zobrazuje období vysokého využití, zvažte zvýšení propustnosti zvýšením velikosti SKU virtuálního počítače.

## <a name="advanced-network-administrator-troubleshooting"></a>Pokročilý poradce při potížích správce sítě

### <a name="capture-a-network-trace"></a>Zaznamenat trasování sítě

Když spouštíte [**PsPing**](/sysinternals/downloads/psping) nebo **nmap**, zachytíte souběžnou síťovou trasu na zdrojovém a cílovém virtuálním počítači a na síťové virtuální zařízení. Pak trasování zastavte.

1. Pokud chcete zachytit síťové trasování, spusťte následující příkaz:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

2. Použijte **PsPing** nebo **nmap** ze zdrojového virtuálního počítače do cílového virtuálního počítače. Příklady jsou **PsPing 10.0.0.4:80** nebo **Nmap-p 80 10.0.0.4**.

3. Otevřete trasování sítě z cílového virtuálního počítače pomocí **tcpdump** nebo analyzátoru paketů podle vašeho výběru. Použijte filtr zobrazení pro IP adresu zdrojového virtuálního počítače, ze kterého jste spustili **PsPing** nebo **nmap** . Příkladem pro Windows **Netmon** je **IPv4. adresa = = 10.0.0.4**. Příklady pro Linux jsou **tcpdump-NN-r vmtrace. Cap src** a **DST Host 10.0.0.4**.

### <a name="analyze-traces"></a>Analyzovat trasování

Pokud nevidíte pakety do trasování virtuálních počítačů back-endu, je pravděpodobně ovlivněná NSG nebo UDR, nebo nejsou směrovací tabulky síťové virtuální zařízení správné.

Pokud vidíte, že se pakety přidávají, ale bez odpovědi, může být nutné vyřešit problém s aplikací nebo bránou firewall virtuálního počítače.

Pokud potřebujete pomoc, [obraťte se na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Vytvoření lístku podpory

Pokud předchozí kroky problém nevyřeší, vytvořte [lístek podpory](../operator/azure-stack-manage-basics.md#where-to-get-support) a použijte [Nástroj pro shromažďování protokolů na vyžádání](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) k poskytnutí protokolů.
