---
title: Řešení problémů se síťovým virtuálním zařízením v centru Azure Stack
description: Řešení potíží s připojením k virtuálnímu počítači nebo VPN při použití síťového virtuálního zařízení (síťové virtuální zařízení) v centru Microsoft Azure Stack.
author: sethmanheim
ms.author: sethm
ms.date: 09/08/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 0facc0cc06ad3ff672531f1eeb7e31eee2f56ee0
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546884"
---
# <a name="troubleshoot-network-virtual-appliance-problems"></a>Řešení potíží se síťovými virtuálními zařízení

Můžete se setkat s problémy s připojením k virtuálním počítačům nebo VPN, které používají síťové virtuální zařízení (síťové virtuální zařízení) v centru Azure Stack.

Tento článek vám pomůže ověřit základní požadavky na platformu Azure Stack hub pro konfigurace síťové virtuální zařízení.

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
2. Zkontroluje výkon síťové virtuální zařízení.
3. Pokročilé řešení potíží se sítí.

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

1. Na portálu centra Azure Stack vyhledejte prostředek síťové virtuální zařízení, vyberte **sítě** a pak vyberte síťové rozhraní.
2. Na stránce **síťové rozhraní** vyberte **Konfigurace protokolu IP**.
3. Ujistěte se, že je povoleno předávání IP.

#### <a name="use-powershell"></a>Použití prostředí PowerShell

1. Spusťte následující příkaz. Hodnoty v lomených závorkách nahraďte vašimi informacemi.

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

2. Ověřte vlastnost **EnableIPForwarding** .

3. Pokud nepovolíte předávání IP, spusťte následující příkazy:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-traffic-can-be-routed-to-the-nva"></a>Ověřte, jestli je možné směrovat provoz do síťové virtuální zařízení.

1. Vyhledejte virtuální počítač, který je nakonfigurovaný pro přesměrování provozu do síťové virtuální zařízení.
1. Chcete-li ověřit, zda je síťové virtuální zařízení dalším segmentem směrování, spusťte příkaz `Tracert <Private IP of NVA>` pro systém Windows nebo `Traceroute <Private IP of NVA>` .
1. Pokud síťové virtuální zařízení není uveden jako další segment směrování, vyhledejte a aktualizujte směrovací tabulky centra Azure Stack.

Některé operační systémy na úrovni hosta můžou mít zavedeny zásady brány firewall pro blokování provozu protokolu ICMP. Aktualizujte pravidla brány firewall pro předchozí příkazy, aby fungovaly.

### <a name="check-whether-traffic-can-reach-the-nva"></a>Ověřte, jestli přenosy můžou dosáhnout pro síťové virtuální zařízení.

1. Vyhledejte virtuální počítač, který by měl mít připojení k síťové virtuální zařízení.
1. Ověřte, jestli některé skupiny zabezpečení sítě (skupin zabezpečení sítě) zablokují provoz. Pro Windows spusťte `ping` (ICMP) nebo `Test-NetConnection <Private IP of NVA>` (TCP). Pro Linux spusťte `Tcpping <Private IP of NVA>` .
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

### <a name="capture-a-network-trace"></a>Zachycení trasování sítě

Když spouštíte [`PsPing`](/sysinternals/downloads/psping) nebo `Nmap` , zachytíte síťové trasování na zdrojovém a cílovém virtuálním počítači a v síťové virtuální zařízení. Pak trasování zastavte.

1. Pokud chcete zachytit síťové trasování, spusťte následující příkaz:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

2. Použijte `PsPing` nebo `Nmap` ze zdrojového virtuálního počítače do cílového virtuálního počítače. Příklady jsou `PsPing 10.0.0.4:80` nebo `Nmap -p 80 10.0.0.4` .

3. Otevřete trasování sítě z cílového virtuálního počítače pomocí **tcpdump** nebo analyzátoru paketů podle vašeho výběru. Použijte filtr zobrazení pro IP adresu zdrojového virtuálního počítače, který jste spustili `PsPing` nebo `Nmap` z. Příklad Windows **Netmon** je `IPv4.address==10.0.0.4` . Příklady pro Linux jsou `tcpdump -nn -r vmtrace.cap src` a `dst host 10.0.0.4` .

### <a name="analyze-traces"></a>Analyzovat trasování

Pokud nevidíte pakety do trasování virtuálních počítačů back-endu, je pravděpodobně ovlivněná NSG nebo UDR, nebo nejsou směrovací tabulky síťové virtuální zařízení správné.

Pokud vidíte, že se pakety přidávají, ale bez odpovědi, může být nutné vyřešit problém s aplikací nebo bránou firewall virtuálního počítače.

Pokud potřebujete pomoc, [obraťte se na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Vytvoření lístku podpory

Pokud předchozí kroky problém nevyřeší, vytvořte [lístek podpory](../operator/azure-stack-manage-basics.md#where-to-get-support) a použijte [Nástroj pro shromažďování protokolů na vyžádání](../operator/azure-stack-diagnostic-log-collection-overview.md) k poskytnutí protokolů.
