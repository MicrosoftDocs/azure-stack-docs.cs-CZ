---
title: Nasazení Azure App Service v offline prostředí v centru Azure Stack
description: Naučte se, jak nasadit Azure App Service v prostředí offline Azure Stack centra zabezpečeném AD FS.
author: BryanLa
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: 7fa4ac0f63b3f5243a6473c921012614bb01bfb2
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568618"
---
# <a name="deploy-azure-app-service-in-an-offline-environment-in-azure-stack-hub"></a>Nasazení Azure App Service v offline prostředí v centru Azure Stack

> [!IMPORTANT]
> Před nasazením Azure App Service 1,8 použijte aktualizaci 1910 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

Podle pokynů v tomto článku můžete nasadit [poskytovatele prostředků Azure App Service](azure-stack-app-service-overview.md) do prostředí Azure Stack hub:
- Nepřipojeno k Internetu
- Zabezpečeno pomocí Active Directory Federation Services (AD FS) (AD FS).

> [!IMPORTANT]
> Před spuštěním instalačního programu poskytovatele prostředků se ujistěte, že jste dokončili kroky v části [požadavky na nasazení Azure App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md). Měli byste si také přečíst [poznámky k verzi](azure-stack-app-service-release-notes-update-eight.md) , které doprovází vydání 1,8, abyste se seznámili s novými funkcemi, opravami a všemi známými problémy, které by mohly mít vliv na nasazení.

Chcete-li přidat poskytovatele prostředků Azure App Service do nasazení Azure Stackho centra online, je nutné dokončit tyto úlohy nejvyšší úrovně:

1. Dokončete požadované [kroky](azure-stack-app-service-before-you-get-started.md) (jako je nákup certifikátů), což může trvat několik dní.
2. [Stáhněte a extrahujte instalační a pomocné soubory](azure-stack-app-service-before-you-get-started.md) do počítače připojeného k Internetu.
3. Vytvořte instalační balíček offline.
4. Spusťte instalační soubor AppService. exe.

## <a name="create-an-offline-installation-package"></a>Vytvoření instalačního balíčku offline

Pokud chcete nasadit Azure App Service v offline prostředí, nejdřív vytvořte instalační balíček offline na počítači, který je připojený k Internetu.

1. Spusťte instalační program AppService. exe na počítači, který je připojený k Internetu. 

2. Vyberte **upřesnit** > **vytvořit instalační balíček offline**. Dokončení tohoto kroku bude trvat několik minut.

    ![Vytvoření offline balíčku v instalačním programu Azure App Service][1]

3. Instalační služba Azure App Service vytvoří instalační balíček offline a zobrazí cestu k němu. Můžete vybrat **Otevřít složku** a otevřít složku v Průzkumníku souborů.

    ![Instalační balíček offline se úspěšně vygeneroval v instalačním programu Azure App Service](media/azure-stack-app-service-deploy-offline/image02.png)

4. Zkopírujte instalační program (AppService. exe) a instalační balíček offline do hostitelského počítače centra Azure Stack.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Dokončení offline instalace Azure App Service v centru Azure Stack

1. Spusťte AppService. exe jako správce z počítače, který se může připojit ke koncovému bodu správy prostředků Azure pro správu centra Azure Stack.

1. Vyberte **upřesnit** > **dokončení offline instalace**.

    ![Dokončení offline instalace v instalačním programu Azure App Service][2]

1. Přejděte do umístění instalačního balíčku offline, který jste vytvořili dříve, a pak vyberte **Další**.

    ![Zadejte cestu k instalačnímu balíčku offline im Azure App Service instalační program](media/azure-stack-app-service-deploy-offline/image04.png)

1. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a potom vyberte možnost **Další**.

1. Přečtěte si a přijměte licenční podmínky třetí strany a pak vyberte **Další**.


1. Ujistěte se, že jsou informace o konfiguraci cloudu Azure App Service správné. Pokud jste během nasazení ASDK použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení centra Azure Stack nebo nasazení v integrovaném systému, je nutné upravit hodnoty v tomto okně, aby odrážely tyto změny. Pokud například použijete příponu domény mycloud.com, Azure Resource Manager koncový bod klienta centra Azure Stack se musí změnit na `management.<region>.mycloud.com`. Po potvrzení vašich informací vyberte **Další**.

    ![Konfigurace Azure App Serviceho cloudu v Azure App Service instalační program][3]

1. Na další stránce instalačního programu App Service se připojíte ke svému centru Azure Stack:

    1. Vyberte metodu připojení, kterou chcete použít – **Credential** nebo **instanční objekt** .
        - **Pověřovací**
            - Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení centra Azure Stack. Vyberte **Connect** (Připojit).
            - Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. například cloudadmin@azurestack.local. Zadejte heslo a pak vyberte **připojit**.
        - **Instanční objekt**
            - Instanční objekt, který použijete, **musí** mít práva **vlastníka** na **předplatném výchozího poskytovatele** .
            - Zadejte **ID objektu služby**, **soubor certifikátu** a **heslo** a pak vyberte **připojit**.

    1. V části **předplatná centra Azure Stack**vyberte **výchozí předplatné poskytovatele**.  Azure App Service v centru Azure Stack **musí** být nasazené v **předplatném výchozího poskytovatele**.

    1. V **umístění centra Azure Stack**vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na ASDK, vyberte **místní** .

1. Instalačnímu programu Azure App Service můžete dovolit vytvořit virtuální síť a přidružené podsítě. Nebo můžete nasadit do existující virtuální sítě, jak je nakonfigurované pomocí [těchto kroků](azure-stack-app-service-before-you-get-started.md#virtual-network).
   - Chcete-li použít metodu instalačního programu Azure App Service, vyberte možnost **vytvořit virtuální síť s výchozím nastavením**, přijměte výchozí hodnoty a pak vyberte možnost **Další**.
   - Chcete-li nasadit do existující sítě, vyberte možnost **použít existující virtuální síť a podsítě**a pak:
       1. Vyberte možnost **skupiny prostředků** , která obsahuje vaši virtuální síť.
       2. Vyberte název **Virtual Network** , do kterého chcete nasadit.
       3. Vyberte správné hodnoty **podsítí** pro každou z požadovaných podsítí rolí.
       4. Vyberte **Další**.

      ![Informace o virtuální síti a podsíti v Instalační službě Azure App Service][5]

1. Zadejte informace pro sdílenou složku a potom vyberte **Další**. Adresa sdílené složky musí používat plně kvalifikovaný název domény (FQDN) nebo IP adresu vašeho souborového serveru. Například: \\\appservicefileserver.Local.cloudapp.azurestack.external\websites nebo \\\10.0.0.1\websites.  Pokud používáte souborový server, který je připojený k doméně, musíte zadat úplné uživatelské jméno včetně domény. Například: `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > Instalační program se před pokračováním pokusí otestovat připojení ke sdílení souborů. Pokud se ale rozhodnete nasadit do existující virtuální sítě, instalační program se nemusí připojit ke sdílené složce a zobrazí upozornění s dotazem, zda chcete pokračovat. Ověřte informace o sdílené složce a pokračujte, pokud je správná.

   ![Informace o sdílení souborů v instalačním programu Azure App Service][8]

1. Na další stránce:
    1. V poli **ID aplikace identity** zadejte identifikátor GUID aplikace, kterou používáte pro identitu (z Azure AD).
    1. Do pole **soubor certifikátu aplikace identity** zadejte (nebo vyhledejte) umístění souboru certifikátu.
    1. Do pole **heslo aplikace identity** zadejte heslo certifikátu. Toto heslo je ten, který jste si poznamenali, když jste použili skript k vytvoření certifikátů.
    1. Do pole **Azure Resource Manager kořenový certifikát souboru** zadejte (nebo vyhledejte) umístění souboru certifikátu.
    1. Vyberte **Další**.

    ![Zadejte informace o ID aplikace a certifikátu v Instalační službě Azure App Service.][10]

1. U každého ze tří polí certifikátu vyberte **Procházet**a potom přejděte na příslušný soubor certifikátu. Je nutné zadat heslo pro každý certifikát. Tyto certifikáty jsou ty, které jste vytvořili v [kroku vytvoření požadovaných certifikátů](azure-stack-app-service-before-you-get-started.md#get-certificates). Po zadání všech informací vyberte **Další** .

    | Box | Příklad názvu souboru certifikátu |
    | --- | --- |
    | **App Service výchozí soubor certifikátu SSL** | \_. AppService. Local. AzureStack. external. pfx |
    | **Soubor certifikátu SSL pro App Service rozhraní API** | api.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL App Service vydavatele** | ftp.appservice.local.AzureStack.external.pfx |

    Pokud jste při vytváření certifikátů použili jinou příponu domény, názvy souborů certifikátu nepoužívají *místní. AzureStack. external*. Místo toho použijte vlastní informace o doméně.

    ![Zadání informací o certifikátu SSL v instalačním programu Azure App Service][11]

1. Zadejte podrobnosti SQL Server pro instanci serveru, která se používá k hostování Azure App Servicech databází poskytovatele prostředků, a pak vyberte **Další**. Instalační program ověří vlastnosti připojení SQL. Pro název SQL Server **musíte** zadat buď interní IP adresu, nebo plně kvalifikovaný název domény.

    > [!NOTE]
    > Instalační program se před pokračováním pokusí otestovat připojení k počítači se spuštěným SQL Server. Pokud jste však zvolili nasazení do existující virtuální sítě, instalační program nemusí být schopný připojit se k počítači se systémem SQL Server a zobrazí upozornění s dotazem, zda chcete pokračovat. Ověřte informace o SQL Server a pokračujte v případě, že jsou správné.
    >
    > Z Azure App Service ve službě Azure Stack hub 1,3 dále instalační program zkontroluje, že na SQL Server úrovni má počítač se systémem SQL Server povolený zahrnutí databáze. Pokud tomu tak není, budete vyzváni k následující výjimce:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Další podrobnosti najdete v [poznámkách k verzi pro Azure App Service v centru Azure Stack 1,3](azure-stack-app-service-release-notes-update-three.md).

    ![Zadat informace o SQL Server v instalačním programu Azure App Service][12]

1. Zkontrolujte možnosti instance role a SKU. Výchozí hodnoty se naplní minimálním počtem instancí a minimální SKU pro každou roli v nasazení ASDK. Shrnutí požadavků na vCPU a paměť je k dispozici pro plánování nasazení. Po provedení výběru vyberte **Další**.

     > [!NOTE]
     > Při nasazení v produkčním prostředí postupujte podle pokynů v části [plánování kapacity pro Azure App Service role serveru v centru Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Role | Minimální instance | Minimální SKU | Poznámky: |
    | --- | --- | --- | --- |
    | Kontrolér | 1 | Standard_A2 – (2 vCPU, 3584 MB) | Spravuje a udržuje stav cloudu Azure App Service. |
    | Správa | 1 | Standard_A2 – (2 vCPU, 3584 MB) | Spravuje Azure App Service Azure Resource Manager a koncové body rozhraní API, rozšíření portálu (správce, tenant, funkce portálu) a datovou službu. Pokud chcete převzetí služeb při selhání podporovat, zvyšte Doporučené instance na 2. |
    | Vydavatel | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Publikuje obsah prostřednictvím FTP a nasazení webu. |
    | FrontEnd | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Směruje požadavky na aplikace Azure App Service. |
    | Sdílený pracovní proces | 1 | Standard_A1 – (1 vCPU, 1792 MB) | Hostuje webové aplikace nebo aplikace API a aplikace Azure Functions. Možná budete chtít přidat další instance. Jako operátor můžete definovat svou nabídku a zvolit libovolnou úroveň SKU. Vrstvy musí mít minimálně jeden vCPU. |

    ![Nastavení úrovní rolí a možností skladové položky v instalačním programu Azure App Service][14]

1. V poli **bitová kopie platformy** zvolte nasazení image virtuálního počítače s Windows serverem 2016 z imagí dostupných na poskytovateli výpočetních prostředků pro cloud Azure App Service. Vyberte **Další**.

    > [!NOTE]
    > Windows Server 2016 *Core není* podporovaná image platformy pro použití s Azure App Service v centru Azure Stack.  Nepoužívejte zkušební image pro produkční nasazení. Azure App Service v centru Azure Stack je nutné, aby bylo na imagi používané k nasazení aktivováno rozhraní Microsoft .NET 3.5.1 SP1. Marketplace – pro bitové kopie systému Windows Server 2016 není tato funkce povolená. Proto musíte vytvořit a použít bitovou kopii systému Windows Server 2016 s touto funkcí, která je předem povolena.
    >
    > Podrobnosti o vytvoření vlastní image a přidání do Marketplace najdete v tématu [Přidání vlastní image virtuálního počítače do centra Azure Stack](azure-stack-add-vm-image.md) . Při přidávání image do Marketplace Nezapomeňte zadat následující:
    >
    >- Vydavatel = MicrosoftWindowsServer
    >- Nabídka = WindowsServer
    >- SKU = 2016 – Datacenter
    >- Verze = zadejte nejnovější verzi

1. Na další stránce:
     1. Zadejte uživatelské jméno a heslo správce virtuálního počítače role pracovního procesu.
     2. Zadejte uživatelské jméno a heslo správce virtuálního počítače jiné role.
     3. Vyberte **Další**.

    ![Zadání role VM VM Admins v instalačním programu Azure App Service][16]

1. Na stránce Souhrn:
    1. Ověřte provedené volby. Chcete-li provést změny, **přejděte na předchozí tlačítka,** kde můžete přejít na předchozí stránky.
    2. Jsou-li konfigurace správné, zaškrtněte políčko.
    3. Pokud chcete spustit nasazení, vyberte **Další**.

    ![Souhrn výběrů provedených v instalačním programu Azure App Service][17]

1. Na další stránce:
    1. Sledujte průběh instalace. Nasazení App Service v centru Azure Stack může trvat až 240 minut, než se nasadí na základě výchozích výběrů a stáří základní image Windows 2016 Datacenter.

    2. Po dokončení spuštění instalačního programu vyberte možnost **ukončit**.

    ![Sledování procesu instalace v instalačním programu Azure App Service][18]

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli Azure App Service RP s instancí SQL Always On, je *nutné* [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Je také nutné synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete do skupiny zabezpečení sítě WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

- Zdroj: Any
- Rozsah zdrojových portů: *
- Cíl: IP adresy
- Rozsah cílových IP adres: rozsah IP adres pro souborový server
- Rozsah cílových portů: 445
- Protocol: TCP
- Akce: povolení
- Priorita: 700
- Název: Outbound_Allow_SMB445

## <a name="validate-the-azure-app-service-on-azure-stack-hub-installation"></a>Ověření Azure App Service v instalaci centra Azure Stack

1. Na portálu pro správu centra Azure Stack přejít na **Správa-App Service**.

1. V přehledu v části Stav zkontrolujte, že **stav** zobrazuje **všechny role jsou připravené**.

    ![Přehled správy Azure App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-azure-app-service-on-azure-stack-hub"></a>Azure App Service testovacích jednotek v centru Azure Stack

Až nasadíte a zaregistrujete poskytovatele prostředků Azure App Service, otestujte ho a ujistěte se, že uživatelé můžou nasazovat webové aplikace a aplikace API.

> [!NOTE]
> V rámci plánu je nutné vytvořit nabídku, která má obor názvů Microsoft. Web. Pak musíte mít předplatné tenanta, které se přihlásí k odběru této nabídky. Další informace najdete v tématu [Vytvoření nabídky](azure-stack-create-offer.md) a [Vytvoření plánu](azure-stack-create-plan.md).
>
> Abyste mohli vytvářet aplikace, které používají Azure App Service v centru Azure Stack, *musíte* mít předplatné tenanta. Jediné možnosti, které může správce služby dokončit v rámci portálu pro správu, se týkají správy Azure App Service poskytovatele prostředků. Mezi tyto možnosti patří přidání kapacity, konfigurace zdrojů nasazení a přidání úrovní a SKU pracovního procesu.
>
> Od třetí verze Technical Preview, pokud chcete vytvářet webové aplikace, rozhraní API a Azure Functions aplikace, musíte použít portál User Portal a mít předplatné tenanta.

1. Na portálu Azure Stack User Portal vyberte **+ vytvořit prostředek** > **web a mobilní zařízení** > **Webová aplikace**.

1. V okně **Webová aplikace** zadejte název do pole **Webová aplikace** .

1. V části **Skupina prostředků**vyberte **Nový**. Do pole **Skupina prostředků** zadejte název.

1. Vyberte **App Service plán/umístění** > **vytvořit nový**.

1. V okně **plán App Service** zadejte název do pole **App Service plán** .

1. Vyberte **cenovou úroveň** > **volno Shared** nebo **shared-Shared** > **Vyberte** > **OK** > **vytvořit**.

1. Za míň než minutu se na řídicím panelu zobrazí dlaždice nové webové aplikace. Vyberte dlaždici.

1. V okně **Webová aplikace** vyberte **Procházet** a zobrazte výchozí web pro tuto aplikaci.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Nasazení webu WordPress, DNN nebo Django (volitelné)

1. Na portálu Azure Stack hub User Portal vyberte **+** , přejít na Azure Marketplace, nasaďte web Django a počkejte na úspěšné dokončení. Webová platforma Django používá databázi založenou na systému souborů. Nevyžaduje žádné další poskytovatele prostředků, jako je SQL nebo MySQL.

1. Pokud jste nasadili také poskytovatele prostředků MySQL, můžete web WordPress nasadit z Azure Marketplace. Po zobrazení výzvy k zadání parametrů databáze zadejte uživatelské jméno jako *uživatel1\@Server1*s uživatelským jménem a zvoleným názvem serveru.

1. Pokud jste nasadili také poskytovatele prostředků SQL Server, můžete nasadit web DNN z Azure Marketplace. Po zobrazení výzvy k zadání parametrů databáze vyberte databázi na počítači se systémem SQL Server připojeném k poskytovateli prostředků.

## <a name="next-steps"></a>Další kroky

Příprava na další operace správy pro Azure App Service v centru Azure Stack:

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
