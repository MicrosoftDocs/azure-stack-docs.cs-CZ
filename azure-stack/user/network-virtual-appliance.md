---
title: Řešení potíží se síťovými virtuálními zařízení v centru Azure Stack
description: Řešení potíží s připojením k virtuálnímu počítači nebo k síti VPN při použití síťového virtuálního zařízení (síťové virtuální zařízení) třetí strany v centru Microsoft Azure Stack.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5e42f9e1b099df2ab1dc1063b3bf2710f59ad66f
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342890"
---
# <a name="troubleshoot-network-virtual-appliance-issues"></a>Řešení potíží se síťovými virtuálními zařízení

Při použití síťového virtuálního zařízení (síťové virtuální zařízení) jiného výrobce v centru Microsoft Azure Stack se může vyskytnout chyba a problémy s připojením k virtuálnímu počítači nebo k síti VPN. Tento článek popisuje základní kroky, které vám pomůžou při ověřování základních požadavků na platformu centra Azure Stack pro konfigurace síťové virtuální zařízení.

Technickou podporu pro síťová virtuální zařízení třetích stran a jejich integraci s platformou centra Azure Stack poskytuje dodavatel síťové virtuální zařízení.

> [!NOTE]
> Pokud máte potíže s připojením nebo směrováním, které zahrnuje síťové virtuální zařízení, měli byste [se obrátit na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) přímo.

Pokud se v tomto článku neřeší problém síťové virtuální zařízení centra Azure Stack, vytvořte prosím [lístek podpory centra Azure Stack](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Kontrolní seznam pro řešení potíží s dodavatelem síťové virtuální zařízení

- Aktualizace softwaru pro software síťové virtuální zařízení VM
- Nastavení a funkce účtu služby.
- Trasy definované uživatelem (udr) v podsítích virtuální sítě, které směrují provoz do síťové virtuální zařízení.
- Udr v podsítích virtuální sítě, které směrují provoz z síťové virtuální zařízení.
- Směrovací tabulky a pravidla v rámci síťové virtuální zařízení (například z NIC1 na NIC2).
- Trasování na síťových kartách síťové virtuální zařízení, aby se ověřilo přijímání a odesílání síťových přenosů.

## <a name="basic-troubleshooting-steps"></a>Základní kroky pro řešení potíží

- Ověřte základní konfiguraci.
- Zkontroluje výkon síťové virtuální zařízení.
- Pokročilé řešení potíží se sítí.

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Ověřit minimální požadavky na konfiguraci pro síťová virtuální zařízení v Azure

Každý síťové virtuální zařízení má základní požadavky na konfiguraci pro správné fungování v Azure Stackovém centru. Následující část uvádí postup ověření těchto základních konfigurací. Další informace získáte [od dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

> [!IMPORTANT]
> Při použití tunelového propojení S2S jsou pakety dále zapouzdřeny pomocí dalších hlaviček. Tento zapouzdření zvyšuje celkovou velikost paketu. V těchto scénářích je potřeba, abyste v **1350**zasvorki TCP **MSS** . Pokud vaše zařízení VPN nepodporují svorky MSS, můžete místo toho nastavit jednotku MTU v rozhraní tunelu na **1400** bajtů. Další informace najdete v tématu [ladění výkonu protokolu virtuální sítě TCP](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

### <a name="check-whether-ip-forwarding-is-enabled-on-nva"></a>Ověřte, jestli je v síťové virtuální zařízení povolené předávání IP.

#### <a name="use-the-azure-stack-hub-portal"></a>Použití portálu centra Azure Stack

- Na portálu centra Azure Stack vyhledejte prostředek síťové virtuální zařízení, vyberte **sítě**a pak vyberte síťové rozhraní.
- Na stránce **síťové rozhraní** vyberte **Konfigurace protokolu IP**.
- Ujistěte se, že je povoleno předávání IP.

#### <a name="use-powershell"></a>Použití prostředí PowerShell

- Spusťte následující příkaz (nahraďte hodnoty v závorkách vašimi informacemi):

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

- Ověřte vlastnost **EnableIPForwarding** .
- Pokud nepovolíte předávání IP, spusťte následující příkazy:

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-the-traffic-can-be-routed-to-the-nva"></a>Ověřte, jestli je možné směrovat provoz do síťové virtuální zařízení.

- Vyhledejte virtuální počítač, který je nakonfigurovaný pro přesměrování provozu do síťové virtuální zařízení.
- Spusťte `Tracert <Private IP of NVA>` systém Windows nebo `Traceroute <Private IP of NVA>` vyhledejte síťové virtuální zařízení jako další segment směrování.
- Pokud síťové virtuální zařízení není uveden jako další segment směrování, vyhledejte a aktualizujte směrovací tabulky centra Azure Stack.

Některé operační systémy na úrovni hosta můžou mít zavedeny zásady brány firewall pro blokování provozu protokolu ICMP. Aby předchozí příkazy fungovaly, musí se tato pravidla firewallu aktualizovat.

### <a name="check-whether-the-traffic-can-reach-the-nva"></a>Ověřte, jestli přenosy můžou dosáhnout pro síťové virtuální zařízení.

- Vyhledejte virtuální počítač, který by měl mít připojení k síťové virtuální zařízení.
- Spusťte `ping (ICMP)` nebo `Test-NetConnection (TCP) <Private IP of NVA>` pro Windows a `Tcpping <Private IP of NVA>` pro Linux ověřte, jestli jsou přenosy blokované všemi skupinami zabezpečení sítě.
- Pokud je provoz blokovaný, upravte skupin zabezpečení sítě tak, aby umožňoval provoz.

### <a name="check-whether-nva-and-vms-are-listening-for-expected-traffic"></a>Ověřte, jestli síťové virtuální zařízení a virtuální počítače naslouchají očekávanému provozu.

- Připojte se k síťové virtuální zařízení pomocí protokolu RDP nebo SSH a potom spusťte následující příkaz:

   **Windows**:
   ```shell
   netstat -an
   ```

   **Linux**:
   ```shell
   netstat -an | grep -i listen
   ```

- Pokud nevidíte port TCP, který je používán softwarem síťové virtuální zařízení, který je uvedený ve výsledcích, musíte aplikaci nakonfigurovat na síťové virtuální zařízení a virtuálním počítači, aby naslouchaly a reagovaly na provoz, který dosahuje těchto portů. Pokud potřebujete [pomoc, obraťte se na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

## <a name="check-nva-performance"></a>Kontrolovat výkon síťové virtuální zařízení

### <a name="validate-vm-cpu"></a>Ověřit procesor virtuálního počítače

Pokud se využití CPU blíží 100%, může docházet k problémům, které mají vliv na zahození síťového paketu. Během špičky procesoru zkontrolujte, který proces na virtuálním počítači hosta způsobuje vysoký procesor, a pokud je to možné, zmírnit ho. Možná budete muset změnit velikost virtuálního počítače na větší velikost SKU nebo pro sadu škálování virtuálního počítače zvýšit počet instancí. U každého z těchto problémů se [podle potřeby obraťte na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="validate-vm-network-statistics"></a>Ověřit statistiku sítě virtuálních počítačů

Pokud síť virtuálních počítačů využívá špičky nebo zobrazuje období vysokého využití, může být také nutné zvýšit velikost SKU virtuálního počítače, aby se získaly možnosti vyšší propustnosti.

## <a name="advanced-network-administrator-troubleshooting"></a>Pokročilý poradce při potížích správce sítě

### <a name="capture-network-trace"></a>Zachytit trasování sítě

Zaznamenejte si souběžnou síťovou trasu na zdrojovém virtuálním počítači, síťové virtuální zařízení a cílový virtuální počítač, zatímco spouštíte [**PsPing**](/sysinternals/downloads/psping) nebo **nmap**, a pak trasování zastavte.

- Pokud chcete zachytit síťové trasování, spusťte následující příkaz:

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

- Použijte **PsPing** nebo **nmap** ze zdrojového virtuálního počítače do cílového virtuálního počítače (například: `PsPing 10.0.0.4:80` nebo `Nmap -p 80 10.0.0.4` ).

- Otevřete trasování sítě z cílového virtuálního počítače pomocí **tcpdump** nebo analyzátoru paketů podle vašeho výběru. Použijte filtr zobrazení pro IP adresu zdrojového virtuálního počítače, na kterém jste spustili **PsPing** nebo **nmap** , jako je například `IPv4.address==10.0.0.4` (Windows Netmon) nebo `tcpdump -nn -r vmtrace.cap src` nebo `dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Analyzovat trasování

Pokud nevidíte pakety, které přijdou do trasování virtuálního počítače back-end, je pravděpodobný vliv na NSG nebo UDR, nebo jsou tabulky směrování síťové virtuální zařízení nesprávné.

Pokud se zobrazí pakety přicházející v, ale bez odpovědi, možná budete muset vyřešit potíže s aplikací virtuálního počítače nebo bránou firewall. U každého z těchto problémů se [podle potřeby obraťte na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

### <a name="create-a-support-ticket"></a>Vytvoření lístku podpory

Pokud žádný z předchozích kroků problém nevyřeší, vytvořte prosím [lístek podpory](../operator/azure-stack-manage-basics.md#where-to-get-support) a použijte [Nástroj pro shromažďování protokolů na vyžádání](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) k poskytnutí protokolů.
