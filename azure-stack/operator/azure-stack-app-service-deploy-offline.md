---
title: Nasazení App Service v offline prostředí v Azure Stack | Microsoft Docs
description: Podrobné pokyny k nasazení App Service v nepřipojeném Azure Stack prostředí zabezpečeném AD FS.
services: azure-stack
documentationcenter: ''
author: BryanLa
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
ms.openlocfilehash: a33dc4ee1d9889b1dfa817b18b2c5c97e78c8432
ms.sourcegitcommit: 701685f0b59e5a3d1a8d39fe477b8df701a51cd2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70159631"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Přidání poskytovatele prostředků App Service k odpojenému Azure Stack prostředí zabezpečeném AD FS

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

> [!IMPORTANT]
> Před nasazením Azure App Service 1,7 použijte aktualizaci 1907 pro váš Azure Stack integrovaný systém nebo nasaďte nejnovější Azure Stack vývojovou sadu.

Podle pokynů v tomto článku můžete nainstalovat [App Service poskytovatele prostředků](azure-stack-app-service-overview.md) do prostředí Azure Stack, které je:

- Nepřipojeno k Internetu
- zabezpečeno pomocí Active Directory Federation Services (AD FS) (AD FS).

> [!IMPORTANT]
> Před spuštěním instalačního programu poskytovatele prostředků se ujistěte, že jste postupovali podle pokynů v části, [než](azure-stack-app-service-before-you-get-started.md) začnete, a přečtěte si [poznámky k verzi](azure-stack-app-service-release-notes-update-seven.md) , které doprovází 1,7. informace o nových funkcích, opravách a známých problémech může mít vliv na nasazení.

Chcete-li přidat poskytovatele prostředků App Service do nasazení Azure Stack offline, je nutné dokončit tyto úlohy nejvyšší úrovně:

1. Dokončete požadované [kroky](azure-stack-app-service-before-you-get-started.md) (jako je nákup certifikátů), což může trvat několik dní.
2. [Stáhněte a extrahujte instalační a pomocné soubory](azure-stack-app-service-before-you-get-started.md) do počítače připojeného k Internetu.
3. Vytvořte instalační balíček offline.
4. Spusťte instalační soubor AppService. exe.

## <a name="create-an-offline-installation-package"></a>Vytvoření instalačního balíčku offline

Pokud chcete nasadit App Service v odpojeném prostředí, musíte nejdřív vytvořit instalační balíček offline na počítači, který je připojený k Internetu.

1. Spusťte instalační program AppService. exe na počítači, který je připojený k Internetu.

2. Klikněte na **Upřesnit** > **vytvořit balíček offline instalace**.

    ![Instalační služba App Service][1]

3. Instalační služba App Service vytvoří instalační balíček offline a zobrazí cestu k němu. Kliknutím na **Otevřít složku** můžete otevřít složku v Průzkumníku souborů.

    ![Instalační služba App Service](media/azure-stack-app-service-deploy-offline/image02.png)

4. Zkopírujte instalační program (AppService. exe) a instalační balíček offline do hostitelského počítače s Azure Stack.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Dokončete offline instalaci App Service v Azure Stack

1. Spusťte AppService. exe jako správce z počítače, který se může spojit s koncovým bodem správy prostředků Azure Azure Stack admin.

2. Klikněte na **Upřesnit** > **Dokončit offline instalaci**.

    ![Instalační služba App Service][2]

3. Přejděte do umístění instalačního balíčku offline, který jste vytvořili dříve, a potom klikněte na tlačítko **Další**.

    ![Instalační služba App Service](media/azure-stack-app-service-deploy-offline/image04.png)

4. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a klikněte na tlačítko **Další**.

5. Zkontrolujte a přijměte licenční podmínky třetí strany a klikněte na tlačítko **Další**.

6. Ujistěte se, že jsou informace o konfiguraci cloudu App Service správné. Pokud jste během nasazení Azure Stack Development Kit použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení Azure Stack nebo při nasazení v integrovaném systému, je nutné upravit hodnoty v tomto okně, aby odrážely to. Pokud například použijete příponu domény mycloud.com, musí koncový bod Azure Resource Manager tenanta Azure Stack změnit na `management.<region>.mycloud.com`. Po potvrzení vašich informací klikněte na tlačítko **Další**.

    ![Instalační služba App Service][3]

7. Na další stránce:
   1. Klikněte na tlačítko **připojit** vedle pole **Azure Stack odběry** .
      - Zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte heslo a klikněte na **Přihlásit**.
   2. V poli **Azure Stack odběry** vyberte **výchozí předplatné poskytovatele**.

      > [!NOTE]
      > App Service lze nasadit pouze do předplatného **výchozího poskytovatele**.
      >

   3. V poli **Azure Stack umístění** vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na Azure Stack Development Kit, vyberte možnost **místní** .
   4. Klikněte na **Další**.

      ![Instalační služba App Service][4]

8. Teď máte možnost nasadit existující Virtual Network, jak je nakonfigurované pomocí kroků uvedených [tady](azure-stack-app-service-before-you-get-started.md#virtual-network), nebo můžete nechat instalační program App Service, aby vytvořil Virtual Network a přidružené podsítě.
   1. Vyberte **vytvořit virtuální síť s výchozím nastavením**, přijměte výchozí hodnoty a pak klikněte na **Další**, nebo;
   2. Vyberte možnost **použít existující virtuální síť a podsítě**.
       1. Vyberte **skupinu prostředků** , která obsahuje vaše Virtual Network.
       2. Vyberte správný název **Virtual Network** , do kterého chcete nasadit.
       3. Vyberte správné hodnoty **podsítí** pro každou z požadovaných podsítí rolí.
       4. Klikněte na **Další**

      ![Instalační služba App Service][5]

9. Zadejte informace pro sdílenou složku a potom klikněte na tlačítko **Další**. Adresa sdílené složky musí používat plně kvalifikovaný název domény nebo IP adresu vašeho souborového serveru. Například \\\appservicefileserver.Local.cloudapp.azurestack.external\websites nebo \\\10.0.0.1\websites.  Pokud používáte souborový server, který je připojený k doméně, musíte zadat úplné uživatelské jméno včetně domény, například myfileserverdomain\FileShareOwner.

    > [!NOTE]
    > Instalační program se před pokračováním pokusí otestovat připojení ke sdílené složce.  Pokud jste však zvolili nasazení v existující Virtual Network, instalační program se nemusí připojit ke sdílené složce a zobrazí upozornění, zda chcete pokračovat.  Ověřte informace o sdílení souborů a pokračujte v případě, že jsou správné.
    >
    >

   ![Instalační služba App Service][8]

10. Na další stránce:
    1. V poli **ID aplikace identity** zadejte identifikátor GUID pro aplikaci, kterou používáte pro identitu (z Azure AD).
    2. Do pole **soubor certifikátu aplikace identity** zadejte (nebo vyhledejte) umístění souboru certifikátu.
    3. Do pole **heslo aplikace identity** zadejte heslo certifikátu. Toto heslo je ten, který jste si poznamenali, když jste použili skript k vytvoření certifikátů.
    4. Do pole **Azure Resource Manager kořenový certifikát souboru** zadejte (nebo vyhledejte) umístění souboru certifikátu.
    5. Klikněte na **Další**.

    ![Instalační služba App Service][10]

11. U každého ze tří polí certifikátu klikněte na **Procházet** a potom přejděte na příslušný soubor certifikátu. Je nutné zadat heslo pro každý certifikát. Tyto certifikáty jsou ty, které jste vytvořili v [kroku vytvoření požadovaných certifikátů](azure-stack-app-service-before-you-get-started.md#get-certificates). Po zadání všech informací klikněte na tlačítko **Další** .

    | Box | Příklad názvu souboru certifikátu |
    | --- | --- |
    | **App Service výchozí soubor certifikátu SSL** | \_.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL pro App Service rozhraní API** | api.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL App Service vydavatele** | ftp.appservice.local.AzureStack.external.pfx |

    Pokud jste při vytváření certifikátů použili jinou příponu domény, názvy souborů certifikátu nepoužívají *místní. AzureStack. external*. Místo toho použijte vlastní informace o doméně.

    ![Instalační služba App Service][11]

12. Zadejte podrobnosti SQL Server pro instanci serveru, která se používá pro hostování App Servicech databází poskytovatele prostředků, a potom klikněte na tlačítko **Další**. Instalační program ověří vlastnosti připojení SQL. Pro název SQL Server **musíte** zadat buď interní IP, nebo plně kvalifikovaný název domény.

    > [!NOTE]
    > Instalační program se před pokračováním pokusí otestovat připojení k systému SQl Server.  Pokud jste však zvolili nasazení v existující Virtual Network, instalační program se nemusí připojit k SQL Server a zobrazí upozornění s dotazem, zda chcete pokračovat.  Ověřte SQL Server informace a pokračujte v případě, že jsou správné.
    >
    > Z Azure App Service v Azure Stack 1,3 a vyšší instalační program zkontroluje, jestli má SQL Server na úrovni SQL Server povolené zahrnutí databáze.  V takovém případě se zobrazí výzva s následující výjimkou:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Další podrobnosti najdete v poznámkách k [verzi pro Azure App Service v Azure Stack 1,3](azure-stack-app-service-release-notes-update-three.md) .

    ![Instalační služba App Service][12]

13. Zkontrolujte možnosti instance role a SKU. Výchozí hodnoty jsou vyplněny minimálním počtem instancí a minimálními SKU pro každou roli v nasazení ASDK. Shrnutí požadavků na vCPU a paměť je k dispozici pro plánování nasazení. Po provedení výběru klikněte na tlačítko **Další**.

     > [!NOTE]
     > Při nasazení v produkčním prostředí postupujte podle pokynů v části [plánování kapacity pro Azure App Service role serveru v Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Role | Minimální instance | Minimální SKU | Poznámky |
    | --- | --- | --- | --- |
    | Kontrolér | 1 | Standard_A2 – (2 vCPU, 3584 MB) | Spravuje a udržuje stav cloudu App Service. |
    | Správa | 1 | Standard_A2 – (2 vCPU, 3584 MB) | Spravuje App Service Azure Resource Manager a koncové body rozhraní API, rozšíření portálu (správce, tenant, funkce portálu) a datovou službu. Pro zajištění podpory převzetí služeb při selhání se zvýšily Doporučené instance na 2. |
    | Vydavatel | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Publikuje obsah prostřednictvím FTP a nasazení webu. |
    | FrontEnd | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Směruje požadavky na aplikace App Service. |
    | Sdílený pracovní proces | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Hostuje webové aplikace nebo aplikace API a aplikace Azure Functions. Možná budete chtít přidat další instance. Jako operátor můžete definovat svou nabídku a zvolit libovolnou úroveň SKU. Vrstvy musí mít minimálně jeden vCPU. |

    ![Instalační služba App Service][14]

    > [!NOTE]
    > **Windows Server 2016 Core není podporovaná image platformy pro použití s Azure App Service v Azure Stack.  Nepoužívejte zkušební image pro produkční nasazení.  Azure App Service v Azure Stack vyžaduje, aby byla na imagi používané pro nasazení aktivována Microsoft.NET 3.5.1 SP1.   Na webu Marketplace byly zavedené bitové kopie systému Windows Server 2016 povoleny, proto je třeba vytvořit a použít bitovou kopii systému Windows Server 2016 s tímto předpovolenou funkcí.**

14. V poli **bitová kopie platformy** zvolte nasazení bitové kopie virtuálního počítače s Windows serverem 2016 z imagí dostupných v poskytovateli prostředků COMPUTE pro Cloud App Service. Vyberte **Další**.

15. Na další stránce:
     1. Zadejte uživatelské jméno a heslo správce virtuálního počítače role pracovního procesu.
     2. Zadejte jiné role uživatelské jméno a heslo správce virtuálního počítače.
     3. Klikněte na **Další**.

    ![Instalační služba App Service][16]

16. Na stránce Souhrn:
    1. Ověřte provedené volby. Chcete-li provést změny, přejděte na předchozí tlačítka, kde můžete přejít na předchozí stránky.
    2. Jsou-li konfigurace správné, zaškrtněte políčko.
    3. Chcete-li spustit nasazení, klikněte na tlačítko **Další**.

    ![Instalační služba App Service][17]

17. Na další stránce:
    1. Sledujte průběh instalace. Nasazení na základě výchozích možností App Service v Azure Stack trvá přibližně 60 minut.
    2. Po úspěšném dokončení instalačního programu klikněte na tlačítko **konec**.

    ![Instalační služba App Service][18]

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service RP s instancí SQL Always On, musíte [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě databáze. převzetí služeb.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem.  Na portálu pro správu vyberte WorkersNsg, skupina zabezpečení sítě a přidejte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

- Zdroj: Any
- Rozsah zdrojových portů: *
- Cíl: Adresy IP
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

> [!NOTE]
> V rámci plánu je potřeba vytvořit nabídku, která má obor názvů Microsoft. Web. Pak musíte mít předplatné tenanta, které se přihlásí k odběru této nabídky. Další informace najdete v tématu [Vytvoření nabídky](azure-stack-create-offer.md) a [Vytvoření plánu](azure-stack-create-plan.md).
>
> Abyste mohli vytvářet aplikace, které používají App Service Azure Stack, *musíte* mít předplatné tenanta. Jediné možnosti, které může správce služby dokončit v rámci portálu pro správu, se vztahují k správě App Service poskytovatele prostředků. Mezi tyto možnosti patří přidání kapacity, konfigurace zdrojů nasazení a přidání úrovní a SKU pracovního procesu.
>
> Od třetí verze Technical Preview, abyste mohli vytvářet webové, API a Azure Functions aplikace, musíte použít portál tenanta a mít předplatné tenanta.

1. Na portálu Azure Stack tenant klikněte na **+ vytvořit prostředek** > **web a mobilní zařízení** > **webovou aplikaci**.

2. V okně **Webová aplikace** zadejte název do pole **Webová aplikace** .

3. V části **Skupina prostředků**klikněte na **Nový**. Do pole **Skupina prostředků** zadejte název.

4. Klikněte na **Plán nebo umístění služby App Service** > **Vytvořit nový**.

5. V okně **plán App Service** zadejte název do pole **App Service plán** .

6. Klikněte **na cenová úroveň** > **Free-Shared** nebo Shared **-Shared** > a**Vyberte** > **OK** > **vytvořit**.

7. Za minutu se na řídicím panelu zobrazí dlaždice nové webové aplikace. Klikněte na dlaždici.

8. V okně **webové aplikace** klikněte na tlačítko **Procházet** a zobrazte výchozí web pro tuto aplikaci.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Nasazení webu WordPress, DNN nebo Django (volitelné)

1. Na portálu Azure Stack tenant klikněte na **+** , přejděte do Azure Marketplace, nasaďte web Django a počkejte na úspěšné dokončení. Webová platforma Django používá databázi založenou na systému souborů. Nevyžaduje žádné další poskytovatele prostředků, jako je SQL nebo MySQL.

2. Pokud jste nasadili také poskytovatele prostředků MySQL, můžete web WordPress nasadit z webu Marketplace. Po zobrazení výzvy k zadání parametrů databáze zadejte uživatelské jméno jako *Uživatel1\@Server1*s uživatelským jménem a názvem serveru podle vašeho výběru.

3. Pokud jste nasadili také poskytovatele prostředků SQL Server, můžete web DNN nasadit z webu Marketplace. Po zobrazení výzvy k zadání parametrů databáze vyberte databázi v počítači se systémem SQL Server připojeném k poskytovateli prostředků.

## <a name="next-steps"></a>Další kroky

Příprava na další operace správy pro App Service v Azure Stack

- [Plánování kapacity](azure-stack-app-service-capacity-planning.md)
- [Konfigurace zdrojů nasazení](azure-stack-app-service-configure-deployment-sources.md)

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
