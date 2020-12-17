---
title: Odstraní virtuální počítač se závislostmi v centru Azure Stack.
description: Jak odstranit virtuální počítač (virtuální počítač) se závislostmi na Azure Stackovém centru
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/16/2020
ms.openlocfilehash: 63a4ed0c6f1d63b21e221713befa84b0d10c1a95
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620700"
---
# <a name="how-to-delete-a-vm-virtual-machine-with-dependencies-on-azure-stack-hub"></a>Jak odstranit virtuální počítač (virtuální počítač) se závislostmi na Azure Stackovém centru

V tomto článku najdete postup odebrání virtuálního počítače a jeho závislostí v centru Azure Stack.

Pokud virtuální počítač odeberete z centra Azure Stack, budou závislosti součástí, které jsou datové disky, rozhraní virtuální sítě a kontejnery diagnostiky, ve skupině prostředků zůstat. Tyto položky se nebudou automaticky odstraňovat spolu s diskem s operačním systémem.

## <a name="delete-a-vm-overview"></a>Přehled odstranění virtuálního počítače

Když vytváříte nový virtuální počítač, vytvoříte obvykle novou skupinu prostředků a všechny závislosti v této skupině prostředků. Pokud chcete odstranit virtuální počítač a všechny jeho závislosti, můžete odstranit skupinu prostředků. Azure Resource Manager zpracuje závislosti pro jejich úspěšné odstranění. Existují situace, kdy nelze odstranit skupinu prostředků pro odebrání virtuálního počítače. Virtuální počítač může například obsahovat prostředky, které nejsou závislé na virtuálním počítači, který chcete zachovat.

## <a name="delete-a-vm-with-dependencies"></a>Odstranění virtuálního počítače se závislostmi

### <a name="with-the-portal"></a>[Použití portálu](#tab/portal)

V případě, že nemůžete odstranit skupinu prostředků, buď nejsou závislosti ve stejné skupině prostředků, nebo existují jiné prostředky, postupujte podle následujících kroků:

1. Otevřete portál Azure Stack User Portal.

2. Vyberte **Virtuální počítače**. Najděte svůj virtuální počítač a potom vyberte počítač. otevře se okno virtuálního počítače.  
![Odstranění virtuálního počítače se závislostmi](./media/delete-vm/azure-stack-hub-delete-vm-portal.png)  

3. Poznamenejte si skupinu prostředků, která obsahuje závislosti virtuálních počítačů a virtuálních počítačů.

4. Vyberte **sítě** a poznamenejte si síťové rozhraní.

5. Vyberte **disky** a poznamenejte si disk s operačním systémem a datové disky.

6. Vraťte se do okna **virtuálního počítače** a vyberte **Odstranit**.

7. Zadáním `yes` potvrďte odstranění a vyberte **Odstranit**.

7. Vyberte **skupiny prostředků** a pak vyberte skupinu prostředků.

8. Odstraňte závislosti tak, že je vyberete ručně, a pak vyberte **Odstranit**.
    1. Zadáním `yes` potvrďte odstranění a vyberte **Odstranit**.
    2. Počkejte, až se prostředek zcela odstraní.
    3. Pak můžete odstranit další závislost.

### <a name="az-modules"></a>[AZ modules](#tab/ps-az)

V případě, že nemůžete odstranit skupinu prostředků, buď nejsou závislosti ve stejné skupině prostředků, nebo existují jiné prostředky, postupujte podle těchto kroků.

Připojte se k prostředí centra Azure Stack a pak aktualizujte následující proměnné s názvem virtuálního počítače a skupinou prostředků. Pokyny k připojení k relaci PowerShellu k Azure Stack hub najdete v tématu [připojení k Azure Stack centra pomocí PowerShellu jako uživatel](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzVM -Name $machineName -ResourceGroupName $resGroupName
```

Načte informace o virtuálním počítači a název závislosti. Ve stejné relaci spusťte následující rutiny:

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Odstraňte kontejner úložiště diagnostiky spouštění. Pokud je název počítače kratší než 9 znaků, bude nutné změnit index na délku řetězce v podřetězci při vytváření `$diagContainer` proměnné. 

Ve stejné relaci spusťte následující rutiny:

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Odeberte virtuální síťové rozhraní.

```powershell
$machine | Remove-AzNetworkInterface -Force
```

Odstraňte disk s operačním systémem.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Odeberte datové disky připojené k VIRTUÁLNÍmu počítači.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Nakonec odstraňte virtuální počítač. Spuštění rutiny trvá nějakou dobu. Součásti připojené k virtuálnímu počítači můžete auditovat tak, že zkontrolujete objekt virtuálního počítače v PowerShellu. Chcete-li zkontrolovat objekt, stačí pouze odkazovat na proměnnou, která obsahuje objekt virtuálního počítače. Zadejte `$machine`.

Pokud chcete virtuální počítač odstranit, ve stejné relaci spusťte následující rutiny:

```powershell
$machine | Remove-AzVM -Force
```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/ps-azureRM)

V případě, že nemůžete odstranit skupinu prostředků, buď nejsou závislosti ve stejné skupině prostředků, nebo existují jiné prostředky, postupujte podle těchto kroků.

Připojte se k prostředí centra Azure Stack a pak aktualizujte následující proměnné s názvem virtuálního počítače a skupinou prostředků. Pokyny k připojení k relaci PowerShellu k Azure Stack hub najdete v tématu [připojení k Azure Stack centra pomocí PowerShellu jako uživatel](azure-stack-powershell-configure-user.md).

```powershell
$machineName = 'VM_TO_DELETE'
$resGroupName = 'RESOURCE_GROUP'
$machine = Get-AzureRmVM -Name $machineName -ResourceGroupName $resGroupName
```

Načte informace o virtuálním počítači a název závislosti. Ve stejné relaci spusťte následující rutiny:

```powershell
 $azResParams = @{
 'ResourceName' = $machineName
 'ResourceType' = 'Microsoft.Compute/virtualMachines'
     'ResourceGroupName' = $resGroupName
 }
 $vmRes = Get-AzureRmResource @azResParams
 $vmId = $vmRes.Properties.VmId
```

Odstraňte kontejner úložiště diagnostiky spouštění. Pokud je název počítače kratší než 9 znaků, bude nutné změnit index na délku řetězce v podřetězci při vytváření `$diagContainer` proměnné. 

Ve stejné relaci spusťte následující rutiny:

```powershell
$container = [regex]::match($machine.DiagnosticsProfile.bootDiagnostics.storageUri, '^http[s]?://(.+?)\.').groups[1].value
$diagContainer = ('bootdiagnostics-{0}-{1}' -f $machine.Name.ToLower().Substring(0, 9), $vmId)
$containerRg = (Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $container }).ResourceGroupName
$storeParams = @{
    'ResourceGroupName' = $containerRg
    'Name' = $container }
Get-AzureRmStorageAccount @storeParams | Get-AzureStorageContainer | where { $_.Name-eq $diagContainer } | Remove-AzureStorageContainer -Force
```

Odeberte virtuální síťové rozhraní.

```powershell
$machine | Remove-AzureRmNetworkInterface -Force
```

Odstraňte disk s operačním systémem.

```powershell
$osVhdUri = $machine.StorageProfile.OSDisk.Vhd.Uri
$osDiskConName = $osVhdUri.Split('/')[-2]
$osDiskStorageAcct = Get-AzureRmStorageAccount | where { $_.StorageAccountName -eq $osVhdUri.Split('/')[2].Split('.')[0] }
$osDiskStorageAcct | Remove-AzureStorageBlob -Container $osDiskConName -Blob $osVhdUri.Split('/')[-1]
```

Odeberte datové disky připojené k VIRTUÁLNÍmu počítači.

```powershell
if ($machine.DataDiskNames.Count -gt 0)
 {
    Write-Verbose -Message 'Deleting disks...'
        foreach ($uri in $machine.StorageProfile.DataDisks.Vhd.Uri )
        {
            $dataDiskStorageAcct = Get-AzureRmStorageAccount -Name $uri.Split('/')[2].Split('.')[0]
             $dataDiskStorageAcct | Remove-AzureStorageBlob -Container $uri.Split('/')[-2] -Blob $uri.Split('/')[-1] -ea Ignore
        }
 }
```

Nakonec odstraňte virtuální počítač. Spuštění rutiny trvá nějakou dobu. Součásti připojené k virtuálnímu počítači můžete auditovat tak, že zkontrolujete objekt virtuálního počítače v PowerShellu. Chcete-li zkontrolovat objekt, stačí pouze odkazovat na proměnnou, která obsahuje objekt virtuálního počítače. Zadejte `$machine`.

Pokud chcete virtuální počítač odstranit, ve stejné relaci spusťte následující rutiny:

```powershell
$machine | Remove-AzureRmVM -Force
```
---
## <a name="next-steps"></a>Další kroky

[Funkce virtuálního počítače centra Azure Stack](azure-stack-vm-considerations.md)