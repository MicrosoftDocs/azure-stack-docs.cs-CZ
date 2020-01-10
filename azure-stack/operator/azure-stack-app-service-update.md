---
title: Aktualizace Azure App Service v centru Azure Stack | Microsoft Docs
description: Naučte se, jak aktualizovat Azure App Service v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/28/2019
ms.openlocfilehash: a024e1712e6b9970438805f87c24afd1f11ec11e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816684"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Aktualizace Azure App Service v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

> [!IMPORTANT]
> Před nasazením Azure App Service 1,7 použijte aktualizaci 1904 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

V tomto článku se dozvíte, jak upgradovat [poskytovatele prostředků App Service](azure-stack-app-service-overview.md) nasazeného v prostředí centra Azure Stack připojené k Internetu.

> [!IMPORTANT]
> Před spuštěním upgradu se ujistěte, že jste už dokončili [nasazení Azure App Service v centru Azure Stack](azure-stack-app-service-deploy.md). Měli byste si také přečíst [poznámky k verzi](azure-stack-app-service-release-notes-update-seven.md) , které doprovází vydání 1,7, abyste se seznámili s novými funkcemi, opravami a všemi známými problémy, které by mohly mít vliv na nasazení.

## <a name="run-the-app-service-resource-provider-installer"></a>Spuštění instalačního programu poskytovatele prostředků App Service

Během tohoto procesu bude upgrade:

* Detekuje předchozí nasazení App Service.
* Připravte všechny balíčky aktualizací a nové verze všech knihoven OSS, které mají být nasazeny.
* Nahrajte do úložiště.
* Upgradujte všechny role App Service (řadiče, Správa, front-end, Vydavatel a role pracovního procesu).
* Aktualizujte definice sady škálování App Service.
* Aktualizuje manifest poskytovatele App Service prostředků.

> [!IMPORTANT]
> Instalační program App Service musí být spuštěný na počítači, který se může připojit ke koncovému bodu správce centra Azure Stack Azure Resource Manager.

Chcete-li upgradovat nasazení App Service v centru Azure Stack proveďte následující kroky:

1. Stáhněte [instalační program App Service](https://aka.ms/appsvcupdate7installer).

2. Spusťte AppService. exe jako správce.

    ![Instalační služba App Service][1]

3. Klikněte na **nasadit App Service nebo upgradujte na nejnovější verzi.**

4. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.

5. Přečtěte si a přijměte licenční podmínky třetí strany a klikněte na **Další**.

6. Ujistěte se, že jsou správné informace o koncovém bodu Azure Resource Manager centra Azure Stack a klienta služby Active Directory. Pokud jste během nasazení ASDK použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení centra Azure Stack, je nutné upravit hodnoty v tomto okně. Pokud například použijete příponu domény *MyCloud.com*, Azure Resource Manager koncový bod centra Azure Stack se musí změnit na *Management.region.MyCloud.com*. Po potvrzení vašich informací klikněte na **Další**.

    ![Informace o cloudu centra Azure Stack][2]

7. Na další stránce:

   1. Klikněte na tlačítko **připojit** vedle pole **Azure Stack předplatné centra** .
        * Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení centra Azure Stack. Klikněte na **Přihlásit se**.
        * Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. Například *cloudadmin\@azurestack. Local*. Zadejte heslo a klikněte na **Přihlásit**.
   2. V poli **předplatná centra Azure Stack** vyberte **výchozí předplatné poskytovatele**.
   3. V poli **Azure Stack centra umístění** vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na ASDK, vyberte **místní** .
   4. Pokud se zjistí existující nasazení App Service, naplní se skupina prostředků a účet úložiště jako nedostupné.
   5. Kliknutím na **Další** zkontrolujte souhrn upgradu.

      ![Zjistila se instalace App Service.][3]

8. Na stránce Souhrn:
   1. Ověřte provedené volby. Chcete-li provést změny, **přejděte na předchozí tlačítka,** kde můžete přejít na předchozí stránky.
   2. Jsou-li konfigurace správné, zaškrtněte políčko.
   3. Chcete-li spustit upgrade, klikněte na tlačítko **Další**.

       ![Souhrn App Serviceho upgradu][4]

9. Stránka průběh upgradu:
    1. Sledujte průběh upgradu. Doba trvání upgradu App Service v centru Azure Stack se liší v závislosti na počtu nasazených instancí rolí.
    2. Po úspěšném dokončení upgradu klikněte na tlačítko **konec**.

        ![App Service průběh upgradu][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Další kroky

Příprava na další operace správy pro App Service v centru Azure Stack:

* [Plánování další kapacity](azure-stack-app-service-capacity-planning.md)
* [Přidat další kapacitu](azure-stack-app-service-add-worker-roles.md)
