---
title: Nasadit službu App Service offline prostředí ve službě Azure Stack | Dokumentace Microsoftu
description: Podrobné informace o tom, jak nasadit službu App Service v odpojeném prostředí Azure Stack zabezpečené službou AD FS.
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
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1dfe1cba366d9b30c53a43724741c9a9e0f65819
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618531"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Přidání poskytovatele prostředků App Service pro odpojené prostředí Azure Stack zabezpečené službou AD FS

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!IMPORTANT]
> Aktualizace 1901 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením Azure App Service 1.5.

Podle pokynů v tomto článku, můžete nainstalovat [poskytovatele prostředků App Service](azure-stack-app-service-overview.md) do prostředí Azure Stack, která je:

- nejsou připojené k Internetu
- zabezpečené službou Active Directory Federation Services (AD FS).

> [!IMPORTANT]  
> Než spustíte instalační program zprostředkovatele prostředků, ujistěte se, že jste postupovali podle pokynů v [před zahájením práce](azure-stack-app-service-before-you-get-started.md) a čtení [poznámky k verzi](azure-stack-app-service-release-notes-update-five.md) dodávané 1.5 verzi, přečtěte si o nové Funkce, opravy a známých problémech, které můžou ovlivnit nasazení.

Chcete-li přidat poskytovatele prostředků App Service do offline nasazení Azure Stack, musíte dokončit tyto úkoly nejvyšší úrovně:

1. Dokončení [požadované kroky](azure-stack-app-service-before-you-get-started.md) (například nákupu certifikátů, což může trvat několik dnů nebo přijímat).
2. [Stáhněte a extrahujte instalační a pomocné soubory](azure-stack-app-service-before-you-get-started.md) na počítači připojeném k Internetu.
3. Vytvořte balíček pro offline instalaci.
4. Spusťte soubor appservice.exe Instalační služby.

## <a name="create-an-offline-installation-package"></a>Vytvořit balíček pro offline instalaci

Chcete-li nasadit službu App Service v odpojeném prostředí, musíte nejdřív vytvořit balíček pro offline instalaci na počítač, který je připojený k Internetu.

1. Spusťte instalační program AppService.exe na počítači, který je připojený k Internetu.

2. Klikněte na tlačítko **Upřesnit** > **vytvořit balíček pro offline instalaci**.

    ![App Service Installer][1]

3. Instalační program služby App Service vytvoří balíček pro offline instalaci a zobrazuje cestu k němu. Můžete kliknout na **otevřít složku** otevřete složku v Průzkumníku souborů.

    ![App Service Installer](media/azure-stack-app-service-deploy-offline/image02.png)

4. Zkopírujte instalační program (AppService.exe) a balíček pro offline instalaci na hostitelském počítači Azure Stack.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Dokončení offline instalace sady služby App Service ve službě Azure Stack

1. Spusťte appservice.exe jako správce na počítači, který můžete oslovit na koncový bod Azure Stack správce Azure Resource Manageru.

2. Klikněte na tlačítko **Upřesnit** > **dokončit offline instalaci**.

    ![App Service Installer][2]

3. Přejděte do umístění balíčku pro offline instalaci jste dříve vytvořili a potom klikněte na tlačítko **Další**.

    ![App Service Installer](media/azure-stack-app-service-deploy-offline/image04.png)

4. Zkontrolujte a přijměte licenční podmínky pro Software společnosti Microsoft a potom klikněte na tlačítko **Další**.

5. Zkontrolujte a přijměte podmínky licence třetí strany a klikněte na **Další**.

6. Ujistěte se, že informace o konfiguraci cloudové služby App Service je správná. Pokud jste použili výchozí nastavení při nasazení Azure Stack Development Kit, můžete přijmout výchozí hodnoty. Ale pokud jste si přizpůsobili možností při nasazení Azure Stack nebo provádíte nasazení na integrovaný systém, musíte upravit hodnoty v tomto okně tak, aby odrážely, který. Například pokud používáte mycloud.com příponu domény, koncový bod služby Azure Stack Tenanta Azure Resource Managerem musíte změnit na `management.<region>.mycloud.com`. Jakmile potvrdíte vaše informace, klikněte na tlačítko **Další**.

    ![App Service Installer][3]

7. Na další stránce:
   1. Klikněte na tlačítko **připojit** vedle **předplatná Azure Stack** pole.
      - Zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte své heslo a klikněte na tlačítko **Sign In**.
   2. V **předplatná Azure Stack** vyberte **výchozí předplatné poskytovatele**.
    
      > [!NOTE]
      > App Service můžete nasadit jenom do **výchozí předplatné poskytovatele**.
      >
    
   3. V **lokality Azure Stack** , vyberte umístění, které odpovídá nasazujete do oblasti. Vyberte například **místní** Pokud vaše nasazení Azure Stack Development Kit.
   4. Klikněte na **Další**.

      ![App Service Installer][4]

8. Teď máte možnost nasadit do existující virtuální síť nakonfigurované kroky [tady](azure-stack-app-service-before-you-get-started.md#virtual-network), nebo povolíte Instalační služby App Service k vytvoření virtuální sítě a přidružené podsítě.
   1. Vyberte **vytvořit virtuální síť s výchozími nastaveními**, přijměte výchozí hodnoty a pak klikněte na tlačítko **Další**, nebo;
   2. Vyberte **použít existující virtuální síť a podsítě**.
       1. Vyberte **skupiny prostředků** obsahující vaší virtuální sítě.
       2. Zvolte správný **virtuální sítě** název pro nasazení.
       3. Vyberte správné **podsítě** hodnoty pro každé z podsítí požadované role;
       4. Klikněte na **Další**

      ![App Service Installer][5]

9. Zadejte informace o sdílené složce a pak klikněte na tlačítko **Další**. Plně kvalifikovaný název domény nebo IP adresa souborového serveru se musí používat adresu sdílené složky. Například \\\appservicefileserver.local.cloudapp.azurestack.external\websites, nebo \\\10.0.0.1\websites.  Pokud používáte souborový server, který je k doméně, je nutné zadat úplné uživatelské jméno včetně domény, například myfileserverdomain\FileShareOwner.

    > [!NOTE]
    > Instalační program pokusí o test připojení ke sdílení souborů než budete pokračovat.  Ale pokud jste se rozhodli nasadit v existující virtuální sítě, nemusí být schopná připojit ke sdílení souborů Instalační služby a zobrazí upozornění, s dotazem, jestli chcete pokračovat.  Zkontrolujte zadané informace o sdílení souborů a pokračovat, pokud jsou správné.
    >
    >

   ![App Service Installer][8]

10. Na další stránce:
    1. V **ID aplikace Identity** zadejte identifikátor GUID pro aplikace, který používáte pro identita (z Azure AD).
    2. V **soubor certifikátu aplikace Identity** pole, zadejte (nebo vyhledejte) umístění souboru certifikátu.
    3. V **heslo certifikátu aplikace Identity** zadejte heslo pro certifikát. Toto heslo je ta, kterou jste si poznamenali při použití skript k vytvoření certifikátů.
    4. V **soubor kořenového certifikátu Azure Resource Manageru** pole, zadejte (nebo vyhledejte) umístění souboru certifikátu.
    5. Klikněte na **Další**.

    ![App Service Installer][10]

11. Pro každý ze tří polí souboru certifikátu, klikněte na tlačítko **Procházet** a potom přejděte k souboru příslušný certifikát. Musíte zadat heslo pro každý certifikát. Tyto certifikáty jsou ty, které jste vytvořili [vytvořit požadované certifikáty krok](azure-stack-app-service-before-you-get-started.md#get-certificates). Klikněte na tlačítko **Další** po zadání všech informací.

    | Pole | Příklad názvu souboru certifikátu |
    | --- | --- |
    | **Výchozí soubor certifikátu SSL služby App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL služby App Service API** | api.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL služby vydavatele aplikace** | ftp.appservice.local.AzureStack.external.pfx |

    Pokud jste použili jinou doménu přípona při vytvoření certifikátů, nepoužívejte názvy souborů certifikátů *místní. AzureStack.external*. Místo toho použijte informace o vaší vlastní doméně.

    ![App Service Installer][11]

12. Zadejte podrobnosti serveru SQL Server instance serveru použitý k hostování databáze poskytovatele prostředků App Service a potom klikněte na tlačítko **Další**. Instalační program ověří vlastnosti připojení SQL. Můžete **musí** zadejte interní IP adresa nebo plně kvalifikovaný název domény pro název serveru SQL Server.

    > [!NOTE]
    > Instalační program pokusí o test připojení k SQl serveru, než budete pokračovat.  Ale pokud jste se rozhodli nasadit v existující virtuální sítě, nemusí být schopný se připojit k serveru SQL Server instalační program a zobrazí upozornění s dotazem, jestli chcete pokračovat.  Zkontrolujte zadané informace o SQL serveru a pokračovat, pokud jsou správné.
    >
    > Ze služby Azure App Service v Azure stacku a vyšší 1.3 instalační program bude zkontrolujte, zda SQL Server databáze členství ve skupině na úrovni serveru SQL povoleno.  Pokud není, zobrazí výzva k následující výjimce:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > Odkazovat [poznámky k verzi pro služby Azure App Service v Azure stacku 1.3](azure-stack-app-service-release-notes-update-three.md) další podrobnosti.
   
    ![App Service Installer][12]

13. Seznamte se s role instance a skladovou Položku možnosti. Minimální počet instancí a minimální SKU pro každou roli v nasazení ASDK se vyplní výchozí hodnoty. Abyste mohli naplánovat nasazení poskytuje přehled požadavků na virtuální procesor a paměť. Po provedení výběru klikněte na tlačítko **Další**.

     > [!NOTE]
     > Nasazení v produkčním prostředí, postupujte podle pokynů v [kapacitní plánování role serveru služby Azure App Service ve službě Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Role | Minimální instance | Minimální SKU | Poznámky |
    | --- | --- | --- | --- |
    | Kontrolér | 1 | Standard_A2 - (2 virtuální procesory a 3584 MB) | Spravuje a udržuje stav cloudové služby App Service. |
    | Vedení | 1 | Standard_A2 - (2 virtuální procesory, 3584 MB) | Slouží ke správě koncových bodů aplikace služby Azure Resource Manageru a rozhraní API, rozšíření portálu (admin, tenanta, portálu Functions) a datové služby. Pro podporu převzetí služeb při selhání, vyšší doporučenou instance až na 2. |
    | Vydavatel | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Publikuje obsahu prostřednictvím nasazení pomocí FTP a web. |
    | FrontEnd | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Směruje žádosti do aplikace služby App Service. |
    | Sdílených pracovních serverů | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Hostitelé web nebo aplikace rozhraní API a aplikace Azure Function App. Můžete chtít přidat další instance. Jako operátor můžete definovat vaši nabídku a zvolte všechny úroveň skladové položky. Na úrovních musí mít minimálně jeden virtuální procesor. |

    ![App Service Installer][14]

    > [!NOTE]
    > **Jádru Windows serveru 2016 není image platformy podporované pro použití se službou Azure App Service ve službě Azure Stack.  Nepoužívejte vyhodnocení Image pro nasazení v produkčním prostředí.  Azure App Service ve službě Azure Stack vyžaduje, aby Microsoft.NET 3.5.1 SP1 se aktivuje na imagi použité pro nasazení.   Syndikovaný Tržiště imagí nemají povolení této funkce Windows serveru 2016, proto musíte vytvořit a použít image Windows serveru 2016 této předem.**

14. V **vyberte Image platformy** zvolte image virtuálního počítače nasazení Windows serveru 2016 z dostupných v poskytovateli výpočetních prostředků pro cloudové služby App Service. Klikněte na **Další**.

15. Na další stránce:
     1. Zadejte uživatelské jméno správce virtuálního počítače Role pracovního procesu a heslo.
     2. Zadejte uživatelské jméno správce jiné role virtuálního počítače a heslo.
     3. Klikněte na **Další**.

    ![App Service Installer][16]

16. Na stránce souhrnu:
    1. Zkontrolujte provedený výběr. Chcete-li provést změny, použijte **předchozí** tlačítka najdete předchozí stránky.
    2. Pokud tyto konfigurace jsou správné, zaškrtněte políčko.
    3. Chcete-li spustit nasazení, klikněte na tlačítko **Další**.

    ![App Service Installer][17]

17. Na další stránce:
    1. Sledujte průběh instalace. App Service ve službě Azure Stack trvá přibližně 60 minut pro nasazení na základě výběru výchozí.
    2. Jakmile se instalační program úspěšně dokončí, klikněte na tlačítko **ukončovací**.

    ![App Service Installer][18]

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]  
> Pokud jste zadali RP App Service se vždy na instanci SQL je nutné [přidat do skupiny dostupnosti databáze appservice_hosting a appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizaci databází, aby nedocházelo ke služby události převzetí služeb při selhání databáze.

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Ověření služby App Service na instalaci služby Azure Stack

1. V portálu pro správu služby Azure Stack, přejděte na **Správa – App Service**.

2. V přehledu, v oblasti stav, zkontrolujte, **stav** zobrazí **připravené jsou všechny role**.

    ![Správa služby App Service](media/azure-stack-app-service-deploy/image12.png)

> [!NOTE]
> Pokud jste se rozhodli nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, je nutné přidat odchozí pravidlo zabezpečení, povolení provozu SMB mezi podsítě pracovního procesu a souborový server.  Chcete-li to provést, přejděte na WorkersNsg v portálu pro správu a přidat odchozí pravidlo zabezpečení s následujícími vlastnostmi:
> * Zdroj: Vše
> * Zdrojový rozsah portů: *
> * Cíl: Adresy IP
> * Rozsah cílových IP adres: Rozsah IP adres pro souborový server
> * Rozsah cílových portů: 445
> * Protokol: TCP
> * Akce: Povolit
> * Priorita: 700
> * Název: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Vyzkoušejte si App Service ve službě Azure Stack

Po nasazení a zaregistrujte poskytovatele prostředků App Service, otestujte a ujistit se, že uživatelé můžou nasazovat webové aplikace a aplikace API.

> [!NOTE]
> Je potřeba vytvořit nabídku, která má obor názvů Microsoft.Web v rámci plánu. Pak musíte mít předplatné tenanta, která si předplatí v rámci této nabídky. Další informace najdete v tématu [vytvořit nabídku](azure-stack-create-offer.md) a [vytvořit plán](azure-stack-create-plan.md).
>
> Můžete *musí* předplatného tenanta k vytvoření aplikace, které používají služby App Service ve službě Azure Stack. Jedinou možností, které správce služby můžete dokončit v rámci portálu pro správu se vztahují k správy poskytovatele prostředků App Service. Mezi tyto možnosti patří zvýšení kapacity, konfigurace zdrojů nasazení a přidání vrstvy pracovních procesů a skladové položky.
>
> Od třetí technical preview k vytvoření webového rozhraní API a Azure funkce aplikace, musíte použít portál pro klienty a mít předplatné tenanta.

1. Na portálu Azure Stack tenanta, klikněte na tlačítko **+ vytvořit prostředek** > **Web + mobilní zařízení** > **webovou aplikaci**.

2. Na **webovou aplikaci** okno, zadejte název v **webovou aplikaci** pole.

3. V části **skupiny prostředků**, klikněte na tlačítko **nový**. Zadejte název **skupiny prostředků** pole.

4. Klikněte na **Plán nebo umístění služby App Service** > **Vytvořit nový**.

5. Na **plán služby App Service** okno, zadejte název v **plán služby App Service** pole.

6. Klikněte na tlačítko **cenová úroveň** > **Free Shared** nebo **Shared Shared** > **vyberte**  >   **OK** > **vytvořit**.

7. V necelou minutu, pro novou webovou aplikaci zobrazí dlaždice na řídicím panelu. Kliknutím na dlaždici.

8. Na **webovou aplikaci** okna, klikněte na tlačítko **Procházet** zobrazit výchozí web pro tuto aplikaci.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Nasazení webu WordPress, DNN nebo Django (volitelné)

1. Na portálu Azure Stack tenanta, klikněte na tlačítko **+**, přejděte na Azure Marketplace, nasazení webu Django a počkat na úspěšné dokončení. Webové platformě Django používá databázi založené na systému souborů. Nevyžaduje žádné další prostředků poskytovatele, jako je SQL nebo MySQL.

2. Pokud jste nasadili také poskytovatele prostředků MySQL, můžete nasadit web ve Wordpressu z Tržiště. Po zobrazení výzvy pro parametry databáze, zadejte uživatelské jméno jako *User1\@Server1*, uživatelské jméno a název serveru podle vašeho výběru.

3. Pokud jste nasadili také poskytovatele prostředků SQL serveru, můžete nasadit DNN webu z Tržiště. Po zobrazení výzvy pro parametry databáze, zvolte databázi v počítači se systémem SQL Server, který je připojený ke zprostředkovateli prostředků.

## <a name="next-steps"></a>Další postup

Můžete také vyzkoušet ostatní [platforma jako služba (PaaS) služby](azure-stack-offer-services-overview.md).

- [Poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
- [Poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
