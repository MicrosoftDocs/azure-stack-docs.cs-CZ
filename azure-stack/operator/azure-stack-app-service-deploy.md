---
title: Nasazení App Service v Azure Stack | Microsoft Docs
description: Přečtěte si, jak nasadit App Service v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
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
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 7088a001fab8e3e618da4be8107f5a5f5a22373f
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271777"
---
# <a name="deploy-app-service-in-azure-stack"></a>Nasazení App Service v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tento článek popisuje, jak nasadit App Service v Azure Stack.

> [!IMPORTANT]
> Před nasazením Azure App Service 1,7 použijte aktualizaci 1907 na integrovaný systém Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

Uživatelům můžete dát možnost vytvářet webové aplikace a aplikace API. Pokud chcete umožnit uživatelům vytvářet tyto aplikace, musíte:

- Přidejte [App Service poskytovatele prostředků](azure-stack-app-service-overview.md) do nasazení Azure Stack pomocí postupu popsaného v tomto článku.
- Až nainstalujete poskytovatele prostředků App Service, můžete ho zahrnout do nabídek a plánů. Uživatelé se pak můžou přihlásit k odběru služby a začít vytvářet aplikace.

> [!IMPORTANT]
> Před spuštěním instalačního programu poskytovatele prostředků se ujistěte, že jste postupovali podle pokynů v tématu, [než](azure-stack-app-service-before-you-get-started.md) začnete, a přečtěte si [poznámky k verzi](azure-stack-app-service-release-notes-update-seven.md) , které doprovází verzi 1,7. Čtení tohoto obsahu vám pomůže seznámit se s novými funkcemi, opravami a všemi známými problémy, které by mohly mít vliv na nasazení.

## <a name="run-the-app-service-resource-provider-installer"></a>Spuštění instalačního programu poskytovatele prostředků App Service

Instalace poskytovatele prostředků App Service trvá alespoň hodinu. Doba potřebná pro závisí na počtu instancí rolí, které nasadíte. Během nasazení spustí instalační program následující úlohy:

- Vytvořte kontejner objektů BLOB v zadaném účtu úložiště Azure Stack.
- Vytvoření zóny DNS a záznamů pro App Service.
- Zaregistrujte poskytovatele prostředků App Service.
- Zaregistrujte položky Galerie App Service.

Pokud chcete nasadit poskytovatele prostředků App Service, postupujte takto:

1. Spusťte AppService. exe jako správce z počítače, který má přístup ke koncovému bodu správy prostředků Azure pomocí správce Azure Stack.

2. Vyberte **nasadit App Service nebo upgradujte na nejnovější verzi**.

    ![Instalační služba App Service][1]

3. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a potom vyberte možnost **Další**.

4. Přečtěte si a přijměte licenční podmínky třetí strany a pak vyberte **Další**.

5. Ujistěte se, že jsou informace o konfiguraci cloudu App Service správné. Pokud jste během nasazení ASDK použili výchozí nastavení, můžete přijmout výchozí hodnoty. Pokud jste ale přizpůsobili možnosti, když jste nasadili ASDK nebo nasazujete na Azure Stack integrovaný systém, musíte upravit hodnoty v tomto okně, aby odrážely rozdíly.

   Pokud například použijete příponu domény mycloud.com, musí se koncový bod Azure Resource Manager tenanta Azure Stack změnit na správu. &lt;region&gt;. MyCloud.com. Zkontrolujte tato nastavení a kliknutím na tlačítko **Další** uložte nastavení.

   ![Instalační služba App Service][2]

6. Na další stránce Instalační služby App Service proveďte následující kroky:

    a. U **Předplatných Azure Stack**vyberte **připojit** .

   - Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení Azure Stack. Vyberte **Přihlásit se**.
   - Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte heslo a pak vyberte **Přihlásit**se.

   b. V části **předplatná Azure Stack**vyberte **výchozí předplatné poskytovatele**.

     > [!IMPORTANT]
     > App Service **musí** být nasazené do **předplatného výchozího poskytovatele**.

   c. V **Azure Stack umístění**vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na ASDK, vyberte **místní** .

    ![Instalační služba App Service][3]

7. Nyní můžete nasadit do existující virtuální sítě, kterou jste nakonfigurovali [pomocí těchto kroků](azure-stack-app-service-before-you-get-started.md#virtual-network), nebo nechat instalační program App Service vytvořit novou virtuální síť a podsítě. Virtuální síť vytvoříte pomocí následujících kroků:

   a. Vyberte **vytvořit virtuální síť s výchozím nastavením**, přijměte výchozí hodnoty a pak vyberte **Další**.

   b. Případně vyberte možnost **použít existující virtuální síť a podsítě**. Proveďte následující akce:

     - Vyberte **skupinu prostředků** , která obsahuje vaši virtuální síť.
     - Vyberte název **Virtual Network** , do kterého chcete nasadit.
     - Vyberte správné hodnoty **podsítí** pro každou z požadovaných podsítí rolí.
     - Vyberte **Další**.

   ![Instalační služba App Service][4]

8. Zadejte informace pro sdílenou složku a potom vyberte **Další**. Adresa sdílené složky musí používat plně kvalifikovaný název domény (FQDN) nebo IP adresu vašeho souborového serveru. Například \\\appservicefileserver.Local.cloudapp.azurestack.external\websites nebo \\\10.0.0.1\websites.  Pokud používáte souborový server, který je připojený k doméně, musíte zadat úplné uživatelské jméno včetně domény. Například myfileserverdomain\FileShareOwner.

   >[!NOTE]
   >Instalační program se před pokračováním pokusí otestovat připojení ke sdílení souborů. Pokud však nasazujete do existující virtuální sítě, může tento test připojení selhat. Zobrazí se upozornění a výzva k pokračování. Pokud jsou informace o sdílené složce správné, pokračujte v nasazení.

   ![Instalační služba App Service][7]

9. Na další stránce Instalační služby App Service proveďte následující kroky:

   a. V poli **ID aplikace identity** zadejte identifikátor GUID aplikace, kterou používáte pro identitu (z Azure AD).

   b. Do pole **soubor certifikátu aplikace identity** zadejte (nebo vyhledejte) umístění souboru certifikátu.

   c. Do pole **heslo aplikace identity** zadejte heslo certifikátu. Toto heslo je ten, který jste si poznamenali, když jste použili skript k vytvoření certifikátů.

   d. Do pole **Azure Resource Manager kořenový certifikát souboru** zadejte (nebo vyhledejte) umístění souboru certifikátu.

   e. Vyberte **Další**.

   ![Instalační služba App Service][9]

10. U každého ze tří polí certifikátu vyberte **Procházet** a přejděte na příslušný soubor certifikátu. Je nutné zadat heslo pro každý certifikát. Tyto certifikáty jsou ty, které jste vytvořili v [kroku vytvoření požadovaných certifikátů](azure-stack-app-service-before-you-get-started.md#get-certificates). Po zadání všech informací vyberte **Další** .

    | Box | Příklad názvu souboru certifikátu |
    | --- | --- |
    | **App Service výchozí soubor certifikátu SSL** | \_.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL pro App Service rozhraní API** | api.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL App Service vydavatele** | ftp.appservice.local.AzureStack.external.pfx |

    Pokud jste při vytváření certifikátů použili jinou příponu domény, názvy souborů certifikátu nepoužívají *místní. AzureStack. external*. Místo toho použijte vlastní informace o doméně.

    ![Instalační služba App Service][10]

11. Zadejte podrobnosti SQL Server pro instanci serveru, která se používá k hostování App Service databáze poskytovatele prostředků, a pak vyberte **Další**. Instalační program ověří vlastnosti připojení SQL.<br><br>Instalační program App Service se před pokračováním pokusí otestovat připojení k SQL Server. Pokud nasazujete do existující virtuální sítě, může tento test připojení selhat. Zobrazí se upozornění a výzva k pokračování. Pokud jsou informace o SQL Server správné, pokračujte v nasazení.

    ![Instalační služba App Service][11]

12. Zkontrolujte možnosti instance role a SKU. Výchozí hodnoty se naplní minimálním počtem instancí a minimální SKU pro každou roli v nasazení ASDK. Shrnutí požadavků na vCPU a paměť je k dispozici pro plánování nasazení. Po provedení výběru vyberte **Další**.

    >[!NOTE]
    >Při nasazení v produkčním prostředí postupujte podle pokynů v části [plánování kapacity pro Azure App Service role serveru v Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Role | Minimální instance | Minimální SKU | Poznámky |
    | --- | --- | --- | --- |
    | Kontrolér | 1 | Standard_A2 – (2 vCPU, 3584 MB) | Spravuje a udržuje stav cloudu App Service. |
    | Správa | 1 | Standard_A2 – (2 vCPU, 3584 MB) | Spravuje App Service Azure Resource Manager a koncové body rozhraní API, rozšíření portálu (správce, tenant, funkce portálu) a datovou službu. Pro zajištění podpory převzetí služeb při selhání se zvýšily Doporučené instance na 2. |
    | Vydavatel | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Publikuje obsah prostřednictvím FTP a nasazení webu. |
    | FrontEnd | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Směruje požadavky na aplikace App Service. |
    | Sdílený pracovní proces | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Hostuje webové aplikace nebo aplikace API a aplikace Azure Functions. Možná budete chtít přidat další instance. Jako operátor můžete definovat svou nabídku a zvolit libovolnou úroveň SKU. Vrstvy musí mít minimálně jeden vCPU. |

    ![Instalační služba App Service][13]

    >[!NOTE]
    >**Windows Server 2016 Core není podporovaná image platformy pro použití s Azure App Service v Azure Stack.  Nepoužívejte zkušební image pro produkční nasazení.**

13. V poli **Image platformy vyberte** nasazení virtuálního počítače s Windows serverem 2016 z imagí dostupných v poskytovateli výpočetních prostředků pro Cloud App Service. Vyberte **Další**.

14. Na další stránce Instalační služby App Service proveďte následující kroky:

     a. Zadejte uživatelské jméno a heslo správce virtuálního počítače role pracovního procesu.

     b. Zadejte uživatelské jméno a heslo správce virtuálního počítače jiné role.

     c. Vyberte **Další**.

    ![Instalační služba App Service][15]

15. Na stránce Souhrn instalační služby App Service proveďte následující kroky:

    a. Ověřte provedené volby. Chcete-li provést změny, **přejděte na předchozí tlačítka,** kde můžete přejít na předchozí stránky.

    b. Jsou-li konfigurace správné, zaškrtněte políčko.

    c. Pokud chcete spustit nasazení, vyberte **Další**.

    ![Instalační služba App Service][16]

16. Na další stránce Instalační služby App Service proveďte následující kroky:

    a. Sledujte průběh instalace. Nasazení na základě výchozích možností App Service v Azure Stack trvá přibližně 60 minut.

    b. Po úspěšném dokončení instalačního programu vyberte možnost **ukončit**.

    ![Instalační služba App Service][17]

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service RP s instancí SQL Always On, musíte do [skupiny dostupnosti přidat databáze appservice_hosting a appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

Pokud nasazujete do existující virtuální sítě a pomocí interní IP adresy se připojíte k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení. Toto pravidlo povoluje provoz protokolu SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete do skupiny zabezpečení sítě WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

- Zdroj: Any
- Rozsah zdrojových portů: *
- Cíl: IP adresy
- Rozsah cílových IP adres: Rozsah IP adres pro souborový server
- Rozsah cílových portů: 445
- Protokol: TCP
- Akce: Allow
- Priorita: 700
- Název: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Ověřit App Service při instalaci Azure Stack

1. Na portálu pro správu Azure Stack přejít na **Správa-App Service**.

2. V přehledu v části Stav zkontrolujte, že **stav** zobrazuje **všechny role jsou připravené**.

    ![Správa App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>App Service testovacích jednotek na Azure Stack

Až nasadíte a zaregistrujete poskytovatele prostředků App Service, otestujte ho a ujistěte se, že uživatelé můžou nasazovat webové aplikace a aplikace API.

>[!NOTE]
>Musíte vytvořit nabídku, která má v plánu obor názvů Microsoft. Web. Budete také potřebovat předplatné tenanta, které se přihlásí k odběru nabídky. Další informace najdete v tématu [Vytvoření nabídky](azure-stack-create-offer.md) a [Vytvoření plánu](azure-stack-create-plan.md).
>
>Abyste mohli vytvářet aplikace, které používají App Service Azure Stack, *musíte* mít předplatné tenanta. Jediné úlohy, které může správce služby dokončit na portálu pro správu, se vztahují k správě App Service poskytovatele prostředků. To zahrnuje přidání kapacity, konfiguraci zdrojů nasazení a přidání úrovní a SKU pracovního procesu.
>
>Pokud chcete vytvářet aplikace pro web, API a Azure Functions, musíte použít portál tenanta a mít předplatné tenanta.
>

Chcete-li vytvořit testovací webovou aplikaci, postupujte podle následujících kroků:

1. Na portálu Azure Stack User Portal vyberte **+ vytvořit prostředek** > **web a mobilní zařízení** > **webovou aplikaci**.

2. V části **Webová aplikace**zadejte název do **webové aplikace**.

3. V části **Skupina prostředků**vyberte **Nový**. Zadejte název **skupiny prostředků**.

4. Vyberte **App Service plán/umístění** > **vytvořit nový**.

5. V části **plán App Service**zadejte název **App Serviceho plánu**.

6. Vyberte **cenovou úroveň** > **Free-Shared** nebo **Shared-Shared** > a**Vyberte** > **OK** > **vytvořit**.

7. Na řídicím panelu se zobrazí dlaždice nové webové aplikace. Vyberte dlaždici.

8. U **webové aplikace**vyberte **Procházet** a zobrazte výchozí web pro tuto aplikaci.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Nasazení webu WordPress, DNN nebo Django (volitelné)

1. Na portálu Azure Stack tenant vyberte **+** , přejít na Azure Marketplace, nasaďte web Django a pak počkejte na dokončení nasazení. Webová platforma Django používá databázi založenou na systému souborů. Nevyžaduje žádné další poskytovatele prostředků, jako je SQL nebo MySQL.

2. Pokud jste nasadili také poskytovatele prostředků MySQL, můžete web WordPress nasadit z webu Marketplace. Po zobrazení výzvy k zadání parametrů databáze zadejte uživatelské jméno jako *Uživatel1\@Server1*s uživatelským jménem a názvem serveru podle vašeho výběru.

3. Pokud jste nasadili také poskytovatele prostředků SQL Server, můžete web DNN nasadit z webu Marketplace. Po zobrazení výzvy k zadání parametrů databáze vyberte databázi v počítači se systémem SQL Server připojeném k poskytovateli prostředků.

## <a name="next-steps"></a>Další postup

Příprava na další operace správy pro App Service v Azure Stack:

- [Plánování kapacity](azure-stack-app-service-capacity-planning.md)
- [Konfigurace zdrojů nasazení](azure-stack-app-service-configure-deployment-sources.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
