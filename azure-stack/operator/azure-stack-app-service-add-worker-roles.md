---
title: Přidání pracovníků a infrastruktury v App Service v centru Azure Stack
description: Podrobné pokyny pro škálování Azure App Service v centru Azure Stack
author: bryanla
ms.topic: article
ms.date: 11/15/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 11/15/2020
ms.openlocfilehash: 3265b77fc6a26a4e43b82d0997ec3e883a29f9da
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518088"
---
# <a name="add-workers-and-infrastructure-in-azure-app-service-on-azure-stack-hub"></a>Přidání pracovních procesů a infrastruktury do služby Azure App Service ve službě Azure Stack Hub

Tento dokument poskytuje pokyny, jak škálovat infrastrukturu a role pracovních procesů v Azure App Service v centru Azure Stack. Pokryjeme všechny kroky potřebné k vytvoření dalších rolí pracovního procesu pro podporu aplikací libovolné velikosti.

> [!NOTE]
> Pokud vaše prostředí Azure Stack hub nemá více než 96 GB paměti RAM, může docházet k potížím při přidávání další kapacity.

Azure App Service v centru Azure Stack podporuje ve výchozím nastavení bezplatné a sdílené úrovně pracovních procesů. Chcete-li přidat další vrstvy pracovního procesu, je nutné přidat další role pracovního procesu.

Pokud si nejste jistí, co bylo nasazeno s výchozím Azure App Service v Azure Stack instalaci centra, můžete si přečíst další informace v části [App Service v článku Přehled centra pro Azure Stack](azure-stack-app-service-overview.md).

Azure App Service v centru Azure Stack nasadí všechny role pomocí Virtual Machine Scale Sets a díky tomu využívají možnosti škálování této úlohy. Proto se veškeré škálování vrstev pracovních procesů provádí prostřednictvím Správce App Service.

## <a name="add-additional-workers-with-powershell"></a>Přidání dalších pracovníků do PowerShellu



### <a name="az-modules"></a>[AZ modules](#tab/az)

1. [Nastavení prostředí pro správu centra Azure Stack v prostředí PowerShell](azure-stack-powershell-configure-admin.md)

2. Tento příklad slouží k horizontálnímu navýšení kapacity sady škálování.

    ```powershell
    
    ##### Scale out the AppService Role instances ######
    
    # Set context to AzureStack admin.
    Login-AzAccount -EnvironmentName AzureStackAdmin
                                                    
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"
    
    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"
    
    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  
    
    # Get current scale set
    $vmss = Get-AzVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName
    
    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
    ```    

    > [!NOTE]
    > Dokončení tohoto kroku může trvat několik hodin v závislosti na typu role a počtu instancí.

3. Sledujte stav nových instancí rolí ve správě App Service. Chcete-li zjistit stav jednotlivých instancí role, klikněte v seznamu na typ role.
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm)

1. [Nastavení prostředí pro správu centra Azure Stack v prostředí PowerShell](azure-stack-powershell-configure-admin.md)

2. Tento příklad slouží k horizontálnímu navýšení kapacity sady škálování.

    ```powershell
    
    ##### Scale out the AppService Role instances ######
    
    # Set context to AzureRMureStack admin.
    Login-AzureRMAccount -EnvironmentName AzureRMureStackAdmin
                                                    
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"
    
    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"
    
    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  
    
    # Get current scale set
    $vmss = Get-AzureRMVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName
    
    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRMVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
    ```   

    > [!NOTE]
    > Dokončení tohoto kroku může trvat několik hodin v závislosti na typu role a počtu instancí.

3. Sledujte stav nových instancí rolí ve správě App Service. Chcete-li zjistit stav jednotlivých instancí role, klikněte v seznamu na typ role.
---

## <a name="add-additional-workers-using-the-administrator-portal"></a>Přidání dalších pracovníků pomocí portálu pro správu

1. Přihlaste se k portálu správce Azure Stackového centra jako správce služby.

2. Přejděte na **App Services**.

    ![App Service na portálu Azure Stack správce centra](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klikněte na **role**. Tady vidíte rozpis všech nasazených rolí App Service.

4. Klikněte pravým tlačítkem na řádek typu, který chcete škálovat, a pak klikněte na **škálovací sady**.

    ![Role App Service škálovací sady na portálu Azure Stack správce centra](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klikněte na **škálování**, vyberte počet instancí, na které chcete škálovat, a pak klikněte na **Uložit**.

    ![Nastavení instancí, které se mají škálovat v App Service rolích v portálu Azure Stack správce centra](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Azure App Service v centru Azure Stack nyní přidá další virtuální počítače, nakonfiguruje je, nainstaluje požadovaný software a po dokončení tohoto procesu ho označí jako připravený. Tento proces může trvat přibližně 80 minut.

7. Průběh připravenosti nových rolí můžete sledovat v okně role v okně **role** .

## <a name="result"></a>Výsledek

Po dokončení nasazení a připravení budou pracovní procesy uživatelům k dispozici pro nasazení svých úloh. Následující snímek obrazovky ukazuje příklad několika cenových úrovní, které jsou ve výchozím nastavení dostupné. Pokud pro konkrétní vrstvu pracovního procesu nejsou k dispozici žádní pracovní procesy, možnost výběru odpovídající cenové úrovně není k dispozici.

![Cenové úrovně pro nový plán App Service ve Azure Stack portálu pro správu centra](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Pokud chcete škálovat role správy, front-endu nebo vydavatele na více instancí, vyberte odpovídající typ role a postupujte podle stejných kroků. Kontrolery se nenasazují jako škálovací sady a proto by se pro všechna produkční nasazení měly při instalaci nasazovat dva kontrolery.

### <a name="next-steps"></a>Další kroky

[Konfigurace zdrojů nasazení](azure-stack-app-service-configure-deployment-sources.md)
