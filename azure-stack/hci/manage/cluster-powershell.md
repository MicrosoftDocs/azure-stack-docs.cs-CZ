---
title: Správa clusterů Azure Stack HCI pomocí prostředí PowerShell
description: Naučte se spravovat clustery v Azure Stack HCI pomocí PowerShellu.
author: v-dasis
ms.topic: how-to
ms.date: 07/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 88f6ace707d14466de481f60133875968b8edce0
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947431"
---
# <a name="manage-azure-stack-hci-clusters-using-powershell"></a>Správa clusterů Azure Stack HCI pomocí prostředí PowerShell

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019

Prostředí Windows PowerShell můžete použít ke správě prostředků a ke konfiguraci funkcí v clusterech Azure Stack HCI.

Clustery můžete spravovat ze vzdáleného počítače, nikoli na hostitelském serveru v clusteru. Tento vzdálený počítač se nazývá počítač pro správu.

> [!NOTE]
> Pokud spouštíte příkazy prostředí PowerShell z počítače pro správu, zahrňte `-Name` `-Cluster` parametr nebo s názvem clusteru, který spravujete. Kromě toho budete muset při použití `-ComputerName` parametru pro uzel serveru zadat plně kvalifikovaný název domény (FQDN).

Kompletní referenční dokumentaci ke správě clusterů pomocí PowerShellu najdete v [referenčních informacích k FailoverCluster](/powershell/module/failoverclusters/?view=win10-ps).

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

Pomocí `Start-Cluster` rutin a `Stop-Cluster` můžete přidat nebo odebrat serverový uzel pro váš cluster. Další příklady a informace o použití naleznete v dokumentaci [Start-cluster](/powershell/module/failoverclusters/start-cluster?view=win10-ps) a [stop-cluster](/powershell/module/failoverclusters/stop-cluster?view=win10-ps) reference.

Spustí Clusterová služba na všech uzlech serveru clusteru, na kterých ještě není spuštěný:

```powershell
Start-Cluster -Name Cluster1
```

Tento příklad zastaví Clusterová služba na všech uzlech v clusteru s názvem Cluster1, čímž se zastaví všechny služby a aplikace nakonfigurované v clusteru:

```powershell
Stop-Cluster -Name Cluster1
```

## <a name="add-or-remove-a-server"></a>Přidat nebo odebrat server

Pomocí `Add-ClusterNode` rutin a `Remove-ClusterNode` můžete přidat nebo odebrat serverový uzel pro váš cluster. Další příklady a informace o použití najdete v referenční dokumentaci ke službě [Add-ClusterNode](/powershell/module/failoverclusters/add-clusternode?view=win10-ps) a [Remove-ClusterNode](/powershell/module/failoverclusters/remove-clusternode?view=win10-ps) .

Tento příklad přidá server s názvem Uzel4 do clusteru s názvem Cluster1. Nejprve zkontrolujte, zda je server spuštěn a připojen k síti s clustery.

```powershell
Add-ClusterNode -Cluster Cluster1 -Name Node4
```

Tento příklad odebere z clusteru Cluster1 uzel s názvem Uzel4:

```powershell
Remove-ClusterNode -Cluster Cluster1 -Name Node4
```

## <a name="setup-the-cluster-witness"></a>Nastavení určujícího clusteru

Pomocí `Set-ClusterQuorum` rutiny nastavte možnosti určující sdílené složky kvora pro cluster. Další příklady a informace o použití najdete v referenční dokumentaci k [set-ClusterQuorum](/powershell/module/failoverclusters/set-clusterquorum?view=win10-ps) .

Tento příklad změní konfiguraci kvora tak, aby na clusteru Cluster1 používala disk s kopií cloudu:

```powershell
Set-ClusterQuorum -Cluster Cluster1 -CloudWitness
```

Tento příklad změní konfiguraci kvora na majoritní podíl uzlů a souborů v clusteru Cluster1 pomocí prostředku disku v \\ fileserver\fsw pro určující sdílenou složku.

```powershell
Set-ClusterQuorum -Cluster Cluster1 -NodeAndFileShareMajority \\fileserver\fsw
```

## <a name="enable-storage-spaces-direct"></a>Aktivace Prostorů úložiště s přímým přístupem

Pomocí `Enable-ClusterStorageSpacesDirect` rutiny povolte prostory úložiště s přímým přístupem v clusteru. Další příklady a informace o použití najdete v referenční dokumentaci [Enable-ClusterStorageSpacesDirect](/powershell/module/failoverclusters/enable-clusterstoragespacesdirect?view=win10-ps) .

Tento příklad povoluje Prostory úložiště s přímým přístupem v Server1:

```powershell
Enable-ClusterStorageSpacesDirect -CimSession Cluster1
```

## <a name="configure-a-hyper-v-host"></a>Konfigurace hostitele Hyper-V

Pomocí `Set-VMHost` rutiny můžete nakonfigurovat různá nastavení hostitele Hyper-V, jako jsou třeba virtuální pevné disky a cesty virtuálních počítačů, migrace za provozu, migrace úložiště, ověřování, pokrývání uzlů NUMA a další. Další příklady a informace o použití najdete v referenční dokumentaci [Enable-ClusterStorageSpacesDirect](/powershell/module/hyper-v/set-vmhost?view=win10-ps) .

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

## <a name="remove-cluster-and-resources"></a>Odebrat cluster a prostředky

Pomocí `Remove-ClusterResource` rutiny odeberte jeden nebo všechny prostředky v clusteru. Další příklady a informace o použití najdete v referenční dokumentaci [Remove-ClusterResource](/powershell/module/failoverclusters/remove-clusterresource?view=win10-ps) .

> [!NOTE]
> Aby bylo možné odebrat cluster, bude nutné dočasně povolit ověřování CredSSP (Credential Security Service Provider). Další informace najdete v tématu [Enable-WSManCredSSP](/powershell/module/microsoft.wsman.management/enable-wsmancredssp?view=powershell-7).

Následující příklad odebere prostředky clusteru podle názvu v Cluster1 clusteru:

```powershell
Remove-ClusterResource -Cluster Cluster1 -Name "Cluster Disk 4"
```

Tento příklad odebere cluster Cluster1 zcela pomocí `Remove-Cluster` rutiny:

```powershell
Remove-Cluster -Cluster Cluster1
```

## <a name="next-steps"></a>Další kroky

- Po provedení změn byste měli cluster ověřit později. Další informace najdete v tématu [ověření clusteru Azure Stack HCI](../deploy/validate.md) .
- Naučte se spravovat clustery pomocí centra pro správu Windows. Přečtěte si téma [Správa clusterů v Azure Stack HCI pomocí centra pro správu systému Windows](cluster.md).
