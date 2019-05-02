---
title: Aktualizace služby Azure App Service v Azure stacku | Dokumentace Microsoftu
description: Podrobné pokyny pro aktualizaci služby Azure App Service ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/18/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: anwestg
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: f451d59d11c79198a3bce3ec358769d423948d29
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64295178"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Aktualizace služby Azure App Service v Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!IMPORTANT]  
> Aktualizace 1901 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením Azure App Service 1.5.

Podle pokynů v tomto článku, můžete upgradovat [poskytovatele prostředků App Service](azure-stack-app-service-overview.md) nasazené v prostředí Azure Stack, který je připojený k Internetu.

> [!IMPORTANT]  
> Před spuštěním upgradu, ujistěte se, že jste již dokončili [nasazení služby Azure App Service na poskytovatele prostředků služby Azure Stack](azure-stack-app-service-deploy.md) a že jste si přečetli [poznámky k verzi](azure-stack-app-service-release-notes-update-five.md) dodávané verze 1.5, přečtěte si o nové funkce, opravy a známých problémech, které můžou ovlivnit nasazení.

## <a name="run-the-app-service-resource-provider-installer"></a>Spusťte instalační program zprostředkovatele prostředků App Service

Během tohoto procesu inovace bude:

* Detekovat předchozí nasazení služby App Service
* Příprava všech balíčků aktualizace a nové verze všech knihoven OSS k nasazení
* Nahrání do úložiště
* Upgradovat všechny role služby App Service (řadiče, správu front-endu, vydavatele a pracovní role)
* Aktualizovat definice škálovací sady služby App Service
* Aktualizujte Manifest poskytovatele prostředků App Service

> [!IMPORTANT]
> Instalační program služby App Service je třeba spustit na počítači, který můžete oslovit na koncový bod Azure Stack správce Azure Resource Manageru.
>
>

Při upgradu nasazení služby App Service ve službě Azure Stack, postupujte podle těchto kroků:

1. Stáhněte si [instalační program služby App Service](https://aka.ms/appsvcupdate5installer)

2. Spustit appservice.exe jako správce

    ![App Service Installer][1]

3. Klikněte na tlačítko **nasazení služby App Service nebo upgradujte na nejnovější verzi.**

4. Přečtěte si a přijměte licenční podmínky pro Software společnosti Microsoft a klikněte na **Další**.

5. Zkontrolujte a přijměte podmínky licence třetí strany a klikněte na **Další**.

6. Ujistěte se, že koncový bod Azure stacku Azure Resource Manageru a Tenanta Active Directory správnost informací. Pokud jste použili výchozí nastavení při nasazení Azure Stack Development Kit, můžete přijmout výchozí hodnoty. Pokud jste si přizpůsobili možností při nasazení Azure Stack, však musíte upravit hodnoty v tomto okně. Například, pokud používáte příponu domény *mycloud.com*, koncový bod služby Azure Stack Azure Resource Managerem musíte změnit na *management.region.mycloud.com*. Jakmile potvrdíte vaše informace, klikněte na tlačítko **Další**.

    ![Informace o cloudu Azure Stack][2]

7. Na další stránce:

   1. Klikněte na tlačítko **připojit** vedle **předplatná Azure Stack** pole.
        * Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení Azure Stack. Klikněte na tlačítko **přihlášení**.
        * Pokud používáte služby Active Directory Federation Services (AD FS), zadejte účet správce. Například *cloudadmin\@azurestack.local*. Zadejte své heslo a klikněte na tlačítko **Sign In**.
   2. V **předplatná Azure Stack** vyberte **výchozí předplatné poskytovatele**.
   3. V **lokality Azure Stack** , vyberte umístění, které odpovídá nasazujete do oblasti. Vyberte například **místní** Pokud vaše nasazení Azure Stack Development Kit.
   4. Pokud se detekuje existující nasazení služby App Service, klikněte prostředek skupiny a účet úložiště bude vyplní a šedě.
   5. Klikněte na tlačítko **Další** Zkontrolujte souhrn upgradu.

      ![Nezjistila se instalace služby App Service][3]

8. Na stránce souhrnu:
   1. Zkontrolujte provedený výběr. Chcete-li provést změny, použijte **předchozí** tlačítka najdete předchozí stránky.
   2. Pokud tyto konfigurace jsou správné, zaškrtněte políčko.
   3. Spusťte upgrade, klikněte na tlačítko **Další**.

       ![Souhrn upgradu služby App Service][4]

9. Stránky uvidíte průběh upgradu:
    1. Sledujte průběh upgradu. Trvání upgradu služby App Service ve službě Azure Stack se liší v závislosti na počtu nasazené instance role.
    2. Po úspěšném dokončení upgradu klikněte na tlačítko **ukončovací**.

        ![Průběh upgradu služby App Service][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Další postup

Můžete také vyzkoušet ostatní [platforma jako služba (PaaS) služby](azure-stack-offer-services-overview.md).

* [Poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
* [Poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)
