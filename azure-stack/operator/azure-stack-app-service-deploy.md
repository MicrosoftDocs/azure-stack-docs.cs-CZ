---
title: Nasazení služby App Service ve službě Azure Stack Hub
description: Přečtěte si, jak nasadit App Service v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 05/05/2020
ms.author: bryanla
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: d8b5be96bf3e150308faf01c161d20e180beeb69
ms.sourcegitcommit: 53b0dde60a6435936a5e0cb9e931245f262d637a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2020
ms.locfileid: "91106975"
---
# <a name="deploy-app-service-in-azure-stack-hub"></a>Nasazení služby App Service ve službě Azure Stack Hub

[!INCLUDE [Azure Stack hub update reminder](../includes/app-service-hub-update-banner.md)]

> [!IMPORTANT]
> Před spuštěním instalačního programu poskytovatele prostředků musíte provést kroky uvedené v části, abyste mohli [začít](azure-stack-app-service-before-you-get-started.md) .

::: zone pivot="state-connected"
V tomto článku se naučíte, jak nasadit App Service v centru Azure Stack, který uživatelům umožňuje vytvářet aplikace pro web, rozhraní API a Azure Functions. Musíte:

- Přidejte [App Service poskytovatele prostředků](azure-stack-app-service-overview.md) do nasazení centra Azure Stack pomocí postupu popsaného v tomto článku.
- Až nainstalujete poskytovatele prostředků App Service, můžete ho zahrnout do nabídek a plánů. Uživatelé se pak můžou přihlásit k odběru služby a začít vytvářet aplikace.

## <a name="run-the-app-service-resource-provider-installer"></a>Spuštění instalačního programu poskytovatele prostředků App Service

Instalace poskytovatele prostředků App Service trvá alespoň hodinu. Doba potřebná pro závisí na počtu instancí rolí, které nasadíte. Během nasazení spustí instalační program následující úlohy:

- Zaregistruje požadované poskytovatele prostředků v předplatném výchozího zprostředkovatele.
- Udělí přispěvatelům přístup k aplikaci App Service identity.
- Vytvoření skupiny prostředků a virtuální sítě (v případě potřeby)
- Vytváření účtů a kontejnerů úložiště pro App Service artefakty instalace, služba využití a aplikace pro vysazování prostředků
- Stáhnout App Service artefakty a nahrajte je do účtu úložiště App Service
- Nasazení App Service
- Registrace služby využití
- Vytvoření záznamů DNS pro App Service
- Zaregistrujte poskytovatele App Service správce a prostředků tenanta.
- Registrovat položky Galerie – web, rozhraní API, Function App, App Service plán, WordPress, DNN, sady a Django aplikace

Pokud chcete nasadit poskytovatele prostředků App Service, postupujte takto:

1. Spusťte appservice.exe jako správce z počítače, který má přístup ke koncovému bodu správy prostředků Azure pro správu centra Azure Stack.

2. Vyberte **nasadit App Service nebo upgradujte na nejnovější verzi**.

    ![Snímek obrazovky znázorňující hlavní obrazovku instalační služby Azure App Service.][1]

3. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a potom vyberte možnost **Další**.

4. Přečtěte si a přijměte licenční podmínky třetí strany a pak vyberte **Další**.

5. Ujistěte se, že jsou informace o konfiguraci cloudu App Service správné. Pokud jste během nasazení ASDK použili výchozí nastavení, můžete přijmout výchozí hodnoty. Pokud jste ale přizpůsobili možnosti, když jste nasadili ASDK nebo nasazujete v integrovaném systému Azure Stack hub, musíte upravit hodnoty v tomto okně, aby odrážely rozdíly.

   Pokud například použijete příponu domény mycloud.com, Azure Resource Manager koncový bod klienta centra Azure Stack musí změnit na správu. &lt; region &gt; . MyCloud.com. Zkontrolujte tato nastavení a kliknutím na tlačítko **Další** uložte nastavení.

   ![Snímek obrazovky, který zobrazuje obrazovku pro určení koncových bodů ARM pro App Service.][2]

6. Na další stránce instalačního programu App Service se připojíte ke svému centru Azure Stack:

    1. Vyberte metodu připojení, kterou chcete použít – **Credential** nebo **instanční objekt** .
 
        - **Přihlašovací údaj**
            - Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení centra Azure Stack. Vyberte **Připojit**.
            - Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte heslo a pak vyberte **připojit**.

        - **Instanční objekt**
            - Instanční objekt, který použijete, **musí** mít práva **vlastníka** na **předplatném výchozího zprostředkovatele** .
            - Zadejte **ID objektu služby**, **soubor certifikátu**a **heslo** a pak vyberte **připojit**.

    1. V části **předplatná centra Azure Stack**vyberte **výchozí předplatné poskytovatele**.  Azure App Service v centru Azure Stack **musí** být nasazené v **předplatném výchozího poskytovatele**.

    1. V **umístění centra Azure Stack**vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na ASDK, vyberte **místní** .

    ![Snímek obrazovky, který ukazuje, kde zadáte informace o předplatném centra Azure Stack v instalačním programu App Service][3]

7. Nyní můžete nasadit do existující virtuální sítě, kterou jste nakonfigurovali [pomocí těchto kroků](azure-stack-app-service-before-you-get-started.md#virtual-network), nebo nechat instalační program App Service vytvořit novou virtuální síť a podsítě. Virtuální síť vytvoříte pomocí následujících kroků:

   a. Vyberte **vytvořit virtuální síť s výchozím nastavením**, přijměte výchozí hodnoty a pak vyberte **Další**.

   b. Případně vyberte možnost **použít existující virtuální síť a podsítě**. Proveďte následující akce:

     - Vyberte **skupinu prostředků** , která obsahuje vaši virtuální síť.
     - Vyberte název **Virtual Network** , do kterého chcete nasadit.
     - Vyberte správné hodnoty **podsítí** pro každou z požadovaných podsítí rolí.
     - Vyberte **Další**.

   ![Snímek obrazovky zobrazující obrazovku, kde můžete nakonfigurovat virtuální síť v instalačním programu App Service.][4]

8. Zadejte informace pro sdílenou složku a potom vyberte **Další**. Adresa sdílené složky musí používat plně kvalifikovaný název domény (FQDN) nebo IP adresu vašeho souborového serveru. Například \\ \appservicefileserver.Local.cloudapp.azurestack.external\websites nebo \\ \10.0.0.1\websites.  Pokud používáte souborový server, který je připojený k doméně, musíte zadat úplné uživatelské jméno včetně domény. Například myfileserverdomain\FileShareOwner.

   >[!NOTE]
   >Instalační program se před pokračováním pokusí otestovat připojení ke sdílení souborů. Pokud však nasazujete do existující virtuální sítě, může tento test připojení selhat. Zobrazí se upozornění a výzva k pokračování. Pokud jsou informace o sdílené složce správné, pokračujte v nasazení.

   ![Snímek obrazovky, který zobrazuje konfiguraci sdílení souborů v instalačním programu App Service.][7]

9. Na další stránce Instalační služby App Service proveďte následující kroky:

   a. V poli **ID aplikace identity** zadejte identifikátor GUID aplikace identity, kterou jste vytvořili jako součást [požadavků](azure-stack-app-service-before-you-get-started.md).

   b. Do pole **soubor certifikátu aplikace identity** zadejte (nebo vyhledejte) umístění souboru certifikátu.

   c. Do pole **heslo aplikace identity** zadejte heslo certifikátu. Toto heslo je ten, který jste si poznamenali, když jste použili skript k vytvoření certifikátů.

   d. Do pole **Azure Resource Manager kořenový certifikát souboru** zadejte (nebo vyhledejte) umístění souboru certifikátu.

   e. Vyberte **Další**.

   ![Snímek obrazovky, který ukazuje, kam zadat informace o aplikaci identity v instalačním programu App Service.][9]

10. U každého ze tří polí certifikátu vyberte **Procházet** a přejděte na příslušný soubor certifikátu. Je nutné zadat heslo pro každý certifikát. Tyto certifikáty jsou ty, které jste vytvořili v části [požadavky na nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md). Po zadání všech informací vyberte **Další** .

    | Box | Příklad názvu souboru certifikátu |
    | --- | --- |
    | **App Service výchozí soubor certifikátu SSL** | \_. AppService. Local. AzureStack. external. pfx |
    | **Soubor certifikátu SSL pro App Service rozhraní API** | API. AppService. Local. AzureStack. external. pfx |
    | **Soubor certifikátu SSL App Service vydavatele** | FTP. AppService. Local. AzureStack. external. pfx |

    Pokud jste při vytváření certifikátů použili jinou příponu domény, názvy souborů certifikátu nepoužívají *místní. AzureStack. external*. Místo toho použijte vlastní informace o doméně.

    ![Snímek obrazovky, který ukazuje, kam zadat umístění certifikátů a hesla v instalačním programu App Service.][10]

11. Zadejte podrobnosti SQL Server pro instanci serveru, která se používá k hostování App Service databáze poskytovatele prostředků, a pak vyberte **Další**. Instalační program ověří vlastnosti připojení SQL.<br><br>Instalační program App Service se před pokračováním pokusí otestovat připojení k SQL Server. Pokud nasazujete do existující virtuální sítě, může tento test připojení selhat. Zobrazí se upozornění a výzva k pokračování. Pokud jsou informace o SQL Server správné, pokračujte v nasazení.

    ![Snímek obrazovky, který ukazuje, kam zadat informace o konfiguraci SQL v instalačním programu App Service.][11]

12. Zkontrolujte možnosti instance role a SKU. Výchozí hodnoty se naplní minimálním počtem instancí a minimální SKU pro každou roli v produkčním nasazení.  Pro nasazení ASDK můžete škálovat instance na nižší skladové jednotky a snížit tak základní hodnoty a potvrzení o paměti, ale budete mít k dispozici snížení výkonu. Shrnutí požadavků na vCPU a paměť je k dispozici pro plánování nasazení. Po provedení výběru vyberte **Další**.

    >[!NOTE]
    >Při nasazení v produkčním prostředí postupujte podle pokynů v části [plánování kapacity pro Azure App Service role serveru v centru Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Role | Minimální instance | Minimální SKU | Poznámky |
    | --- | --- | --- | --- |
    | Kontrolér | 2 | Standard_A4_v2 – (4 jádra, 8192 MB) | Spravuje a udržuje stav cloudu App Service. |
    | Správa | 1 | Standard_D3_v2 – (4 jádra, 14336 MB) | Spravuje App Service Azure Resource Manager a koncové body rozhraní API, rozšíření portálu (správce, tenant, funkce portálu) a datovou službu. Pokud chcete převzetí služeb při selhání podporovat, zvyšte Doporučené instance na 2. |
    | Publisher | 1 | Standard_A2_v2 – (2 jádra, 4096 MB) | Publikuje obsah prostřednictvím FTP a nasazení webu. |
    | FrontEnd | 1 | Standard_A4_v2 – (4 jádra, 8192 MB) | Směruje požadavky na aplikace App Service. |
    | Sdílený pracovní proces | 1 | Standard_A4_v2 – (4 jádra, 8192 MB) | Hostuje webové aplikace nebo aplikace API a aplikace Azure Functions. Možná budete chtít přidat další instance. Jako operátor můžete definovat svou nabídku a zvolit libovolnou úroveň SKU. Vrstvy musí mít minimálně jeden vCPU. |

    ![Snímek obrazovky, který ukazuje, kde můžete nakonfigurovat role pracovních procesů v instalačním programu App Service.][13]

    > [!NOTE]
    > **Windows Server 2016 Core není podporovaná image platformy pro použití s Azure App Service v centru Azure Stack.  Nepoužívejte zkušební image pro produkční nasazení.**

13. V poli **Image platformy vyberte** nasazení virtuálního počítače s Windows serverem 2016 z imagí dostupných v poskytovateli výpočetních prostředků pro Cloud App Service. Vyberte **Další**.

14. Na další stránce Instalační služby App Service proveďte následující kroky:

     a. Zadejte uživatelské jméno a heslo správce virtuálního počítače role pracovního procesu.

     b. Zadejte uživatelské jméno a heslo správce virtuálního počítače jiné role.

     c. Vyberte **Další**.

    ![Snímek obrazovky, který ukazuje, kde nakonfigurovat přihlašovací údaje role pracovního procesu v instalačním programu App Service][15]

15. Na stránce Souhrn instalační služby App Service proveďte následující kroky:

    a. Ověřte provedené volby. Chcete-li provést změny, **přejděte na předchozí tlačítka,** kde můžete přejít na předchozí stránky.

    b. Jsou-li konfigurace správné, zaškrtněte políčko.

    c. Pokud chcete spustit nasazení, vyberte **Další**.

    ![Snímek obrazovky zobrazující souhrnné informace o nasazení zásobníku v instalačním programu App Service.][16]

16. Na další stránce Instalační služby App Service proveďte následující kroky:

    a. Sledujte průběh instalace. Nasazení App Service v centru Azure Stack může trvat až 240 minut, než se nasadí na základě výchozích výběrů a stáří základní image Windows 2016 Datacenter.

    b. Po úspěšném dokončení instalačního programu vyberte možnost  **ukončit**.

    ![Snímek obrazovky, který ukazuje průběh nasazení v instalačním programu App Service.][17]

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service RP s instancí SQL Always On, je **nutné** [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

Pokud nasazujete do existující virtuální sítě a pomocí interní IP adresy se připojíte k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení. Toto pravidlo povoluje provoz protokolu SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete do skupiny zabezpečení sítě WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

- Zdroj: Any
- Rozsah zdrojových portů: *
- Cíl: IP adresy
- Rozsah cílových IP adres: rozsah IP adres pro souborový server
- Rozsah cílových portů: 445
- Protokol. TCP
- Akce: Povolit
- Priorita: 700
- Název: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-hub-installation"></a>Ověření App Service v instalaci centra Azure Stack

1. Na portálu pro správu centra Azure Stack přejít na **Správa-App Service**.

2. V přehledu v části Stav zkontrolujte, že **stav** zobrazuje **všechny role jsou připravené**.

    ![Správa App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack-hub"></a>Testovací verze služby App Service ve službě Azure Stack Hub

Až nasadíte a zaregistrujete poskytovatele prostředků App Service, otestujte ho a ujistěte se, že uživatelé můžou nasazovat webové aplikace a aplikace API.

>[!NOTE]
>Musíte vytvořit nabídku, která má v plánu obor názvů Microsoft. Web. Budete také potřebovat předplatné tenanta, které se přihlásí k odběru nabídky. Další informace najdete v tématu [Vytvoření nabídky](azure-stack-create-offer.md) a [Vytvoření plánu](azure-stack-create-plan.md).
>
>Abyste mohli vytvářet aplikace, které používají App Service v centru Azure Stack, *musíte* mít předplatné tenanta. Jediné úlohy, které může správce služby dokončit na portálu pro správu, se vztahují k správě App Service poskytovatele prostředků. To zahrnuje přidání kapacity, konfiguraci zdrojů nasazení a přidání úrovní a SKU pracovního procesu.
>
>Pokud chcete vytvářet webové aplikace, rozhraní API a Azure Functions aplikace, musíte použít portál User Portal a mít předplatné tenanta.
>

Chcete-li vytvořit testovací webovou aplikaci, postupujte podle následujících kroků:

1. V uživatelském portálu Azure Stack hub vyberte **+ vytvořit prostředek**  >  **web a mobilní zařízení**  >  **webovou aplikaci**.

2. V části **Webová aplikace**zadejte název do **webové aplikace**.

3. V části **Skupina prostředků**vyberte **Nový**. Zadejte název **skupiny prostředků**.

4. Vyberte **App Service plán/umístění**  >  **vytvořit nový**.

5. V části **plán App Service**zadejte název **App Serviceho plánu**.

6. Vyberte **cenovou úroveň**  >  **Free-Shared** nebo **Shared-Shared**a  >  **Vyberte**  >  **OK**  >  **vytvořit**.

7. Na řídicím panelu se zobrazí dlaždice nové webové aplikace. Vyberte dlaždici.

8. U **webové aplikace**vyberte **Procházet** a zobrazte výchozí web pro tuto aplikaci.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Nasazení webu WordPress, DNN nebo Django (volitelné)

1. Na portálu Azure Stack User Portal vyberte **+** , přejít na Azure Marketplace, nasaďte web Django a potom počkejte na dokončení nasazení. Webová platforma Django používá databázi založenou na systému souborů. Nevyžaduje žádné další poskytovatele prostředků, jako je SQL nebo MySQL.

2. Pokud jste nasadili také poskytovatele prostředků MySQL, můžete web WordPress nasadit z webu Marketplace. Po zobrazení výzvy k zadání parametrů databáze zadejte uživatelské jméno jako *Uživatel1 \@ Server1*s uživatelským jménem a názvem serveru podle vašeho výběru.

3. Pokud jste nasadili také poskytovatele prostředků SQL Server, můžete web DNN nasadit z webu Marketplace. Po zobrazení výzvy k zadání parametrů databáze vyberte databázi v počítači se systémem SQL Server připojeném k poskytovateli prostředků.
::: zone-end



<!----------------------------------------- DISCONNECTED PIVOT -------------------------------------------->
::: zone pivot="state-disconnected"
V tomto článku se dozvíte, jak nasadit [poskytovatele prostředků Azure App Service](azure-stack-app-service-overview.md) do prostředí Azure Stack hub:
- Nepřipojeno k Internetu
- Zabezpečeno pomocí Active Directory Federation Services (AD FS) (AD FS).

Chcete-li přidat poskytovatele prostředků Azure App Service do nasazení Azure Stackho centra online, je nutné dokončit tyto úlohy nejvyšší úrovně:

1. Dokončete požadované [kroky](azure-stack-app-service-before-you-get-started.md) (jako je nákup certifikátů), což může trvat několik dní.
2. [Stáhněte a extrahujte instalační a pomocné soubory](azure-stack-app-service-before-you-get-started.md) do počítače připojeného k Internetu.
3. Vytvořte instalační balíček offline.
4. Spusťte instalační soubor appservice.exe.

## <a name="create-an-offline-installation-package"></a>Vytvoření instalačního balíčku offline

Pokud chcete nasadit Azure App Service v offline prostředí, nejdřív vytvořte instalační balíček offline na počítači, který je připojený k Internetu.

1. Spusťte instalační program AppService.exe na počítači, který je připojený k Internetu. 

2. Vyberte **Upřesnit**  >  **vytvořit balíček pro offline instalaci**. Dokončení tohoto kroku bude trvat několik minut.

    ![Vytvoření offline balíčku v instalačním programu Azure App Service][31]

3. Instalační služba Azure App Service vytvoří instalační balíček offline a zobrazí cestu k němu. Můžete vybrat **Otevřít složku** a otevřít složku v Průzkumníku souborů.

    ![Instalační balíček offline se úspěšně vygeneroval v instalačním programu Azure App Service](media/azure-stack-app-service-deploy-offline/image02.png)

4. Zkopírujte instalační program (AppService.exe) a offline instalační balíček do počítače, který má připojení k vašemu centru Azure Stack.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Dokončení offline instalace Azure App Service v centru Azure Stack

1. Spusťte appservice.exe jako správce z počítače, který se může připojit ke koncovému bodu správy prostředků Azure pro správu centra Azure Stack.

1. Vyberte **Upřesnit**  >  **kompletní offline instalaci**.

    ![Dokončení offline instalace v instalačním programu Azure App Service][32]

1. Přejděte do umístění instalačního balíčku offline, který jste vytvořili dříve, a pak vyberte **Další**.

    ![Zadejte cestu k instalačnímu balíčku offline im Azure App Service instalační program](media/azure-stack-app-service-deploy-offline/image04.png)

1. Zkontrolujte a přijměte licenční podmínky pro software společnosti Microsoft a potom vyberte možnost **Další**.

1. Přečtěte si a přijměte licenční podmínky třetí strany a pak vyberte **Další**.


1. Ujistěte se, že jsou informace o konfiguraci cloudu Azure App Service správné. Pokud jste během nasazení ASDK použili výchozí nastavení, můžete zde přijmout výchozí hodnoty. Pokud jste však přizpůsobili možnosti při nasazení centra Azure Stack nebo nasazení v integrovaném systému, je nutné upravit hodnoty v tomto okně, aby odrážely tyto změny. Pokud například použijete příponu domény mycloud.com, Azure Resource Manager koncový bod klienta centra Azure Stack musí změnit na `management.<region>.mycloud.com` . Po potvrzení vašich informací vyberte **Další**.

    ![Konfigurace Azure App Serviceho cloudu v Azure App Service instalační program][33]

1. Na další stránce instalačního programu App Service se připojíte ke svému centru Azure Stack:

    1. Vyberte metodu připojení, kterou chcete použít – **Credential** nebo **instanční objekt** .
        - **Přihlašovací údaj**
            - Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení centra Azure Stack. Vyberte **Připojit**.
            - Pokud používáte Active Directory Federation Services (AD FS) (AD FS), zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte heslo a pak vyberte **připojit**.
        - **Instanční objekt**
            - Instanční objekt, který použijete, **musí** mít práva **vlastníka** na **předplatném výchozího zprostředkovatele** .
            - Zadejte **ID objektu služby**, **soubor certifikátu**a **heslo** a pak vyberte **připojit**.

    1. V části **předplatná centra Azure Stack**vyberte **výchozí předplatné poskytovatele**.  Azure App Service v centru Azure Stack **musí** být nasazené v **předplatném výchozího poskytovatele**.

    1. V **umístění centra Azure Stack**vyberte umístění, které odpovídá oblasti, do které nasazujete. Pokud například nasazujete na ASDK, vyberte **místní** .

1. Instalačnímu programu Azure App Service můžete dovolit vytvořit virtuální síť a přidružené podsítě. Nebo můžete nasadit do existující virtuální sítě, jak je nakonfigurované pomocí [těchto kroků](azure-stack-app-service-before-you-get-started.md#virtual-network).
   - Chcete-li použít metodu instalačního programu Azure App Service, vyberte možnost **vytvořit virtuální síť s výchozím nastavením**, přijměte výchozí hodnoty a pak vyberte možnost **Další**.
   - Chcete-li nasadit do existující sítě, vyberte možnost **použít existující virtuální síť a podsítě**a pak:
       1. Vyberte možnost **skupiny prostředků** , která obsahuje vaši virtuální síť.
       2. Vyberte název **Virtual Network** , do kterého chcete nasadit.
       3. Vyberte správné hodnoty **podsítí** pro každou z požadovaných podsítí rolí.
       4. Vyberte **Další**.

      ![Informace o virtuální síti a podsíti v Instalační službě Azure App Service][35]

1. Zadejte informace pro sdílenou složku a potom vyberte **Další**. Adresa sdílené složky musí používat plně kvalifikovaný název domény (FQDN) nebo IP adresu vašeho souborového serveru. Například: \\ \appservicefileserver.Local.cloudapp.azurestack.external\websites nebo \\ \10.0.0.1\websites.  Pokud používáte souborový server, který je připojený k doméně, musíte zadat úplné uživatelské jméno včetně domény. Například: `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > Instalační program se před pokračováním pokusí otestovat připojení ke sdílení souborů. Pokud se ale rozhodnete nasadit do existující virtuální sítě, instalační program se nemusí připojit ke sdílené složce a zobrazí upozornění s dotazem, zda chcete pokračovat. Ověřte informace o sdílené složce a pokračujte, pokud je správná.

   ![Informace o sdílení souborů v instalačním programu Azure App Service][38]

1. Na další stránce:
    1. V poli **ID aplikace identity** zadejte identifikátor GUID aplikace identity, kterou jste vytvořili jako součást [požadavků](azure-stack-app-service-before-you-get-started.md).
    1. Do pole **soubor certifikátu aplikace identity** zadejte (nebo vyhledejte) umístění souboru certifikátu.
    1. Do pole **heslo aplikace identity** zadejte heslo certifikátu. Toto heslo je ten, který jste si poznamenali, když jste použili skript k vytvoření certifikátů.
    1. Do pole **Azure Resource Manager kořenový certifikát souboru** zadejte (nebo vyhledejte) umístění souboru certifikátu.
    1. Vyberte **Další**.

    ![Zadejte informace o ID aplikace a certifikátu v Instalační službě Azure App Service.][40]

1. U každého ze tří polí certifikátu vyberte **Procházet** a přejděte na příslušný soubor certifikátu. Je nutné zadat heslo pro každý certifikát. Tyto certifikáty jsou ty, které jste vytvořili v části [požadavky na nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md). Po zadání všech informací vyberte **Další** .

    | Box | Příklad názvu souboru certifikátu |
    | --- | --- |
    | **App Service výchozí soubor certifikátu SSL** | \_. AppService. Local. AzureStack. external. pfx |
    | **Soubor certifikátu SSL pro App Service rozhraní API** | API. AppService. Local. AzureStack. external. pfx |
    | **Soubor certifikátu SSL App Service vydavatele** | FTP. AppService. Local. AzureStack. external. pfx |

    Pokud jste při vytváření certifikátů použili jinou příponu domény, názvy souborů certifikátu nepoužívají *místní. AzureStack. external*. Místo toho použijte vlastní informace o doméně.

    ![Zadání informací o certifikátu SSL v instalačním programu Azure App Service][41]

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
    > Další informace najdete v [poznámkách k verzi pro Azure App Service v centru Azure Stack 1,3](azure-stack-app-service-release-notes-update-three.md).

    ![Zadat informace o SQL Server v instalačním programu Azure App Service][42]

1. Zkontrolujte možnosti instance role a SKU. Výchozí hodnoty se naplní minimálním počtem instancí a minimální SKU pro každou roli v produkčním nasazení.  Pro nasazení ASDK můžete škálovat instance na nižší skladové jednotky a snížit tak základní hodnoty a potvrzení o paměti, ale budete mít k dispozici snížení výkonu.  Shrnutí požadavků na vCPU a paměť je k dispozici pro plánování nasazení. Po provedení výběru vyberte **Další**.

     > [!NOTE]
     > Při nasazení v produkčním prostředí postupujte podle pokynů v části [plánování kapacity pro Azure App Service role serveru v centru Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    
    | Role | Minimální instance | Minimální SKU | Poznámky |
    | --- | --- | --- | --- |
    | Kontrolér | 2 | Standard_A4_v2 – (4 jádra, 8192 MB) | Spravuje a udržuje stav cloudu App Service. |
    | Správa | 1 | Standard_D3_v2 – (4 jádra, 14336 MB) | Spravuje App Service Azure Resource Manager a koncové body rozhraní API, rozšíření portálu (správce, tenant, funkce portálu) a datovou službu. Pokud chcete převzetí služeb při selhání podporovat, zvyšte Doporučené instance na 2. |
    | Publisher | 1 | Standard_A2_v2 – (2 jádra, 4096 MB) | Publikuje obsah prostřednictvím FTP a nasazení webu. |
    | FrontEnd | 1 | Standard_A4_v2 – (4 jádra, 8192 MB) | Směruje požadavky na aplikace App Service. |
    | Sdílený pracovní proces | 1 | Standard_A4_v2 – (4 jádra, 8192 MB) | Hostuje webové aplikace nebo aplikace API a aplikace Azure Functions. Možná budete chtít přidat další instance. Jako operátor můžete definovat svou nabídku a zvolit libovolnou úroveň SKU. Vrstvy musí mít minimálně jeden vCPU. |

    ![Nastavení úrovní rolí a možností skladové položky v instalačním programu Azure App Service][44]

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

    ![Zadání role VM VM Admins v instalačním programu Azure App Service][46]

1. Na stránce Souhrn:
    1. Ověřte provedené volby. Chcete-li provést změny, **přejděte na předchozí tlačítka,** kde můžete přejít na předchozí stránky.
    2. Jsou-li konfigurace správné, zaškrtněte políčko.
    3. Pokud chcete spustit nasazení, vyberte **Další**.

    ![Souhrn výběrů provedených v instalačním programu Azure App Service][47]

1. Na další stránce:
    1. Sledujte průběh instalace. Nasazení App Service v centru Azure Stack může trvat až 240 minut, než se nasadí na základě výchozích výběrů a stáří základní image Windows 2016 Datacenter.

    2. Po dokončení spuštění instalačního programu vyberte možnost **ukončit**.

    ![Sledování procesu instalace v instalačním programu Azure App Service][48]

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli Azure App Service RP s instancí SQL Always On, je **nutné** [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Je také nutné synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete do skupiny zabezpečení sítě WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

- Zdroj: Any
- Rozsah zdrojových portů: *
- Cíl: IP adresy
- Rozsah cílových IP adres: rozsah IP adres pro souborový server
- Rozsah cílových portů: 445
- Protokol. TCP
- Akce: Povolit
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

1. V uživatelském portálu Azure Stack hub vyberte **+ vytvořit prostředek**  >  **web a mobilní zařízení**  >  **webovou aplikaci**.

1. V okně **Webová aplikace** zadejte název do pole **Webová aplikace** .

1. V části **Skupina prostředků**vyberte **Nový**. Do pole **Skupina prostředků** zadejte název.

1. Vyberte **App Service plán/umístění**  >  **vytvořit nový**.

1. V okně **plán App Service** zadejte název do pole **App Service plán** .

1. Vyberte **cenovou úroveň**  >  **Free-Shared** nebo **Shared-Shared**a  >  **Vyberte**  >  **OK**  >  **vytvořit**.

1. Za míň než minutu se na řídicím panelu zobrazí dlaždice nové webové aplikace. Vyberte dlaždici.

1. V okně **Webová aplikace** vyberte **Procházet** a zobrazte výchozí web pro tuto aplikaci.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Nasazení webu WordPress, DNN nebo Django (volitelné)

1. Na portálu Azure Stack hub User Portal vyberte **+** , přejít na Azure Marketplace, nasaďte web Django a počkejte na úspěšné dokončení. Webová platforma Django používá databázi založenou na systému souborů. Nevyžaduje žádné další poskytovatele prostředků, jako je SQL nebo MySQL.

1. Pokud jste nasadili také poskytovatele prostředků MySQL, můžete web WordPress nasadit z Azure Marketplace. Po zobrazení výzvy k zadání parametrů databáze zadejte uživatelské jméno jako *Uživatel1 \@ Server1*s uživatelským jménem a názvem serveru podle vašeho výběru.

1. Pokud jste nasadili také poskytovatele prostředků SQL Server, můžete nasadit web DNN z Azure Marketplace. Po zobrazení výzvy k zadání parametrů databáze vyberte databázi na počítači se systémem SQL Server připojeném k poskytovateli prostředků.

::: zone-end

## <a name="next-steps"></a>Další kroky

Příprava na další operace správy pro App Service v centru Azure Stack:

- [Plánování kapacity](azure-stack-app-service-capacity-planning.md)
- [Konfigurace zdrojů nasazení](azure-stack-app-service-configure-deployment-sources.md)

<!-- Connected image references-->
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

<!-- Disconnected image references-->
[31]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[32]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[33]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[34]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[35]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[36]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[37]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[38]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[39]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[40]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[41]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[42]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[43]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[44]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[45]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[46]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[47]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[48]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
