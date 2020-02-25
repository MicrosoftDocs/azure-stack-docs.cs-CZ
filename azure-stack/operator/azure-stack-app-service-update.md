---
title: Aktualizace Azure App Service v centru Azure Stack
description: Naučte se, jak aktualizovat Azure App Service v centru Azure Stack.
author: BryanLa
ms.topic: article
ms.date: 01/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: 07cf0762a87fbf0cea490d246e6e31e003fd7275
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568601"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Aktualizace Azure App Service v centru Azure Stack

> [!IMPORTANT]
> Před nasazením Azure App Service 1,8 použijte aktualizaci 1910 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

V tomto článku se dozvíte, jak upgradovat [poskytovatele prostředků Azure App Service](azure-stack-app-service-overview.md) nasazeného v prostředí centra Azure Stack připojené k Internetu.

> [!IMPORTANT]
> Před spuštěním upgradu se ujistěte, že jste už dokončili [nasazení Azure App Service v centru Azure Stack](azure-stack-app-service-deploy.md). Měli byste si také přečíst [poznámky k verzi](azure-stack-app-service-release-notes-update-eight.md) , které doprovází vydání 1,8, abyste se seznámili s novými funkcemi, opravami a všemi známými problémy, které by mohly mít vliv na nasazení.

## <a name="run-the-azure-app-service-resource-provider-installer"></a>Spuštění instalačního programu poskytovatele prostředků Azure App Service

Během tohoto procesu bude upgrade:

* Detekuje předchozí nasazení Azure App Service.
* Připravte všechny balíčky aktualizací a nové verze všech knihoven OSS, které mají být nasazeny.
* Nahrajte do úložiště.
* Upgradujte všechny role Azure App Service (řadiče, Správa, front-end, Vydavatel a role pracovního procesu).
* Aktualizujte definice sady škálování Azure App Service.
* Aktualizuje manifest poskytovatele Azure App Service prostředků.

> [!IMPORTANT]
> Instalační program Azure App Service musí být spuštěný na počítači, který se může připojit ke koncovému bodu správce centra Azure Stack Azure Resource Manager.

Chcete-li upgradovat nasazení Azure App Service v centru Azure Stack proveďte následující kroky:

1. Stáhněte [instalační program Azure App Service](https://aka.ms/appsvcupdate8installer).

2. Spusťte AppService. exe jako správce.

    ![Instalační služba Azure App Service][1]

3. Klikněte na **nasadit Azure App Service nebo upgradujte na nejnovější verzi.**

4. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.

5. Přečtěte si a přijměte licenční podmínky třetí strany a klikněte na **Další**.

6. Ujistěte se, že jsou správné informace o koncovém bodu Azure Resource Manager centra Azure Stack a klienta služby Active Directory. Pokud jste během nasazení ASDK použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení centra Azure Stack, je nutné upravit hodnoty v tomto okně. Pokud například použijete příponu domény *MyCloud.com*, Azure Resource Manager koncový bod centra Azure Stack se musí změnit na *Management.region.MyCloud.com*. Po potvrzení vašich informací klikněte na **Další**.

    ![Informace o cloudu centra Azure Stack][2]

7. Na další stránce:

    1. Vyberte metodu připojení, kterou chcete použít – **Credential** nebo **instanční objekt** .
        - **Pověřovací**
            - Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení centra Azure Stack. Vyberte **Connect** (Připojit).
            - Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. například cloudadmin@azurestack.local. Zadejte heslo a pak vyberte **připojit**.
        - **Instanční objekt**
            - Instanční objekt, který použijete, **musí** mít práva **vlastníka** na **předplatném výchozího poskytovatele** .
            - Zadejte **ID objektu služby**, **soubor certifikátu** a **heslo** a pak vyberte **připojit**.

    1. V části **předplatná centra Azure Stack**vyberte **výchozí předplatné poskytovatele**.    Azure App Service v centru Azure Stack **musí** být nasazené v **předplatném výchozího poskytovatele**.

    1. V **umístění centra Azure Stack**vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na ASDK, vyberte **místní** .

    1. Pokud se zjistí existující nasazení Azure App Service, naplní se skupina prostředků a účet úložiště jako nedostupné.

      ![Zjistila se instalace Azure App Service.][3]

8. Na stránce Souhrn:
   1. Ověřte provedené volby. Chcete-li provést změny, **přejděte na předchozí tlačítka,** kde můžete přejít na předchozí stránky.
   2. Jsou-li konfigurace správné, zaškrtněte políčko.
   3. Chcete-li spustit upgrade, klikněte na tlačítko **Další**.

       ![Souhrn Azure App Serviceho upgradu][4]

9. Stránka průběh upgradu:
    1. Sledujte průběh upgradu. Doba trvání upgradu Azure App Service v centru Azure Stack se liší v závislosti na počtu nasazených instancí rolí.
    2. Po úspěšném dokončení upgradu klikněte na tlačítko **konec**.

        ![Azure App Service průběh upgradu][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Další kroky

Příprava na další operace správy pro Azure App Service v centru Azure Stack:

* [Plánování další kapacity](azure-stack-app-service-capacity-planning.md)
* [Přidat další kapacitu](azure-stack-app-service-add-worker-roles.md)
