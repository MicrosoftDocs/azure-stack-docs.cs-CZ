---
title: Aktualizovat Azure App Service offline | Microsoft Docs
description: Podrobné pokyny pro aktualizaci Azure App Service v centru Azure Stack offline
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.openlocfilehash: 5b0e2fab05f0f025955725d5a3d6d95b84cc446b
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880120"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack-hub"></a>Offline aktualizace Azure App Service v centru Azure Stack

> [!IMPORTANT]
> Před nasazením Azure App Service 1,7 1907 nainstalujte do integrovaného systému Azure Stack hub nebo novější verzi Azure Stack Development Kit.

Podle pokynů v tomto článku můžete upgradovat [poskytovatele App Serviceho prostředku](azure-stack-app-service-overview.md) nasazeného v prostředí Azure Stack hub:

* Nepřipojeno k Internetu
* Zabezpečeno pomocí Active Directory Federation Services (AD FS) (AD FS).

> [!IMPORTANT]
> Před spuštěním upgradu se ujistěte, že jste už dokončili [nasazení Azure App Service v poskytovateli prostředků služby Azure Stack hub](azure-stack-app-service-deploy-offline.md) a že jste si přečetli poznámky k [verzi](azure-stack-app-service-release-notes-update-seven.md), ke které doprovází vydání 1,7, abyste se seznámili s novými funkcemi, opravami a všemi známými problémy, které by mohly mít vliv na nasazení.

## <a name="run-the-app-service-resource-provider-installer"></a>Spuštění instalačního programu poskytovatele prostředků App Service

Chcete-li upgradovat poskytovatele prostředků App Service v prostředí centra Azure Stack, je nutné dokončit tyto úlohy:

1. Stáhněte [instalační program App Service](https://aka.ms/appsvcupdate7installer).
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

    ![Instalační služba App Service][1]

2. Kliknutím na **upřesnit** > **vytvořit offline balíček**

    ![Rozšířené instalační služby App Service][2]

3. Instalační služba App Service vytvoří balíček s upgradem offline a zobrazí cestu k němu.  Kliknutím na **Otevřít složku** můžete otevřít složku v Průzkumníku souborů.

4. Zkopírujte instalační program (AppService. exe) a offline balíček s upgradem na hostitelský počítač centra Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>Dokončení upgradu App Service v centru Azure Stack

> [!IMPORTANT]
> Instalační program App Service musí být spuštěný na počítači, který se může připojit ke koncovému bodu správce centra Azure Stack Azure Resource Manager.
>
>

1. Spusťte AppService. exe jako správce.

    ![Instalační služba App Service][1]

2. Kliknutím na **upřesnit** > **dokončete offline instalaci nebo upgrade**.

    ![Rozšířené instalační služby App Service][2]

3. Přejděte do umístění offline balíčku pro upgrade, který jste dříve vytvořili, a pak klikněte na **Další**.

4. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.

5. Přečtěte si a přijměte licenční podmínky třetí strany a klikněte na **Další**.

6. Ujistěte se, že jsou správné informace o klientovi služby Azure Stack centrum Azure Resource Manager a službě Active Directory. Pokud jste během nasazení Azure Stack Development Kit použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení centra Azure Stack, je nutné upravit hodnoty v tomto okně. Pokud například použijete příponu domény *MyCloud.com*, Azure Resource Manager koncový bod centra Azure Stack se musí změnit na *Management.region.MyCloud.com*. Po potvrzení vašich informací klikněte na tlačítko **Další**.

    ![Informace o cloudu centra Azure Stack][3]

7. Na další stránce:

   1. Klikněte na tlačítko **připojit** vedle pole **Azure Stack předplatné centra** .
      * Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení centra Azure Stack. Klikněte na **Přihlásit se**.
      * Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. Příklad: _cloudadmin@azurestack.local_ . Zadejte heslo a klikněte na **Přihlásit**.
   2. V poli **předplatná centra Azure Stack** vyberte **výchozí předplatné poskytovatele**.
   3. V poli **Azure Stack centra umístění** vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na Azure Stack Development Kit, vyberte možnost **místní** .
   4. Pokud se zjistí existující nasazení App Service, naplní se skupina prostředků a účet úložiště a zobrazí se šedě.
   5. Kliknutím na **Další** zkontrolujte souhrn upgradu.

      ![Zjistila se instalace App Service.][4]

8. Na stránce Souhrn:
   1. Ověřte provedené volby. Chcete-li provést změny, **přejděte na předchozí tlačítka,** kde můžete přejít na předchozí stránky.
   2. Jsou-li konfigurace správné, zaškrtněte políčko.
   3. Chcete-li spustit upgrade, klikněte na tlačítko **Další**.

       ![Souhrn App Serviceho upgradu][5]

9. Stránka průběh upgradu:
    1. Sledujte průběh upgradu. Doba trvání upgradu App Service v centru Azure Stack se liší v závislosti na počtu nasazených instancí rolí.
    2. Po úspěšném dokončení upgradu klikněte na tlačítko **konec**.

        ![App Service průběh upgradu][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Další kroky

Příprava na další operace správy pro App Service v centru Azure Stack

* [Plánování další kapacity](azure-stack-app-service-capacity-planning.md)
* [Přidat další kapacitu](azure-stack-app-service-add-worker-roles.md)
