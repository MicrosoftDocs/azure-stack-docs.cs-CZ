---
title: Vytvoření clusteru Azure Stack HCI pomocí prostředí Windows PowerShell
description: Naučte se vytvořit cluster s více sblíženými Azure Stack HCL pomocí prostředí Windows PowerShell.
author: v-dasis
ms.topic: how-to
ms.date: 08/11/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 4bd669e04f2b4b4e1ef173a3a44e52d8c6067a60
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064510"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-powershell"></a>Vytvoření clusteru Azure Stack HCI pomocí prostředí Windows PowerShell

> Platí pro: Azure Stack HCI, verze v20H2

V tomto článku se naučíte, jak pomocí prostředí Windows PowerShell vytvořit Azure Stackého clusteru HCI s více instancemi, který používá Prostory úložiště s přímým přístupem. Pokud místo toho chcete vytvořit cluster pomocí Průvodce vytvořením clusteru v centru pro správu Windows, přečtěte si téma [Vytvoření clusteru pomocí centra pro správu systému Windows](create-cluster.md).

Mezi dvěma typy clusterů můžete vybrat:

- Standardní cluster s alespoň dvěma serverovými uzly, který je umístěn v jedné lokalitě.
- Roztažené clustery s nejméně čtyřmi serverovými uzly, které jsou rozloženy na dvě lokality se dvěma uzly na jeden web.

V tomto článku vytvoříme ukázkový cluster s názvem Cluster1, který se skládá ze čtyř uzlů serveru s názvem Server1, Server2, Server3 a Server4.

Pro scénář roztaženého clusteru použijeme jako název ClusterS1 a použijete stejné čtyři uzly serveru roztažené napříč lokalitami Site1 a site2.

Další informace o roztažené clustery najdete v tématu [Přehled roztaženého clusteru](../concepts/stretched-clusters.md).

Pokud vás zajímá testování Azure Stack HCI, ale mají omezený nebo žádný náhradní hardware, Projděte si [příručku pro vyhodnocení Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), kde Vás provedeme Azure Stack HCL pomocí vnořené virtualizace, ať už v Azure, nebo v jednom fyzickém systému v místním prostředí.

## <a name="before-you-begin"></a>Než začnete

Než začnete, ujistěte se, že jste:

- Přečtěte si téma [požadavky na systém Azure Stack HCI](../concepts/system-requirements.md).
- Nainstalujte do každého serveru v clusteru Azure Stack operační systém HCI. Viz [nasazení operačního systému Azure Stack HCI](operating-system.md).
- Mít účet, který je členem místní skupiny Administrators na každém serveru.
- Mít práva ve službě Active Directory k vytváření objektů.

## <a name="using-windows-powershell"></a>Použití Windows PowerShellu

PowerShell můžete buď spustit místně v relaci RDP na hostitelském serveru, nebo můžete PowerShell spustit vzdáleně z počítače pro správu. Tento článek se zabývá možností vzdálené.

Při spuštění prostředí PowerShell z počítače pro správu zahrňte `-Name` `-Cluster` parametr nebo s názvem serveru nebo clusteru, který spravujete. Kromě toho může být při použití `-ComputerName` parametru pro uzel serveru potřeba zadat plně kvalifikovaný název domény (FQDN).

Pro Hyper-V a clustering s podporou převzetí služeb při selhání budete potřebovat také rutiny Nástroje pro vzdálenou správu serveru (RSAT) a moduly prostředí PowerShell. Pokud nejsou již v relaci PowerShellu v počítači pro správu k dispozici, můžete je přidat pomocí následujícího příkazu: `Add-WindowsFeature RSAT-Clustering-PowerShell` .

## <a name="step-1-provision-the-servers"></a>Krok 1: zřízení serverů

Nejprve se připojíme ke každému serveru, připojíte se k doméně (stejná doména, ve které je počítač pro správu), a nainstalujete požadované role a funkce.

### <a name="step-11-connect-to-the-servers"></a>Krok 1,1: připojení k serverům

Abyste se mohli připojit k serverům, musíte nejdřív mít připojení k síti, připojit se ke stejné doméně nebo plně důvěryhodné doméně a mít k těmto serverům oprávnění místního správce.

Otevřete PowerShell a použijte plně kvalifikovaný název domény nebo IP adresu serveru, ke kterému se chcete připojit. Po spuštění následujícího příkazu na každém serveru (Server1, Server2, Server3, Server4) se zobrazí výzva k zadání hesla:

   ```powershell
   Enter-PSSession -ComputerName "Server1" -Credential "Server1\Administrator"
   ```

Tady je další příklad stejné věci:

   ```powershell
   $myServer1 = "Server1"
   $user = "$myServer1\Administrator"

   Enter-PSSession -ComputerName $myServer1 -Credential $user
   ```

> [!TIP]
> Pokud spouštíte příkazy prostředí PowerShell z počítače pro správu, může se zobrazit chyba, například *Služba WinRM nemůže zpracovat požadavek.* Pokud to chcete opravit, přidejte jednotlivé servery do seznamu Důvěryhodné hostitele na počítači pro správu pomocí PowerShellu. Tento seznam podporuje zástupné znaky, jako `Server*` například.
>
> `Set-Item WSMAN:\Localhost\Client\TrustedHosts -Value Server1 -Force`
>  
> Seznam důvěryhodných hostitelů zobrazíte tak, že zadáte `Get-Item WSMAN:\Localhost\Client\TrustedHosts` .  
>
> Seznam můžete vyprázdnit zadáním `Clear-Item WSMAN:\Localhost\Client\TrustedHost` .  

### <a name="step-12-join-the-domain-and-add-domain-accounts"></a>Krok 1,2: Připojte se k doméně a přidejte účty domény.

Zatím jste se připojili ke každému uzlu serveru pomocí účtu místního správce `<ServerName>\Administrator` .

Chcete-li pokračovat, bude nutné připojit servery k doméně a použít účet domény, který je v místní skupině Administrators na každém serveru.

Pomocí `Enter-PSSession` rutiny se připojte ke každému serveru a spusťte následující rutinu, kde nahradíte název serveru, název domény a přihlašovací údaje domény:

```powershell  
Add-Computer -NewName "Server1" -DomainName "contoso.com" -Credential "Contoso\User" -Restart -Force  
```

Pokud váš účet správce není členem skupiny Domain Admins, přidejte účet správce do místní skupiny Administrators na každém serveru, nebo to ještě lépe, přidejte skupinu, kterou používáte pro správce. K tomu můžete použít následující příkaz:

```powershell
Add-LocalGroupMember -Group "Administrators" -Member "king@contoso.local"
```

### <a name="step-13-install-roles-and-features"></a>Krok 1,3: Instalace rolí a funkcí

Dalším krokem je instalace požadovaných rolí a funkcí systému Windows na všech serverech clusteru. Tady jsou role, které se mají nainstalovat:

- BitLocker
- Přemostění Datacenter (pro síťové adaptéry RoCEv2)
- Clustering s podporou převzetí služeb při selhání
- Souborový server
- FS – modul odstranění duplicitních dat
- Hyper-V
- RSAT – modul AD-PowerShell
- Replika úložiště (jenom pro roztažené clustery)

Pro každý server použijte následující příkaz:

```powershell
Install-WindowsFeature -ComputerName "Server1" -Name "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-Clustering-PowerShell", "Storage-Replica" -IncludeAllSubFeature -IncludeManagementTools
```

Pokud chcete spustit příkaz na všech serverech v clusteru současně, použijte následující skript, který upraví seznam proměnných na začátku, aby odpovídal vašemu prostředí.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"
$FeatureList = "BitLocker", "Data-Center-Bridging", "Failover-Clustering", "FS-FileServer", "Hyper-V", "Hyper-V-PowerShell", "RSAT-Clustering-PowerShell", "Storage-Replica"

# This part runs the Install-WindowsFeature cmdlet on all servers in $ServerList, passing the list of features in $FeatureList.
Invoke-Command ($ServerList) {
    Install-WindowsFeature -Name $Using:Featurelist -IncludeAllSubFeature -IncludeManagementTools
}
```
Pak restartujte všechny servery:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Restart-Computer -ComputerName $ServerList
```

## <a name="step-2-configure-networking"></a>Krok 2: konfigurace sítě

Tento krok nakonfiguruje různé prvky sítě ve vašem prostředí.

### <a name="disable-unused-networks"></a>Zakázat nepoužívané sítě

Je nutné zakázat všechny sítě odpojené nebo nepoužívané pro správu, úložiště nebo provoz úloh (například virtuální počítače). Zde zjistíte, jak identifikovat nepoužívané sítě:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-NetAdapter -CimSession $Servers | Where-Object Status -eq Disconnected
```
A tady je postup, jak je zakázat:

```powershell
Get-NetAdapter -CimSession $Servers | Where-Object Status -eq Disconnected | Disable-NetAdapter -Confirm:$False
```

### <a name="assign-virtual-network-adapters"></a>Přiřazení virtuálních síťových adaptérů

Dále přiřadíte virtuální síťové adaptéry (virtuální síťové adaptéry) pro správu a zbytek provozu, jak je uvedeno v následujícím příkladu. Je nutné nakonfigurovat alespoň jeden síťový adaptér pro správu clusteru.

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
Rename-VMNetworkAdapter -ManagementOS -Name $vSwitchName -NewName Management -ComputerName $Servers
Add-VMNetworkAdapter -ManagementOS -Name SMB01 -SwitchName $vSwitchName -CimSession $Servers
Add-VMNetworkAdapter -ManagementOS -Name SMB02 -SwitchName $vSwitchName -Cimsession $Servers
```

A ověřte, že byly úspěšně přidány a přiřazeny:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-VMNetworkAdapter -CimSession $Servers -ManagementOS
```

### <a name="create-virtual-switches"></a>Vytvořit virtuální přepínače

Pro každý uzel serveru v clusteru je nutný virtuální přepínač. V následujícím příkladu se virtuální přepínač s funkcí SR-IOV vytvoří pomocí síťových adaptérů, které jsou připojené (stav je zapnutý). Povolené rozhraní SR-IOV může být užitečné také v případě, že je vyžadováno pro RDMA s povoleným Vmnics;) (virtuální síťové adaptéry pro virtuální počítače).

Všechny síťové adaptéry musí být u síťových adaptérů pro seskupování identické.

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$vSwitchName="vSwitch"
```

A vytvořte virtuální přepínač:

```powershell
Invoke-Command -ComputerName $Servers -ScriptBlock {New-VMSwitch -Name $using:vSwitchName -EnableEmbeddedTeaming $TRUE -EnableIov $true -NetAdapterName (Get-NetAdapter | Where-Object Status -eq Up ).InterfaceAlias}
```

Nyní ověřte, zda byl přepínač úspěšně vytvořen:

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
Get-VMSwitch -CimSession $Servers | Select-Object Name, IOVEnabled, IOVS*
Get-VMSwitchTeam -CimSession $Servers
```

### <a name="configure-ip-addresses-and-vlans"></a>Konfigurace IP adres a sítí VLAN

Můžete nakonfigurovat jednu nebo dvě podsítě. Pokud chcete zabránit přetěžování propojení přepínačů, je vhodné, aby dvě podsítě byly preferovány. Například provoz úložiště protokolu SMB zůstane v podsíti, která je vyhrazená pro jeden fyzický přepínač.

### <a name="obtain-network-interface-information"></a>Získat informace o síťovém rozhraní

Než budete moct nastavit IP adresy pro síťové karty, budete nejdřív potřebovat nějaké informace, třeba index rozhraní ( `ifIndex` ), `Interface Alias` a `Address Family` . Napište je pro každý uzel serveru, jak je budete potřebovat později.

Spusťte následující příkazy:

```powershell
$ServerList = "Server1", "Server2", "Server3", "Server4"
Get-NetIPInterface -ComputerName $ServerList
```

#### <a name="configure-one-subnet"></a>Konfigurace jedné podsítě

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$StorNet="172.16.1."
$StorVLAN=1
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $Servers){
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    $IP++
    New-NetIPAddress -IPAddress ($StorNet+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN -Access -ManagementOS -CimSession $Servers
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN -Access -ManagementOS -CimSession $Servers
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="configure-two-subnets"></a>Konfigurace dvou podsítí

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
$StorNet1="172.16.1."
$StorNet2="172.16.2."
$StorVLAN1=1
$StorVLAN2=2
$IP=1 #starting IP Address

#Configure IP Addresses
foreach ($Server in $Servers){
    New-NetIPAddress -IPAddress ($StorNet1+$IP.ToString()) -InterfaceAlias "vEthernet (SMB01)" -CimSession $Server -PrefixLength 24
    New-NetIPAddress -IPAddress ($StorNet2+$IP.ToString()) -InterfaceAlias "vEthernet (SMB02)" -CimSession $Server -PrefixLength 24
    $IP++
}

#Configure VLANs
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB01 -VlanId $StorVLAN1 -Access -ManagementOS -CimSession $Servers
Set-VMNetworkAdapterVlan -VMNetworkAdapterName SMB02 -VlanId $StorVLAN2 -Access -ManagementOS -CimSession $Servers
#Restart each host vNIC adapter so that the Vlan is active.
Restart-NetAdapter "vEthernet (SMB01)" -CimSession $Servers
Restart-NetAdapter "vEthernet (SMB02)" -CimSession $Servers
```

#### <a name="verify-vlan-ids-and-subnets"></a>Ověření ID sítě VLAN a podsítí

```powershell
$Servers = "Server1", "Server2", "Server3", "Server4"
#verify ip config
Get-NetIPAddress -CimSession $servers -InterfaceAlias vEthernet* -AddressFamily IPv4 | Sort-Object -Property PSComputername | ft pscomputername,interfacealias,ipaddress -AutoSize -GroupBy PSComputerName

#Verify that the VlanID is set
Get-VMNetworkAdapterVlan -ManagementOS -CimSession $servers | Sort-Object -Property Computername | Format-Table ComputerName,AccessVlanID,ParentAdapter -AutoSize -GroupBy ComputerName
```

## <a name="step-3-prep-for-cluster-setup"></a>Krok 3: Příprava pro instalaci clusteru

Dále ověřte, že jsou servery připravené ke clusteringu. Nejprve si správnosti kontrolu, abyste měli jistotu, že servery ještě nepatří do clusteru:

Použijte `Get-ClusterNode` k zobrazení všech uzlů:

```powershell
Get-ClusterNode
```

Použijte `Get-ClusterResource` k zobrazení všech uzlů clusteru:

```powershell
Get-ClusterResource
```

Použijte `Get-ClusterNetwork` k zobrazení všech sítí s clustery:

```powershell
Get-ClusterNetwork
```

### <a name="step-31-prepare-drives"></a>Krok 3,1: Příprava jednotek

Než povolíte Prostory úložiště s přímým přístupem, zajistěte, aby byly jednotky prázdné. Spuštěním následujícího skriptu odeberte všechny staré oddíly nebo jiná data.

> [!WARNING]
> Tento skript trvale odebere všechna data z jiných jednotek, než je spouštěcí jednotka systému Azure Stack HCI.

```powershell
# Fill in these variables with your values
$ServerList = "Server1", "Server2", "Server3", "Server4"

Invoke-Command ($ServerList) {
    Update-StorageProviderCache
    Get-StoragePool | ? IsPrimordial -eq $false | Set-StoragePool -IsReadOnly:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$false -ErrorAction SilentlyContinue
    Get-StoragePool | ? IsPrimordial -eq $false | Remove-StoragePool -Confirm:$false -ErrorAction SilentlyContinue
    Get-PhysicalDisk | Reset-PhysicalDisk -ErrorAction SilentlyContinue
    Get-Disk | ? Number -ne $null | ? IsBoot -ne $true | ? IsSystem -ne $true | ? PartitionStyle -ne RAW | % {
        $_ | Set-Disk -isoffline:$false
        $_ | Set-Disk -isreadonly:$false
        $_ | Clear-Disk -RemoveData -RemoveOEM -Confirm:$false
        $_ | Set-Disk -isreadonly:$true
        $_ | Set-Disk -isoffline:$true
    }
    Get-Disk | Where Number -Ne $Null | Where IsBoot -Ne $True | Where IsSystem -Ne $True | Where PartitionStyle -Eq RAW | Group -NoElement -Property FriendlyName
} | Sort -Property PsComputerName, Count
```

### <a name="step-32-test-cluster-configuration"></a>Krok 3,2: test konfigurace clusteru

V tomto kroku se ujistíte, že uzly serveru jsou správně nakonfigurované, aby se vytvořil cluster. `Test-Cluster`Rutina se používá ke spouštění testů pro ověření, jestli je konfigurace vhodná pro fungování jako v rámci sblíženého clusteru. Následující příklad používá `-Include` parametr s konkrétní kategorií testů, které jsou zadány. Tím se zajistí, že se do ověření zahrne správné testy.

```powershell
Test-Cluster -Cluster –Node "Server1", "Server2", "Server3", "Server4" –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
```

## <a name="step-4-create-the-cluster"></a>Krok 4: Vytvoření clusteru

Nyní jste připraveni vytvořit cluster s uzly serveru, které jste ověřili v předchozích krocích.

Při vytváření clusteru se zobrazí upozornění, že `"There were issues while creating the clustered role that may prevent it from starting. For more information, view the report file below."` můžete toto upozornění bezpečně ignorovat. Důvodem je to, že pro určující disk clusteru, který vytvoříte později, nejsou k dispozici žádné disky. 

> [!NOTE]
> Pokud servery používají statické IP adresy, upravte následující příkaz tak, aby odrážel statickou IP adresu, a to tak, že přidáte následující parametr a zadáte IP adresu: `–StaticAddress <X.X.X.X>;` .

```powershell
 New-Cluster –Name "Cluster1" –Node "Server1", "Server2", "Server3", "Server4" –NoStorage
```

Je teď váš cluster vytvořený.

Po vytvoření clusteru může trvat nějakou dobu, než se název clusteru replikuje napříč vaší doménou, zejména pokud byly servery pracovní skupiny nově přidané do služby Active Directory. I když se cluster může zobrazit v centru pro správu systému Windows, nemusí být k dispozici, aby se mohl ještě připojit.

Pokud po nějaké době řešení clusteru neproběhne úspěšně, můžete ve většině případů připojit pomocí názvu jednoho z clusterovaných serverů místo názvu clusteru.

## <a name="step-5-set-up-sites-stretched-cluster"></a>Krok 5: nastavení lokalit (roztaženého clusteru)

Tato úloha platí pouze v případě, že vytváříte roztaženého clusteru mezi dvěma lokalitami.

### <a name="step-51-create-sites"></a>Krok 5,1: vytvoření webů

V níže uvedené rutině je *FaultDomain* pro web jednoduše jiný název. V tomto příkladu se jako název roztaženého clusteru používá "ClusterS1".

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site1"
```

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1" -FaultDomainType Site -Name "Site2"
```

Pomocí `Get-ClusterFaultDomain` rutiny ověřte, že byly pro cluster vytvořeny oba lokality.

```powershell
New-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-52-assign-server-nodes"></a>Krok 5,2: přiřazení uzlů serveru

V dalším kroku přiřadíme čtyři uzly serveru na příslušné lokality. V následujícím příkladu Server1 a Server2 jsou přiřazeny k Site1, zatímco Server3 a Server4 jsou přiřazeny site2.

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server1", "Server2" -Parent "Site1"
```

```powershell
Set-ClusterFaultDomain -CimSession "ClusterS1" -Name "Server3", "Server4" -Parent "Site2"
```

Pomocí `Get-ClusterFaultDomain` rutiny ověřte, zda jsou uzly ve správných lokalitách.

```powershell
Get-ClusterFaultDomain -CimSession "ClusterS1"
```

### <a name="step-53-set-a-preferred-site"></a>Krok 5,3: nastavení upřednostňované lokality

Můžete také definovat globální *preferovaný* webový server, což znamená, že zadané prostředky a skupiny musí běžet na preferovaném webu.  Toto nastavení lze definovat na úrovni webu pomocí následujícího příkazu:  

```powershell
(Get-Cluster).PreferredSite = "Site1"
```

Zadání upřednostňovaného webového serveru pro roztažené clustery přináší následující výhody:

- **Studená Start** – při studeném startu se virtuální počítače umístí do upřednostňované lokality.

- **Hlasování kvora**
  - Při použití dynamického kvora se vychází z pasivního (replikovaného) serveru jako prvního, aby se zajistilo, že preferovaná lokalita bude zachována, pokud jsou všechny ostatní věci stejné. Kromě toho se uzly serveru vyřadí z pasivní lokality jako první během reseskupení po událostech, jako jsou například chyby asymetrického připojení k síti.

  - Pokud se při rozdělení kvora dvou lokalit dá připojit ke sdílenému clusteru, automaticky se zvolí preferovaný Web. Uzly serveru v pasivní lokalitě vyřaďte z členství v clusteru. To umožňuje, aby cluster držel souběžně 50% ztrátu hlasů.

Upřednostňovanou lokalitu lze také nakonfigurovat na úrovni role clusteru nebo skupiny. V takovém případě může být pro každou skupinu virtuálních počítačů nakonfigurována jiná upřednostňovaná lokalita. To umožňuje, aby byl web aktivní a upřednostňovaný pro konkrétní virtuální počítače.

## <a name="step-6-enable-storage-spaces-direct"></a>Krok 6: povolení Prostory úložiště s přímým přístupem

Po vytvoření clusteru použijte `Enable-ClusterStorageSpacesDirect` rutinu, která umožní prostory úložiště s přímým přístupem a automaticky proveďte následující kroky:

- **Vytvořte fond úložiště:** Vytvoří fond úložiště pro cluster s názvem, jako je Cluster1 fond úložiště.

- **Vytvořit disk s historií výkonu clusteru:** Vytvoří virtuální disk s historií výkonu clusteru ve fondu úložiště.

- **Vytvořit data a svazky protokolů:** Vytvoří datový svazek a svazek protokolu ve fondu úložiště.

- **Konfigurace mezipamětí prostory úložiště s přímým přístupem:** Pokud je k dispozici více než jeden typ média (jednotky) pro Prostory úložiště s přímým přístupem, povolí to nejrychlejší jako zařízení mezipaměti (čtení a zápis ve většině případů).

- **Vytvořit vrstvy:** Vytvoří dvě úrovně jako výchozí úrovně. Jedna se nazývá Capacity (Kapacita) a druhá Performance (Výkon). Rutina provede analýzu zařízení a nakonfiguruje každou vrstvu na základě kombinace typů zařízení a požadované odolnosti.

U roztaženého clusteru `Enable-ClusterStorageSpacesDirect` provede rutina také následující akce:

- Zkontrolujte, jestli se lokality nastavily.
- Určení uzlů, ve kterých jsou umístěny lokality
- Určuje, jaké úložiště má každý uzel k dispozici.
- Zkontroluje, jestli je na každém uzlu nainstalovaná funkce replika úložiště.
- Vytvoří fond úložiště pro každou lokalitu a identifikuje ho názvem lokality.
- Vytvoří data a protokolové svazky v jednotlivých fondech úložiště – jeden pro každý web

Následující příkaz povolí Prostory úložiště s přímým přístupem. Můžete také zadat popisný název pro fond úložiště, jak je znázorněno zde:

```powershell
$session = New-CimSession -Cluster "Cluster1" | Enable-ClusterStorageSpacesDirect -PoolFriendlyName "Cluster1 Storage Pool"
```

Chcete-li zobrazit fondy úložiště, použijte tento příkaz:

```powershell
Get-StoragePool -CimSession $session
```

! Nyní jste vytvořili clustery bez jakýchkoli kostí.

## <a name="after-you-create-the-cluster"></a>Po vytvoření clusteru

Teď, když jste hotovi, stále existují některé důležité úkoly, které je potřeba provést:

- Nastavte určující cluster. Viz [Nastavení určujícího clusteru](witness.md).
- Vytvořte si svazky. Viz [vytvoření svazků](../manage/create-volumes.md).
- U roztaženého clusteru vytvořte svazky a nastavte replikaci pomocí repliky úložiště. Přečtěte si téma [vytvoření svazků a nastavení replikace pro roztažené clustery](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Další kroky

- Zaregistrujte svůj cluster v Azure. Viz [Správa registrace Azure](../manage/manage-azure-registration.md).
- Proveďte konečné ověření clusteru. Viz [ověření clusteru Azure Stack HCI](validate.md)
- Zřizování virtuálních počítačů. Přečtěte si téma [Správa virtuálních počítačů v Azure Stack HCL pomocí prostředí PowerShell](../manage/vm-powershell.md).
- Cluster můžete také vytvořit pomocí centra pro správu systému Windows. Další informace najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí centra pro správu systému Windows](create-cluster.md).
