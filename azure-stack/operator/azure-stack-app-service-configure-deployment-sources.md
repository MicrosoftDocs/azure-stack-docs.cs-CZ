---
title: Konfigurace zdrojů nasazení pro App Services v Azure Stack | Microsoft Docs
description: Jak může správce služby nakonfigurovat zdroje nasazení (Git, GitHub, BitBucket, DropBox a OneDrive) pro App Services v Azure Stack
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
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 8512568c709770f736d6740d83578dee7391adff
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "66269249"
---
# <a name="configure-deployment-sources"></a>Konfigurace zdrojů nasazení

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

App Service v Azure Stack podporuje nasazení na vyžádání od více poskytovatelů správy zdrojů. Tato funkce umožňuje vývojářům aplikací nasadit přímo ze svých úložišť správy zdrojového kódu. Pokud chtějí uživatelé nakonfigurovat App Service pro připojení ke svým úložištím, operátor cloudu musí nejdřív nakonfigurovat integraci mezi App Service na Azure Stack a poskytovatelem správy zdrojů.  

Kromě místního Gitu jsou podporovány následující poskytovatelé správy zdrojů:

* GitHub
* BitBucket
* OneDrive
* KLÁDACÍ

## <a name="view-deployment-sources-in-app-service-administration"></a>Zobrazení zdrojů nasazení v App Service správě

1. Přihlaste se k portálu správce Azure Stack https://adminportal.local.azurestack.external) (jako správce služby.
2. Přejděte na **všechny služby** a vyberte **App Service**.
    ![Správce poskytovatele prostředků App Service][1]
3. Klikněte na **Konfigurace správy zdrojového kódu**. Můžete zobrazit seznam všech nakonfigurovaných zdrojů nasazení.
    ![Konfigurace správy zdrojového kódu App Service Správce poskytovatele prostředků][2]

## <a name="configure-github"></a>Konfigurace GitHubu

K dokončení této úlohy musíte mít účet GitHub. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Přihlaste se k GitHubu https://www.github.com/settings/developers, přejděte na a potom klikněte na **zaregistrovat novou aplikaci**.
    ![GitHub – registrace nové aplikace][3]
2. Zadejte **název aplikace**. například **App Service v Azure Stack**.
3. Zadejte **adresu URL domovské stránky**. Adresa URL domovské stránky musí být adresa Azure Stackho portálu. Například, https://portal.local.azurestack.external.
4. Zadejte **Popis aplikace**.
5. Zadejte **adresu URL zpětného volání autorizace**. Ve výchozím nasazení Azure Stack je adresa URL ve formátu https://portal.local.azurestack.external/TokenAuthorize. Pokud používáte jinou doménu, nahraďte název domény místní. azurestack. external.
6. Klikněte na tlačítko **zaregistrovat aplikaci**. Zobrazí se stránka s výpisem **ID klienta** a **tajného kódu klienta** pro aplikaci.
    ![Registrace aplikace dokončené na GitHubu][5]
7.  Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu Azure Stack (https://adminportal.local.azurestack.external) jako správce služby).
8.  Přejděte do části **poskytovatelé prostředků**a vyberte **správce poskytovatele prostředků App Service**.
9. Klikněte na **Konfigurace správy zdrojového kódu**.
10. Zkopírujte a vložte **ID klienta** a **tajný klíč klienta** do odpovídajících vstupních polí pro GitHub.
11. Klikněte na **Uložit**.

## <a name="configure-bitbucket"></a>Konfigurace BitBucket

K dokončení této úlohy musíte mít účet BitBucket. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Přihlaste se k BitBucket a přejděte do části **integrace** v rámci svého účtu.
    ![Řídicí panel BitBucket – integrace][7]
2. V části Správa přístupu a **Přidat příjemce**klikněte na **OAuth** .
    ![BitBucket přidat příjemce OAuth][8]
3. Zadejte **jméno** příjemce. například **App Service v Azure Stack**.
4. Zadejte **Popis** aplikace.
5. Zadejte **adresu URL zpětného volání**. Ve výchozím nasazení Azure Stack je adresa URL zpětného volání ve tvaru https://portal.local.azurestack.external/TokenAuthorize. Pokud používáte jinou doménu, nahraďte název domény pro azurestack. Local. Aby byla integrace BitBucket úspěšná, musí adresa URL následovat po velkých písmenech, které jsou tady uvedené.
6. Zadejte **adresu URL**. Tato adresa URL by měla být adresa URL Azure Stackového portálu. například https://portal.local.azurestack.external.
7. Vyberte požadovaná **oprávnění** :
    - **Úložiště**: *Čtení*
    - Webhooky: *Čtení a zápis*
8. Klikněte na **Uložit**. V části **příjemci OAuth**se teď zobrazí tato nová aplikace spolu sklíčem a tajným **kódem** .
    ![Seznam aplikací BitBucket][9]
9.  Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu Azure Stack (https://adminportal.local.azurestack.external) jako správce služby).
10.  Přejděte do části **poskytovatelé prostředků** a vyberte **správce poskytovatele prostředků App Service**.
11. Klikněte na **Konfigurace správy zdrojového kódu**.
12. Zkopírujte a vložte tento **klíč** do vstupního pole pro **ID klienta** a **tajný** klíč do vstupního pole **tajného klíče klienta** pro Bitbucket.
13. Klikněte na **Uložit**.

## <a name="configure-onedrive"></a>Konfigurace OneDrivu

Abyste mohli dokončit tuto úlohu, musíte mít účet Microsoft propojený s účtem OneDrive.  Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

> [!NOTE]
> Účty OneDrivu pro firmy se momentálně nepodporují.

1. Vyhledejte a https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm Přihlaste se pomocí svého účtu Microsoft.
2. V části **Moje aplikace**klikněte na **Přidat aplikaci**.
![Aplikace OneDrive][10]
3. Zadejte **název** nové registrace aplikace: zadejte **App Service na Azure Stack**a pak klikněte na **vytvořit aplikaci** .
4. Další obrazovka obsahuje seznam vlastností vaší nové aplikace. Uložte **ID aplikace** do nějakého dočasného umístění.
![Vlastnosti aplikace OneDrive][11]
5. V části **tajné kódy aplikací**klikněte na **Generovat nové heslo**. Zaznamenejte si **nové vygenerované heslo**. Toto je váš tajný klíč aplikace a nedá se získat po kliknutí na **OK**.
6. V části **platformy**klikněte na **Přidat platformu**a pak vyberte **Web**.
7. Zadejte **identifikátor URI přesměrování**. Ve výchozím nasazení Azure Stack je identifikátor URI přesměrování ve formátu https://portal.local.azurestack.external/TokenAuthorize. Pokud používáte jinou doménu, nahraďte název domény pro azurestack. Local.
![Aplikace OneDrive – přidat webovou platformu][12]
8. Přidání **oprávnění** - Microsoft Graph delegovaná**oprávnění**
    - **Files.ReadWrite.AppFolder**
    - **Uživatelský. Oprávnění**  
      ![Aplikace OneDrive – oprávnění grafu][13]
9. Klikněte na **Uložit**.
10.  Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu Azure Stack (https://adminportal.local.azurestack.external) jako správce služby).
11.  Přejděte do části **poskytovatelé prostředků** a vyberte **správce poskytovatele prostředků App Service**.
12. Klikněte na **Konfigurace správy zdrojového kódu**.
13. Zkopírujte a vložte **ID aplikace** do vstupního pole **pro ID klienta** a **heslo** do vstupního pole **tajného klíče klienta** pro OneDrive.
14. Klikněte na **Uložit**.

## <a name="configure-dropbox"></a>Konfigurace DropBoxu

> [!NOTE]
> K dokončení této úlohy musíte mít účet DropBox. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Vyhledejte a https://www.dropbox.com/developers/apps Přihlaste se pomocí přihlašovacích údajů k účtu Dropboxu.
2. Klikněte na **vytvořit aplikaci**.

    ![Aplikace Dropboxu][14]

3. Vyberte **API Dropboxu**.
4. Nastavte úroveň přístupu na **složku aplikace**.
5. Zadejte **název** vaší aplikace.
![Registrace aplikace Dropbox][15]
6. Klikněte na **vytvořit aplikaci**. Zobrazí se stránka s přehledem nastavení aplikace, včetně **klíče aplikace** a tajného kódu **aplikace**.
7. Ujistěte se, že je **název složky aplikace** nastavený na **App Service na Azure Stack**.
8. Nastavte **identifikátor URI pro přesměrování OAuth 2** a potom klikněte na **Přidat**. Ve výchozím nasazení Azure Stack je identifikátor URI přesměrování ve formátu https://portal.local.azurestack.external/TokenAuthorize. Pokud používáte jinou doménu, nahraďte doménu pro azurestack. Local.
![Konfigurace aplikace Dropbox][16]
9.  Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu Azure Stack (https://adminportal.local.azurestack.external) jako správce služby).
10.  Přejděte do části **poskytovatelé prostředků** a vyberte **správce poskytovatele prostředků App Service**.
11. Klikněte na **Konfigurace správy zdrojového kódu**.
12. Zkopírujte a vložte **klíč aplikace** do vstupního pole **pro ID klienta** a **tajný klíč aplikace** do vstupního pole tajného klíče **klienta** pro Dropbox.
13. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další postup

Uživatelé teď můžou používat zdroje nasazení pro věci, jako [](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment)je průběžné nasazování, [místní nasazení Git](https://docs.microsoft.com/azure/app-service/deploy-local-git)a [synchronizace složek](https://docs.microsoft.com/azure/app-service/deploy-content-sync)v cloudu.

<!--Image references-->
[1]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin.png
[2]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-source-control-configuration.png
[3]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-developer-applications.png
[4]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-populated.png
[5]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-github-register-a-new-oauth-application-complete.png
[6]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-roles-management-server-repair-all.png
[7]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-dashboard.png
[8]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer.png
[9]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-bitbucket-access-management-add-oauth-consumer-complete.png
[10]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-applications.png
[11]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-registration.png
[12]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-platform.png
[13]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Onedrive-application-graph-permissions.png
[14]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-applications.png
[15]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-registration.png
[16]: ./media/azure-stack-app-service-configure-deployment-sources/App-service-provider-admin-Dropbox-application-configuration.png
