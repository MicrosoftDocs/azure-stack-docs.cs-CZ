---
title: Aktualizace Azure App Service v centru Azure Stack
description: Naučte se, jak aktualizovat Azure App Service v centru Azure Stack.
author: BryanLa
ms.topic: article
ms.date: 05/05/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: ecedd49789a20788f1f1ba40e568298c569d168d
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848077"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Aktualizace Azure App Service v centru Azure Stack

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

::: zone pivot="state-connected"
V tomto článku se dozvíte, jak upgradovat [poskytovatele prostředků Azure App Service](azure-stack-app-service-overview.md) nasazeného v prostředí internetového centra Azure Stack připojené k Internetu.

> [!IMPORTANT]
> Před spuštěním upgradu je nutné dokončit [nasazení Azure App Service v centru Azure Stack](azure-stack-app-service-deploy.md). 

## <a name="run-the-azure-app-service-resource-provider-installer"></a>Spuštění instalačního programu poskytovatele prostředků Azure App Service

Během tohoto procesu bude upgrade:

* Detekuje předchozí nasazení Azure App Service.
* Připravte všechny balíčky aktualizací a nové verze všech knihoven OSS, které mají být nasazeny.
* Nahrání do úložiště.
* Upgradujte všechny role Azure App Service (řadiče, Správa, front-end, Vydavatel a role pracovního procesu).
* Aktualizujte definice sady škálování Azure App Service.
* Aktualizuje manifest poskytovatele Azure App Service prostředků.

> [!IMPORTANT]
> Instalační program Azure App Service musí být spuštěný na počítači, který se může připojit ke koncovému bodu správce centra Azure Stack Azure Resource Manager.

Chcete-li upgradovat nasazení Azure App Service v centru Azure Stack proveďte následující kroky:

1. Stáhněte [instalační program Azure App Service](https://aka.ms/appsvcupdateQ2installer).

2. Spusťte AppService. exe jako správce.

    ![Instalační služba Azure App Service][1]

3. Klikněte na **nasadit Azure App Service nebo upgradujte na nejnovější verzi.**

4. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.

5. Přečtěte si a přijměte licenční podmínky třetí strany a klikněte na **Další**.

6. Ujistěte se, že jsou správné informace o koncovém bodu Azure Resource Manager centra Azure Stack a klienta služby Active Directory. Pokud jste během nasazení ASDK použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení centra Azure Stack, je nutné upravit hodnoty v tomto okně. Pokud například použijete příponu domény *MyCloud.com*, Azure Resource Manager koncový bod centra Azure Stack se musí změnit na *Management.region.MyCloud.com*. Po potvrzení vašich informací klikněte na **Další**.

    ![Informace o cloudu centra Azure Stack][2]

7. Na další stránce:

    1. Vyberte metodu připojení, kterou chcete použít – **Credential** nebo **instanční objekt** .
        - **Přihlašovací údaj**
            - Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení centra Azure Stack. Vyberte **Connect** (Připojit).
            - Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte heslo a pak vyberte **připojit**.
        - **Instanční objekt**
            - Instanční objekt, který použijete, **musí** mít práva **vlastníka** na **předplatném výchozího zprostředkovatele** .
            - Zadejte **ID objektu služby**, **soubor certifikátu**a **heslo** a pak vyberte **připojit**.

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
::: zone-end

::: zone pivot="state-disconnected"
V tomto článku se dozvíte, jak upgradovat [poskytovatele prostředků Azure App Service](azure-stack-app-service-overview.md) nasazeného v prostředí Azure Stack hub:

* Nepřipojeno k Internetu
* zabezpečeno pomocí Active Directory Federation Services (AD FS) (AD FS).

> [!IMPORTANT]
> Před spuštěním upgradu je nutné dokončit [nasazení Azure App Service v centru Azure Stack v odpojeném prostředí](azure-stack-app-service-deploy-offline.md). 

## <a name="run-the-app-service-resource-provider-installer"></a>Spuštění instalačního programu poskytovatele prostředků App Service

Chcete-li upgradovat poskytovatele prostředků App Service v prostředí centra Azure Stack, je nutné dokončit tyto úlohy:

1. Stáhněte [instalační program Azure App Service](https://aka.ms/appsvcupdate8installer).
2. Vytvořte offline balíček s upgradem.
3. Spusťte instalační program App Service (AppService. exe) a dokončete upgrade.

Během tohoto procesu bude upgrade:

* Zjistit předchozí nasazení App Service
* Nahrát do úložiště
* Upgrade všech rolí App Service (řadiče, Správa, front-end, Vydavatel a role pracovního procesu)
* Aktualizovat definice sady škálování App Service
* Aktualizovat manifest poskytovatele App Service prostředků

## <a name="create-an-offline-upgrade-package"></a>Vytvoření offline balíčku pro upgrade

Chcete-li upgradovat App Service v odpojeném prostředí, musíte nejprve vytvořit balíček offline upgradu na počítači, který je připojen k Internetu.

1. Spusťte AppService. exe jako správce.

    ![Instalační služba Azure App Service][11]

2. Kliknout na **Upřesnit** > **vytvořit offline balíček**

    ![Rozšířené instalační služby Azure App Service][12]

3. Instalační služba Azure App Service vytvoří balíček s upgradem offline a zobrazí cestu k němu.  Kliknutím na **Otevřít složku** můžete otevřít složku v Průzkumníku souborů.

4. Zkopírujte instalační program (AppService. exe) a instalační balíček offline na počítač, který má připojení k vašemu centru Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>Dokončení upgradu App Service v centru Azure Stack

> [!IMPORTANT]
> Instalační program Azure App Service musí být spuštěný na počítači, který se může připojit ke koncovému bodu správce centra Azure Stack Azure Resource Manager.

1. Spusťte AppService. exe jako správce.

    ![Instalační služba Azure App Service][11]

2. Klikněte na **Upřesnit** > **Dokončit offline instalaci nebo upgrade**.

    ![Rozšířené instalační služby Azure App Service][12]

3. Přejděte do umístění offline balíčku pro upgrade, který jste dříve vytvořili, a pak klikněte na **Další**.

4. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.

5. Přečtěte si a přijměte licenční podmínky třetí strany a klikněte na **Další**.

6. Ujistěte se, že jsou správné informace o klientovi služby Azure Stack centrum Azure Resource Manager a službě Active Directory. Pokud jste během nasazení Azure Stack Development Kit použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení centra Azure Stack, je nutné upravit hodnoty v tomto okně. Pokud například použijete příponu domény *MyCloud.com*, Azure Resource Manager koncový bod centra Azure Stack se musí změnit na *Management.region.MyCloud.com*. Po potvrzení vašich informací klikněte na tlačítko **Další**.

    ![Informace o cloudu centra Azure Stack][13]

7. Na další stránce:

   1. Vyberte metodu připojení, kterou chcete použít – **Credential** nebo **instanční objekt** .
        - **Přihlašovací údaj**
            - Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení centra Azure Stack. Vyberte **Connect** (Připojit).
            - Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte heslo a pak vyberte **připojit**.
        - **Instanční objekt**
            - Instanční objekt, který použijete, **musí** mít práva **vlastníka** na **předplatném výchozího zprostředkovatele** .
            - Zadejte **ID objektu služby**, **soubor certifikátu**a **heslo** a pak vyberte **připojit**.

   1. V části **předplatná centra Azure Stack**vyberte **výchozí předplatné poskytovatele**.  Azure App Service v centru Azure Stack **musí** být nasazené v **předplatném výchozího poskytovatele**.

   1. V **umístění centra Azure Stack**vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na ASDK, vyberte **místní** .
   
   1. Pokud se zjistí existující nasazení App Service, naplní se skupina prostředků a účet úložiště a zobrazí se šedě.

      ![Zjistila se instalace Azure App Service.][14]
8. Na stránce Souhrn:
   1. Ověřte provedené volby. Chcete-li provést změny, **přejděte na předchozí tlačítka,** kde můžete přejít na předchozí stránky.
   2. Jsou-li konfigurace správné, zaškrtněte políčko.
   3. Chcete-li spustit upgrade, klikněte na tlačítko **Další**.

       ![Souhrn Azure App Serviceho upgradu][15]

9. Stránka průběh upgradu:
    1. Sledujte průběh upgradu. Doba trvání upgradu App Service v centru Azure Stack se liší v závislosti na počtu nasazených instancí rolí.
    2. Po úspěšném dokončení upgradu klikněte na tlačítko **konec**.

        ![Azure App Service průběh upgradu][16]
::: zone-end

## <a name="next-steps"></a>Další kroky

Příprava na další operace správy pro Azure App Service v centru Azure Stack:

* [Plánování další kapacity](azure-stack-app-service-capacity-planning.md)
* [Přidat další kapacitu](azure-stack-app-service-add-worker-roles.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

[11]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[12]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[13]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[14]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[15]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[16]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png