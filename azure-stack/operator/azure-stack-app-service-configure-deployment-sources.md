---
title: Konfigurace zdrojů nasazení pro App Services v centru Azure Stack
description: Naučte se konfigurovat zdroje nasazení (Git, GitHub, BitBucket, DropBox a OneDrive) pro App Services v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: a3ca9d760969974d9ba6fd35f0907472d2271c61
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703498"
---
# <a name="configure-deployment-sources-for-app-services-on-azure-stack-hub"></a>Konfigurace zdrojů nasazení pro App Services v centru Azure Stack

App Service v centru Azure Stack podporuje nasazení na vyžádání od více poskytovatelů správy zdrojového kódu. Tato funkce umožňuje vývojářům aplikací nasadit přímo ze svých úložišť správy zdrojového kódu. Pokud chtějí uživatelé nakonfigurovat App Service pro připojení ke svým úložištím, operátor cloudu musí nejdřív nakonfigurovat integraci mezi App Service v Azure Stackovém centru a poskytovatelem správy zdrojů.  

Kromě místního Gitu jsou podporovány následující poskytovatelé správy zdrojů:

* GitHub
* BitBucket
* OneDrivu
* KLÁDACÍ

## <a name="view-deployment-sources-in-app-service-administration"></a>Zobrazení zdrojů nasazení v App Service správě

1. Přihlaste se na portál pro správu centra Azure Stack (https://adminportal.local.azurestack.external) jako správce služby.
2. Přejděte na **všechny služby** a vyberte **App Service**.
    Správce poskytovatele prostředků ![App Service][1]
3. Klikněte na **Konfigurace správy zdrojového kódu**. Můžete zobrazit seznam všech nakonfigurovaných zdrojů nasazení.
    ![App Service konfigurace správy zdrojového kódu správce prostředků][2]

## <a name="configure-github"></a>Konfigurace GitHubu

K dokončení této úlohy musíte mít účet GitHub. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Přihlaste se k GitHubu, přejděte na https://www.github.com/settings/developersa pak klikněte na **zaregistrovat novou aplikaci**.
    ![GitHub – registrace nové aplikace][3]
2. Zadejte **název aplikace**. Například **App Service v centru Azure Stack**.
3. Zadejte **adresu URL domovské stránky**. Adresa URL domovské stránky musí být adresa portálu Azure Stack hub. například https://portal.local.azurestack.external.
4. Zadejte **Popis aplikace**.
5. Zadejte **adresu URL zpětného volání autorizace**. Ve výchozím nasazení centra Azure Stack je adresa URL ve formě https://portal.local.azurestack.external/TokenAuthorize. Pokud pracujete v jiné doméně, přepněte název domény pro místní. azurestack. external.
6. Klikněte na **Registrovat aplikaci**. Zobrazí se stránka s výpisem **ID klienta** a **tajného kódu klienta** pro aplikaci.
    ![registraci aplikace dokončené na GitHubu][5]
7. Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu centra Azure Stack (https://adminportal.local.azurestack.external) jako správce služby.
8. Přejděte do části **poskytovatelé prostředků**a vyberte **správce poskytovatele prostředků App Service**.
9. Klikněte na **Konfigurace správy zdrojového kódu**.
10. Zkopírujte a vložte **ID klienta** a **tajný klíč klienta** do odpovídajících vstupních polí pro GitHub.
11. Klikněte na možnost **Uložit**.

## <a name="configure-bitbucket"></a>Konfigurace BitBucket

K dokončení této úlohy musíte mít účet BitBucket. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Přihlaste se k BitBucket a přejděte do části **integrace** v rámci svého účtu.
    ![BitBucket na řídicí panel – integrace][7]
2. V části Správa přístupu a **Přidat příjemce**klikněte na **OAuth** .
    ![BitBucket přidat příjemce OAuth][8]
3. Zadejte **jméno** příjemce. Například **App Service v centru Azure Stack**.
4. Zadejte **Popis** aplikace.
5. Zadejte **adresu URL zpětného volání**. Ve výchozím nasazení centra Azure Stack je adresa URL zpětného volání ve tvaru https://portal.local.azurestack.external/TokenAuthorize. Pokud pracujete v jiné doméně, nahraďte název domény pro azurestack. Local. Aby byla integrace BitBucket úspěšná, musí adresa URL následovat po velkých písmenech, které jsou tady uvedené.
6. Zadejte **adresu URL**. Tato adresa URL by měla být adresa URL portálu Azure Stack hub. například https://portal.local.azurestack.external.
7. Vyberte požadovaná **oprávnění** :
    - **Úložiště**: *číst*
    - **Webhooky**: *čtení a zápis*
8. Klikněte na možnost **Uložit**. V části **příjemci OAuth**se teď zobrazí tato nová aplikace spolu s klíčem a **tajným** **kódem** .
    Seznam aplikací ![BitBucket][9]
9.  Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu centra Azure Stack (https://adminportal.local.azurestack.external) jako správce služby.
10.  Přejděte do části **poskytovatelé prostředků** a vyberte **správce poskytovatele prostředků App Service**.
11. Klikněte na **Konfigurace správy zdrojového kódu**.
12. Zkopírujte a vložte tento **klíč** do vstupního pole pro **ID klienta** a **tajný** klíč do vstupního pole **tajného klíče klienta** pro Bitbucket.
13. Klikněte na možnost **Uložit**.

## <a name="configure-onedrive"></a>Konfigurace OneDrivu

Abyste mohli dokončit tuto úlohu, musíte mít účet Microsoft propojený s účtem OneDrive.  Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

> [!NOTE]
> Účty OneDrivu pro firmy se aktuálně nepodporují.

1. Přejděte na https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm a přihlaste se pomocí svého účtu Microsoft.
2. V části **Moje aplikace**klikněte na **Přidat aplikaci**.
Aplikace ![OneDrive][10]
3. Zadejte **název** nové registrace aplikace: zadejte **App Service v centru Azure Stack**a pak klikněte na **vytvořit aplikaci**.
4. Další obrazovka obsahuje seznam vlastností vaší nové aplikace. Uložte **ID aplikace** do nějakého dočasného umístění.
![vlastnosti aplikace OneDrive][11]
5. V části **tajné kódy aplikací**klikněte na **Generovat nové heslo**. Zaznamenejte si **nové vygenerované heslo**. Toto heslo je tajný kód vaší aplikace a po kliknutí na tlačítko **OK**se nedá získat.
6. V části **platformy**klikněte na **Přidat platformu**a pak vyberte **Web**.
7. Zadejte **identifikátor URI přesměrování**. Ve výchozím nasazení centra Azure Stack je identifikátor URI přesměrování ve formě https://portal.local.azurestack.external/TokenAuthorize. Pokud pracujete v jiné doméně, přepněte název domény pro azurestack. Local.
![aplikace OneDrive – přidat webovou platformu][12]
8. Přidejte **Microsoft Graph oprávnění** - **delegovaná oprávnění**.
    - **Soubory. AppFolder.**
    - **Uživatelský. Oprávnění**  
      ![oprávnění pro grafy aplikace OneDrive][13]
9. Klikněte na možnost **Uložit**.
10.  Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu centra Azure Stack (https://adminportal.local.azurestack.external) jako správce služby.
11.  Přejděte do části **poskytovatelé prostředků** a vyberte **správce poskytovatele prostředků App Service**.
12. Klikněte na **Konfigurace správy zdrojového kódu**.
13. Zkopírujte a vložte **ID aplikace** do vstupního pole **pro ID klienta** a **heslo** do vstupního pole **tajného klíče klienta** pro OneDrive.
14. Klikněte na možnost **Uložit**.

## <a name="configure-dropbox"></a>Konfigurace DropBoxu

> [!NOTE]
> K dokončení této úlohy musíte mít účet DropBox. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Vyhledejte https://www.dropbox.com/developers/apps a přihlaste se pomocí přihlašovacích údajů k účtu DropBoxu.
2. Klikněte na **vytvořit aplikaci**.

    ![Aplikace Dropboxu][14]

3. Vyberte **API Dropboxu**.
4. Nastavte úroveň přístupu na **složku aplikace**.
5. Zadejte **název** vaší aplikace.
Registrace aplikace ![Dropbox][15]
6. Klikněte na **vytvořit aplikaci**. Zobrazí se stránka s přehledem nastavení aplikace, včetně **klíče aplikace** a **tajného kódu aplikace**.
7. Ujistěte se, že je **název složky aplikace** nastavený na **App Service v centru Azure Stack**.
8. Nastavte **identifikátor URI pro přesměrování OAuth 2** a potom klikněte na **Přidat**. Ve výchozím nasazení centra Azure Stack je identifikátor URI přesměrování ve formě https://portal.local.azurestack.external/TokenAuthorize. Pokud pracujete v jiné doméně, přepněte doménu pro azurestack. Local.
![konfigurace aplikace Dropbox][16]
9.  Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu centra Azure Stack (https://adminportal.local.azurestack.external) jako správce služby.
10.  Přejděte do části **poskytovatelé prostředků** a vyberte **správce poskytovatele prostředků App Service**.
11. Klikněte na **Konfigurace správy zdrojového kódu**.
12. Zkopírujte a vložte **klíč aplikace** do vstupního pole **pro ID klienta** a **tajný klíč aplikace** do vstupního pole **tajného klíče klienta** pro Dropbox.
13. Klikněte na možnost **Uložit**.

## <a name="next-steps"></a>Další kroky

Uživatelé teď můžou používat zdroje nasazení pro věci, jako je [průběžné nasazování](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment), [místní nasazení Git](https://docs.microsoft.com/azure/app-service/deploy-local-git)a [synchronizace složek v cloudu](https://docs.microsoft.com/azure/app-service/deploy-content-sync).

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
