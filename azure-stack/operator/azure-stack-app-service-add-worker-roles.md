---
title: Přidání pracovníků a infrastruktury v App Service Azure Stack | Microsoft Docs
description: Podrobné pokyny pro škálování Azure Stack App Services
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 43ee38c18e2831d1cb96958501cee6f77292edd0
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271668"
---
# <a name="add-workers-and-infrastructure-in-app-service-on-azure-stack"></a>Přidání pracovníků a infrastruktury v App Service Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*  

Tento dokument poskytuje pokyny, jak škálovat infrastrukturu a role pracovních procesů v App Service v Azure Stack. Pokryjeme všechny kroky potřebné k vytvoření dalších rolí pracovního procesu pro podporu aplikací libovolné velikosti.

> [!NOTE]
> Pokud vaše Azure Stack prostředí neobsahuje více než 96 GB paměti RAM, může docházet k potížím při přidávání další kapacity.

App Service v Azure Stack ve výchozím nastavení podporuje úrovně Free a Shared Worker. Chcete-li přidat další vrstvy pracovního procesu, je nutné přidat další role pracovního procesu.

Pokud si nejste jistí, co bylo nasazeno s výchozím App Service v Azure Stack instalaci, můžete si přečíst další informace v článku [App Service na Azure Stack Overview](azure-stack-app-service-overview.md).

Azure App Service v Azure Stack nasadí všechny role pomocí Virtual Machine Scale Sets a díky tomu využívají možnosti škálování této úlohy. Proto se veškeré škálování vrstev pracovních procesů provádí prostřednictvím Správce App Service.

## <a name="add-additional-workers-with-powershell"></a>Přidání dalších pracovníků do PowerShellu

1. [Nastavení prostředí pro správu Azure Stack v PowerShellu](azure-stack-powershell-configure-admin.md)

2. V tomto příkladu můžete škálovat sadu škálování:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Dokončení tohoto kroku může trvat několik hodin v závislosti na typu role a počtu instancí.
   >
   >

3. Sledujte stav nových instancí rolí ve správě App Service. Chcete-li zjistit stav jednotlivých instancí role, klikněte v seznamu na typ role.

## <a name="add-additional-workers-using-the-admin-portal"></a>Přidání dalších pracovníků pomocí portálu pro správu

1. Přihlaste se k portálu pro správu Azure Stack jako správce služby.

2. Přejděte na **App Services**.

    ![App Service na portálu pro správu Azure Stack](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Klikněte na **role**. Tady vidíte rozpis všech nasazených rolí App Service.

4. Klikněte pravým tlačítkem na řádek typu, který chcete škálovat, a pak klikněte na **škálovací sady**.

    ![Role škálovací sady App Service v portálu pro správu Azure Stack](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Klikněte na **škálování**, vyberte počet instancí, na které chcete škálovat, a pak klikněte na **Uložit**.

    ![Nastavení instancí pro horizontální navýšení kapacity v App Service rolích na portálu pro správu Azure Stack](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service v Azure Stack nyní přidá další virtuální počítače, nakonfiguruje je, nainstaluje veškerý požadovaný software a označí je po dokončení tohoto procesu jako připravené. Tento proces může trvat přibližně 80 minut.

7. Průběh připravenosti nových rolí můžete sledovat v okně role v okně **role** .

## <a name="result"></a>Výsledek

Po dokončení nasazení a připravení budou pracovní procesy uživatelům k dispozici pro nasazení svých úloh. Následující snímek obrazovky ukazuje příklad několika cenových úrovní, které jsou ve výchozím nastavení dostupné. Pokud pro konkrétní vrstvu pracovního procesu nejsou k dispozici žádní pracovní procesy, možnost výběru odpovídající cenové úrovně není k dispozici.

![Cenové úrovně pro nový plán App Service na portálu pro správu Azure Stack](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Pro horizontální navýšení kapacity správy, front-endu nebo rolí vydavatele použijte stejný postup, který vybere vhodný typ role. Řadiče nejsou nasazené jako sady škálování a proto by se měly v době instalace pro všechna produkční nasazení nasadit dvě.

### <a name="next-steps"></a>Další postup

[Konfigurace zdrojů nasazení](azure-stack-app-service-configure-deployment-sources.md)
