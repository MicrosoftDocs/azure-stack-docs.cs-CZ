---
title: Konfigurace zdrojů nasazení pro App Services v centru Azure Stack
description: Naučte se konfigurovat zdroje nasazení (Git, GitHub, BitBucket, DropBox a OneDrive) pro App Services v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 03/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: f679f4a609f7be2798a664dc4a748e56f8b0d2fb
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374908"
---
# <a name="configure-deployment-sources-for-app-services-on-azure-stack-hub"></a>Konfigurace zdrojů nasazení pro App Services v centru Azure Stack

App Service v centru Azure Stack podporuje nasazení na vyžádání od více poskytovatelů správy zdrojového kódu. Tato funkce umožňuje vývojářům aplikací nasazovat přímo z úložišť správy zdrojového kódu. Pokud chtějí uživatelé nakonfigurovat App Service pro připojení ke svým úložištím, operátor cloudu musí nejdřív nakonfigurovat integraci mezi App Service v Azure Stackovém centru a poskytovatelem správy zdrojů.  

Kromě místního Gitu jsou podporovány následující poskytovatelé správy zdrojů:

* GitHub
* BitBucket
* OneDrive
* KLÁDACÍ

## <a name="view-deployment-sources-in-app-service-administration"></a>Zobrazení zdrojů nasazení v App Service správě

1. Přihlaste se k portálu správce Azure Stackového centra jako správce služby.
2. Přejděte na **všechny služby** a vyberte **App Service**.

    ![Správce poskytovatele prostředků App Service][1]

3. Vyberte **konfiguraci správy zdrojového kódu**. Můžete zobrazit seznam všech nakonfigurovaných zdrojů nasazení.

    ![Konfigurace správy zdrojového kódu App Service Správce poskytovatele prostředků][2]

## <a name="configure-github"></a>Konfigurace GitHubu

K dokončení této úlohy musíte mít účet GitHub. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Přihlaste se k GitHubu, klikněte na https://www.github.com/settings/developers a pak vyberte **zaregistrovat novou aplikaci**.

    ![GitHub – registrace nové aplikace][3]

2. Zadejte **název aplikace**. Například **App Service v centru Azure Stack**.
3. Zadejte **adresu URL domovské stránky**. Adresa URL domovské stránky musí být adresa portálu Azure Stack hub. Například, `https://portal.<region>.<FQDN>`. Další informace o plně kvalifikovaném názvu domény (FQDN) centra Azure Stack najdete v tématu [obor názvů DNS centra Azure Stack](azure-stack-integrate-dns.md#azure-stack-hub-dns-namespace).
4. Zadejte **Popis aplikace**.
5. Zadejte **adresu URL zpětného volání autorizace**. Ve výchozím nasazení centra Azure Stack je adresa URL ve formátu `https://portal.<region>.<FQDN>/TokenAuthorize` . 
6. Vyberte **Registrovat aplikaci**. Zobrazí se stránka s výpisem **ID klienta** a **tajného kódu klienta** pro aplikaci.

    ![Registrace aplikace dokončené na GitHubu][5]

7. Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu centra Azure Stack jako správce služby.
8. Přejít na **poskytovatele prostředků** a vybrat **App Service Správce poskytovatele prostředků**.
9. Vyberte **konfiguraci správy zdrojového kódu**.
10. Zkopírujte a vložte **ID klienta** a **tajný klíč klienta** do odpovídajících vstupních polí pro GitHub.
11. Vyberte **Uložit**.

## <a name="configure-bitbucket"></a>Konfigurace BitBucket

K dokončení této úlohy musíte mít účet BitBucket. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Přihlaste se k BitBucket a pokračujte v **integraci** s vaším účtem.

    ![Řídicí panel BitBucket – integrace][7]

2. V části Správa přístupu a **Přidat příjemce**vyberte **OAuth** .

    ![BitBucket přidat příjemce OAuth][8]

3. Zadejte **jméno** příjemce. Například **App Service v centru Azure Stack**.
4. Zadejte **Popis** aplikace.
5. Zadejte **adresu URL zpětného volání**. Ve výchozím nasazení centra Azure Stack je adresa URL zpětného volání ve tvaru `https://portal.<region>.<FQDN>/TokenAuthorize` . Aby byla integrace BitBucket úspěšná, musí adresa URL následovat po velkých písmenech, které jsou tady uvedené.
6. Zadejte **adresu URL**. Tato adresa URL by měla být adresa URL portálu Azure Stack hub. Například, `https://portal.<region>.<FQDN>`.
7. Vyberte požadovaná **oprávnění** :

    - **Úložiště**: *číst*
    - **Webhooky**: *čtení a zápis*

8. Vyberte **Uložit**. V části **příjemci OAuth**se teď zobrazí tato nová aplikace spolu s klíčem a **tajným** **kódem** .

    ![Seznam aplikací BitBucket][9]

9. Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu centra Azure Stack jako správce služby.
10. Přejít na **poskytovatele prostředků** a vybrat **App Service Správce poskytovatele prostředků**.
11. Vyberte **konfiguraci správy zdrojového kódu**.
12. Zkopírujte a vložte tento **klíč** do vstupního pole pro **ID klienta** a **tajný** klíč do vstupního pole **tajného klíče klienta** pro Bitbucket.
13. Vyberte **Uložit**.

## <a name="configure-onedrive"></a>Konfigurace OneDrivu

Abyste mohli dokončit tuto úlohu, musíte mít účet Microsoft propojený s účtem OneDrive. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

> [!NOTE]
> Účty OneDrivu pro firmy se aktuálně nepodporují.

1. Přejít na https://apps.dev.microsoft.com/?referrer=https%3A%2F%2Fdev.onedrive.com%2Fapp-registration.htm a přihlaste se pomocí svého účet Microsoft.
2. V části **Moje aplikace**vyberte **Přidat aplikaci**.

    ![Aplikace OneDrive][10]

3. Zadejte **název** nové registrace aplikace: zadejte **App Service v centru Azure Stack** a pak vyberte **vytvořit aplikaci**.
4. Další obrazovka obsahuje seznam vlastností vaší nové aplikace. Uložte **ID aplikace** do dočasného umístění.

    ![Vlastnosti aplikace OneDrive][11]

5. V části **tajné kódy aplikací**vyberte **Generovat nové heslo**. Zaznamenejte si **nové vygenerované heslo**. Toto heslo je tajný kód vaší aplikace a nedá se získat po výběru **OK**.
6. V části **platformy**vyberte **Přidat platformu** a pak vyberte **Web**.
7. Zadejte **identifikátor URI přesměrování**. Ve výchozím nasazení centra Azure Stack je identifikátor URI přesměrování ve formátu `https://portal.<region>.<FQDN>/TokenAuthorize` .

    ![Aplikace OneDrive – přidat webovou platformu][12]

8. Přidejte **Microsoft Graph oprávnění**  -  **delegovaná oprávnění**.

    - **Soubory. AppFolder.**
    - **Uživatel: Čtení** ![ aplikace OneDrive – oprávnění grafu][13]

9. Vyberte **Uložit**.
10. Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu centra Azure Stack jako správce služby.
11. Přejít na **poskytovatele prostředků** a vybrat **App Service Správce poskytovatele prostředků**.
12. Vyberte **konfiguraci správy zdrojového kódu**.
13. Zkopírujte a vložte **ID aplikace** do vstupního pole **pro ID klienta** a **heslo** do vstupního pole **tajného klíče klienta** pro OneDrive.
14. Vyberte **Uložit**.

## <a name="configure-dropbox"></a>Konfigurace DropBoxu

> [!NOTE]
> K dokončení této úlohy musíte mít účet DropBox. Místo osobního účtu možná budete chtít použít účet pro vaši organizaci.

1. Pokračujte https://www.dropbox.com/developers/apps a přihlaste se pomocí přihlašovacích údajů k účtu Dropboxu.
2. Vyberte **Vytvořit aplikaci**.

    ![Aplikace Dropboxu][14]

3. Vyberte **API Dropboxu**.
4. Nastavte úroveň přístupu na **složku aplikace**.
5. Zadejte **název** vaší aplikace.

    ![Registrace aplikace Dropbox][15]

6. Vyberte **vytvořit aplikaci**. Zobrazí se stránka s přehledem nastavení aplikace, včetně **klíče aplikace** a **tajného kódu aplikace**.
7. Ujistěte se, že je **název složky aplikace** nastavený na **App Service v centru Azure Stack**.
8. Nastavte **identifikátor URI pro přesměrování OAuth 2** a pak vyberte **Přidat**. Ve výchozím nasazení centra Azure Stack je identifikátor URI přesměrování ve formátu `https://portal.<region>.<FQDN>/TokenAuthorize` .

    ![Konfigurace aplikace Dropbox][16]

9. Na nové kartě nebo okně prohlížeče se přihlaste k portálu pro správu centra Azure Stack jako správce služby.
10. Přejít na **poskytovatele prostředků** a vybrat **App Service Správce poskytovatele prostředků**.
11. Vyberte **konfiguraci správy zdrojového kódu**.
12. Zkopírujte a vložte **klíč aplikace** do vstupního pole **pro ID klienta** a **tajný klíč aplikace** do vstupního pole **tajného klíče klienta** pro Dropbox.
13. Vyberte **Uložit**.

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
