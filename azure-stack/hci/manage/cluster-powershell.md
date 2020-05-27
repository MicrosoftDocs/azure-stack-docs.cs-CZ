---
title: Správa clusterů Azure Stack HCI pomocí prostředí PowerShell
description: Naučte se spravovat clustery v Azure Stack HCI pomocí PowerShellu.
author: v-dasis
ms.topic: article
ms.date: 05/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 922a6188c1529d3461288ac28ff542db6fa9a527
ms.sourcegitcommit: f2203f070c17e19ba1a41d681662bac3ab50ed12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83855682"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Správa clusterů Azure Stack HCI pomocí prostředí PowerShell

> Platí pro Windows Server 2019.

Prostředí Windows PowerShell můžete použít ke správě prostředků a ke konfiguraci funkcí v clusterech Azure Stack HCI.

Obvykle se clustery spravují ze vzdáleného počítače se systémem Windows 10, nikoli na hostitelském serveru v clusteru. Tento vzdálený počítač se nazývá počítač pro správu.

> [!NOTE]
> Pokud spouštíte příkazy prostředí PowerShell z počítače pro správu, zahrňte `-Name` `-Cluster` parametr nebo s názvem clusteru, který spravujete. Kromě toho budete muset při použití `-ComputerName` parametru pro uzel serveru zadat plně kvalifikovaný název domény (FQDN).

Kompletní referenční dokumentaci ke správě clusterů pomocí PowerShellu najdete v [referenčních informacích k FailoverCluster](https://docs.microsoft.com/powershell/module/failoverclusters/?view=win10-ps).

## <a name="using-windows-powershell"></a>Použití Windows PowerShellu

Prostředí Windows PowerShell slouží k provádění všech úkolů v tomto článku. Doporučujeme, abyste aplikaci připnout na hlavní panel pro usnadnění práce.

Pokud v relaci PowerShellu nejsou dostupné následující rutiny, možná budete muset přidat `Failover Cluster` modul pro funkci Windows PowerShellu pomocí následujícího příkazu PowerShellu: `Add-WindowsFeature RSAT-Clustering-PowerShell` .

> [!NOTE]
> Počínaje verzí Windows 10 říjen 2018 se RSAT nabízí jako sada funkcí na vyžádání přímo z Windows 10. Stačí přejít na **nastavení > aplikace > aplikace & funkce > volitelné funkce > přidat funkci > RSAT: Nástroje clusteringu s podporou převzetí služeb při selhání**a vybrat **nainstalovat**. Chcete-li zobrazit průběh instalace, klikněte na tlačítko zpět a zobrazte stav na stránce Správa volitelných funkcí. Nainstalovaná funkce bude zachována v rámci upgradu verze Windows 10.

## <a name="view-cluster-settings-and-resources"></a>Zobrazení nastavení clusteru a prostředků

Načte informace o clusteru s názvem Cluster1:

```powershell
Get-Cluster -Name Cluster1
```
Načte informace o jednom nebo více uzlech nebo serverech v Cluster1:

```powershell
Get-ClusterNode -Cluster Cluster1
```

Chcete-li zjistit, které funkce systému Windows jsou nainstalovány na uzlu clusteru, použijte `Get-WindowsFeature` rutinu. Příklad:

```powershell
Get-WindowsFeature -ComputerName Server1
```

Zobrazení síťových adaptérů a jejich vlastností, jako je název, adresy IPv4 a ID sítě VLAN:

```powershell
Get-NetAdapter -CimSession Server1 | Where Status -Eq "Up" | Sort InterfaceAlias | Format-Table Name, InterfaceDescription, Status, LinkSpeed, VLANID, MacAddress
```

Postup zobrazení virtuálních přepínačů technologie Hyper-V a způsobu seskupování fyzických síťových adaptérů:

```powershell
Get-VMSwitch -ComputerName Server1
```

Zobrazení virtuálních síťových adaptérů hostitele:

```powershell
Get-VMNetworkAdapter -ComputerName Server1
```

Pokud chcete zjistit, jestli je povolená Prostory úložiště s přímým přístupem:

```powershell
Get-CimSession -ComputerName Server1 | Get-ClusterStorageSpacesDirect
```

## <a name="start-or-stop-a-cluster"></a>Spuštění nebo zastavení clusteru

Pomocí `Start-Cluster` rutin a `Stop-Cluster` můžete přidat nebo odebrat serverový uzel pro váš cluster. Další příklady a informace o použití naleznete v dokumentaci [Start-cluster](https://docs.microsoft.com/powershell/module/failoverclusters/start-cluster?view=win10-ps) a [stop-cluster](https://docs.microsoft.com/powershell/module/failoverclusters/stop-cluster?view=win10-ps) reference.

Spustí Clusterová služba na všech uzlech serveru clusteru, na kterých ještě není spuštěný:

```powershell
Start-Cluster -Name Cluster1
```

Tento příklad zastaví Clusterová služba na všech uzlech v clusteru s názvem Cluster1, čímž se zastaví všechny služby a aplikace nakonfigurované v clusteru:

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Přidat nebo odebrat server

Pomocí `Add-ClusterNode` rutin a `Remove-ClusterNode` můžete přidat nebo odebrat serverový uzel pro váš cluster. Další příklady a informace o použití najdete v referenční dokumentaci ke službě [Add-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/add-clusternode?view=win10-ps) a [Remove-ClusterNode](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusternode?view=win10-ps) .

Tento příklad přidá server s názvem Uzel4 do clusteru s názvem Cluster1. Ujistěte se, že je server zapnutý a připojený k síti s clustery jako první.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

Tento příklad odebere z clusteru Cluster1 uzel s názvem Uzel4:

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="set-quorum-options"></a>Nastavení možností kvora

Pomocí `Set-ClusterQuorum` rutiny nastavte možnosti kvora pro cluster. Další příklady a informace o použití najdete v referenční dokumentaci k [set-ClusterQuorum](https://docs.microsoft.com/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps) .

Tento příklad změní konfiguraci kvora tak, aby na clusteru Cluster1 používala disk s kopií cloudu:

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

Tento příklad změní konfiguraci kvora na majoritní podíl uzlů a souborů v clusteru Cluster1 pomocí prostředku disku v \\ fileserver\fsw pro určující sdílenou složku.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>Aktivace Prostorů úložiště s přímým přístupem

Pomocí `Enable-ClusterStorageSpacesDirect` rutiny povolte prostory úložiště s přímým přístupem v clusteru. Další příklady a informace o použití najdete v referenční dokumentaci [Enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps) .

Tento příklad povoluje Prostory úložiště s přímým přístupem v Server1:

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Konfigurace hostitele Hyper-V

Pomocí `Set-VMHost` rutiny můžete nakonfigurovat různá nastavení hostitele Hyper-V, jako jsou třeba virtuální pevné disky a cesty virtuálních počítačů, migrace za provozu, migrace úložiště, ověřování, pokrývání uzlů NUMA a další. Další příklady a informace o použití najdete v referenční dokumentaci [Enable-ClusterStorageSpacesDirect](https://docs.microsoft.com/powershell/module/hyper-v/Set-VMHost?view=win10-ps) .

Tento příklad určuje nové výchozí umístění pro virtuální pevné disky a virtuální počítače na hostitelském serveru Server1:

```powershell
Set-VMHost -ComputerName Server1 -VirtualHardDiskPath "C:\Hyper-V\Virtual Hard Disks" -VirtualMachinePath "C:\Hyper-V\Configuration Files"
```

Tento příklad nakonfiguruje hostitelský server Server1 tak, aby umožňoval 10 souběžných migrací za provozu a migrací úložiště:

```powershell
Set-VMHost -ComputerName Server1 -MaximumVirtualMachineMigrations 10 -MaximumStorageMigrations 10
```

Tento příklad nakonfiguruje hostitelský server Server1 k ověřování příchozích migrací za provozu pomocí protokolu Kerberos:

```powershell
Set-VMHost -ComputerName Server1 -VirtualMachineMigrationAuthenticationType Kerberos
```

## <a name="validate-a-cluster"></a>Ověření clusteru

Pomocí `Test-Cluster` rutiny spusťte ověřovací testy v clusteru. Další příklady a informace o použití naleznete v referenční dokumentaci k [testovacímu clusteru](https://docs.microsoft.com/powershell/module/failoverclusters/test-cluster?view=win10-ps) .

Tento příklad spustí všechny použitelné testy pro ověření clusteru v clusteru s názvem Cluster1:

```powershell
Test-Cluster -Cluster Cluster1
```

V tomto příkladu jsou uvedeny názvy všech testů a kategorií v ověřování clusteru. Zadejte tyto názvy testů s parametry *Ignore* nebo *include* pro spuštění konkrétních testů:


```powershell
Test-Cluster -Cluster Cluster1 -List
Category                                DisplayName                             Description
--------                                -----------                             -----------
Cluster Configuration                   List Cluster Core Groups                List information about the available...
Cluster Configuration                   List Cluster Network Information        List cluster-specific network settin...
Cluster Configuration                   List Cluster Resources                  List the resources that are configur...
Cluster Configuration                   List Cluster Volumes                    List information for the volumes in ...
Cluster Configuration                   List Clustered Roles                    List information about clustered roles.
Cluster Configuration                   Validate Quorum Configuration           Validate that the current quorum con...
Cluster Configuration                   Validate Resource Status                Validate that cluster resources are ...
Cluster Configuration                   Validate Service Principal Name         Validate that a Service Principal Na...
Cluster Configuration                   Validate Volume Consistency             If any volumes are flagged as incons...
Hyper-V Configuration                   List Information About Servers Runni... List Hyper-V related information on ...
Hyper-V Configuration                   Validate Compatibility of Virtual Fi... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Memory Resource Poo... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Network Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Processor Resource ... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Hyper-V Role Installed         Validate that all the nodes have the...
Hyper-V Configuration                   Validate Hyper-V Storage Resource Po... Validate that all specified nodes sh...
Hyper-V Configuration                   Validate Matching Processor Manufact... Validate that all specified nodes sh...
Hyper-V Configuration                   List Hyper-V Virtual Machine Informa... List Hyper-V virtual machine informa...
Hyper-V Configuration                   Validate Hyper-V Integration Service... Validate that the Hyper-V integratio...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Net... Validate that all virtual machines o...
Hyper-V Configuration                   Validate Hyper-V Virtual Machine Sto... Validate that all virtual machines o...
Inventory                               List Fibre Channel Host Bus Adapters    List Fibre Channel host bus adapters...
Inventory                               List iSCSI Host Bus Adapters            List iSCSI host bus adapters on each...
Inventory                               List SAS Host Bus Adapters              List Serial Attached SCSI (SAS) host...
Inventory                               List BIOS Information                   List BIOS information from each node...
Inventory                               List Environment Variables              List environment variables set on ea...
Inventory                               List Memory Information                 List memory information for each node.
Inventory                               List Operating System Information       List information about the operating...
Inventory                               List Plug and Play Devices              List Plug and Play devices on each n...
Inventory                               List Running Processes                  List the running processes on each n...
Inventory                               List Services Information               List information about the services ...
Inventory                               List Software Updates                   List software updates that have been...
Inventory                               List System Drivers                     List the system drivers on each node.
Inventory                               List System Information                 List system information such as comp...
Inventory                               List Unsigned Drivers                   List the unsigned drivers on each node.
Network                                 List Network Binding Order              List the order in which networks are...
Network                                 Validate Cluster Network Configuration  Validate the cluster networks that w...
Network                                 Validate IP Configuration               Validate that IP addresses are uniqu...
Network                                 Validate Multiple Subnet Properties     For clusters using multiple subnets,...
Network                                 Validate Network Communication          Validate that servers can communicat...
Network                                 Validate Windows Firewall Configuration Validate that the Windows Firewall i...
Storage                                 List Disks                              List all disks visible to one or mor...
Storage                                 List Potential Cluster Disks            List disks that will be validated fo...
Storage                                 Validate CSV Network Bindings           Validate that network bindings requi...
Storage                                 Validate CSV Settings                   Validate that settings and configura...
Storage                                 Validate Disk Access Latency            Validate acceptable latency for disk...
Storage                                 Validate Disk Arbitration               Validate that a node that owns a dis...
Storage                                 Validate Disk Failover                  Validate that a disk can fail over s...
Storage                                 Validate File System                    Validate that the file system on dis...
Storage                                 Validate Microsoft MPIO-based disks     Validate that disks that use Microso...
Storage                                 Validate Multiple Arbitration           Validate that in a multiple-node arb...
Storage                                 Validate SCSI device Vital Product D... Validate uniqueness of inquiry data ...
Storage                                 Validate SCSI-3 Persistent Reservation  Validate that storage supports the S...
Storage                                 Validate Simultaneous Failover          Validate that disks can fail over si...
System Configuration                    Validate Active Directory Configuration Validate that all the nodes have the...
System Configuration                    Validate All Drivers Signed             Validate that tested servers contain...
System Configuration                    Validate Cluster Service and Driver ... Validate startup settings used by se...
System Configuration                    Validate Memory Dump Settings           Validate that none of the nodes curr...
System Configuration                    Validate Operating System Edition       Validate that all tested servers are...
System Configuration                    Validate Operating System Installati... Validate that the operating systems ...
System Configuration                    Validate Operating System Version       Validate that the operating systems ...
System Configuration                    Validate Required Services              Validate that services required for ...
System Configuration                    Validate Same Processor Architecture    Validate that all servers run as 64-...
System Configuration                    Validate Service Pack Levels            Validate that all servers with same ...
System Configuration                    Validate Software Update Levels         Validate that all tested servers hav...
System Configuration                    Validate System Drive Variable          Validate that all nodes have the sam...
```

## <a name="remove-cluster-and-resources"></a>Odebrat cluster a prostředky

Pomocí `Remove-ClusterResource` rutiny odeberte jeden nebo všechny prostředky v clusteru. Další příklady a informace o použití najdete v referenční dokumentaci [Remove-ClusterResource](https://docs.microsoft.com/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps) .

> [!NOTE]
> Aby bylo možné odebrat cluster, bude nutné dočasně povolit ověřování CredSSP (Credential Security Service Provider). Další informace najdete v tématu [Enable-WSManCredSSP](https://docs.microsoft.com/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

Následující příklad odebere prostředky clusteru podle názvu v Cluster1 clusteru:

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

Tento příklad odebere cluster Cluster1 zcela pomocí `Remove-Cluster` rutiny:

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Další kroky

Naučte se spravovat clustery pomocí centra pro správu Windows. Přečtěte si téma [Správa clusterů v Azure Stack HCI pomocí centra pro správu systému Windows](cluster.md).