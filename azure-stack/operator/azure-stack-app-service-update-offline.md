---
title: Aktualizovat Azure App Service offline | Microsoft Docs
description: Podrobné pokyny pro aktualizaci Azure App Service Azure Stack offline
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
ms.openlocfilehash: 5482d078e4edbda10806220c989909424d9fcd95
ms.sourcegitcommit: 701685f0b59e5a3d1a8d39fe477b8df701a51cd2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70159595"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Offline aktualizace Azure App Service v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

> [!IMPORTANT]
> Před nasazením Azure App Service 1,7 1907 nainstalujte do integrovaného systému Azure Stack nebo novější verzi Azure Stack Development Kit.

Podle pokynů v tomto článku můžete upgradovat [App Service poskytovatele prostředků](azure-stack-app-service-overview.md) nasazeného v Azure Stack prostředí:

* Nepřipojeno k Internetu
* zabezpečeno pomocí Active Directory Federation Services (AD FS) (AD FS).

> [!IMPORTANT]
> Před spuštěním upgradu se ujistěte, že jste už dokončili [nasazení Azure App Service na Azure Stack poskytovatele prostředků](azure-stack-app-service-deploy-offline.md) a že jste si přečetli [poznámky k verzi](azure-stack-app-service-release-notes-update-seven.md), které doprovází vydání 1,7, abyste se dozvěděli o nových funkce, opravy a všechny známé problémy, které by mohly mít vliv na nasazení.

## <a name="run-the-app-service-resource-provider-installer"></a>Spuštění instalačního programu poskytovatele prostředků App Service

Chcete-li upgradovat poskytovatele prostředků App Service v prostředí Azure Stack, je nutné dokončit tyto úlohy:

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

2. Kliknout na **Upřesnit** > **vytvořit offline balíček**

    ![Rozšířené instalační služby App Service][2]

3. Instalační služba App Service vytvoří balíček s upgradem offline a zobrazí cestu k němu.  Kliknutím na **Otevřít složku** můžete otevřít složku v Průzkumníku souborů.

4. Zkopírujte instalační program (AppService. exe) a offline balíček s upgradem na hostitelský počítač Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Dokončete upgrade App Service v Azure Stack

> [!IMPORTANT]
> Instalační program App Service musí být spuštěný na počítači, který se může připojit ke koncovému bodu Azure Stack správce Azure Resource Manager.
>
>

1. Spusťte AppService. exe jako správce.

    ![Instalační služba App Service][1]

2. Klikněte na **Upřesnit** > **Dokončit offline instalaci nebo upgrade**.

    ![Rozšířené instalační služby App Service][2]

3. Přejděte do umístění offline balíčku pro upgrade, který jste dříve vytvořili, a pak klikněte na **Další**.

4. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.

5. Přečtěte si a přijměte licenční podmínky třetí strany a klikněte na **Další**.

6. Ujistěte se, že jsou informace o Azure Stack Azure Resource Manager koncový bod a informace o Tenantovi služby Active Directory správné. Pokud jste během nasazení Azure Stack Development Kit použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení Azure Stack, je nutné upravit hodnoty v tomto okně. Pokud například použijete příponu domény *MyCloud.com*, musí se koncový bod Azure Resource Manager Azure Stack změnit na *Management.region.MyCloud.com*. Po potvrzení vašich informací klikněte na tlačítko **Další**.

    ![Informace o cloudu Azure Stack][3]

7. Na další stránce:

   1. Klikněte na tlačítko **připojit** vedle pole **Azure Stack odběry** .
      * Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení Azure Stack. Klikněte na **Přihlásit se**.
      * Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. Příklad: _cloudadmin@azurestack.local_ . Zadejte heslo a klikněte na **Přihlásit**.
   2. V poli **Azure Stack odběry** vyberte **výchozí předplatné poskytovatele**.
   3. V poli **Azure Stack umístění** vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na Azure Stack Development Kit, vyberte možnost **místní** .
   4. Pokud se zjistí existující nasazení App Service, naplní se skupina prostředků a účet úložiště a zobrazí se šedě.
   5. Kliknutím na **Další** zkontrolujte souhrn upgradu.

      ![Zjistila se instalace App Service.][4]

8. Na stránce Souhrn:
   1. Ověřte provedené volby. Chcete-li provést změny, přejděte na předchozí tlačítka, kde můžete přejít na předchozí stránky.
   2. Jsou-li konfigurace správné, zaškrtněte políčko.
   3. Chcete-li spustit upgrade, klikněte na tlačítko **Další**.

       ![Souhrn App Serviceho upgradu][5]

9. Stránka průběh upgradu:
    1. Sledujte průběh upgradu. Doba trvání upgradu App Service v Azure Stack se liší v závislosti na počtu nasazených instancí rolí.
    2. Po úspěšném dokončení upgradu klikněte na tlačítko **konec**.

        ![App Service průběh upgradu][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Další postup

Příprava na další operace správy pro App Service v Azure Stack

* [Plánování další kapacity](azure-stack-app-service-capacity-planning.md)
* [Přidat další kapacitu](azure-stack-app-service-add-worker-roles.md)
