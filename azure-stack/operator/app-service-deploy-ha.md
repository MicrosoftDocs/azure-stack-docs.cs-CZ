---
title: Nasazení App Service centra Azure Stack v konfiguraci s vysokou dostupností
description: Přečtěte si, jak nasadit App Service v centru Azure Stack pomocí vysoce dostupné konfigurace.
author: BryanLa
ms.topic: article
ms.date: 01/02/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/02/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: e6d8708b15d5402d16b17a722640c9bf0c2251c4
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848026"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Nasazení App Service v konfiguraci s vysokou dostupností

V tomto článku se dozvíte, jak používat položky webu Marketplace centra Azure Stack k nasazení App Service pro Azure Stack hub v konfiguraci s vysokou dostupností. K dostupným položkám Marketplace používá toto řešení také šablonu [AppService-\ Share-SQLServer-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack centra pro rychlý Start. Tato šablona automatizuje vytváření vysoce dostupné infrastruktury pro hostování poskytovatele prostředků App Service. App Service se pak nainstaluje na tuto infrastrukturu virtuálních počítačů s vysokou dostupností. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Nasazení virtuálních počítačů infrastruktury s vysokou dostupností App Service
Šablona pro rychlý Start [AppService-SQLServer-sqlserver](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack hub usnadňuje nasazení App Service v konfiguraci s vysokou dostupností. Měl by být nasazený v předplatném výchozího poskytovatele. 

Když se použije k vytvoření vlastního prostředku v centru Azure Stack, šablona vytvoří:
- Virtuální síť a požadované podsítě.
- Skupiny zabezpečení sítě pro podsítě souborového serveru, SQL Server a Active Directory Domain Services (služba AD DS).
- Účty úložiště pro disky virtuálních počítačů a určující Cloud clusteru
- Jeden interní nástroj pro vyrovnávání zatížení pro virtuální počítače SQL s privátní IP adresou, která je vázaná na naslouchací proces SQL AlwaysOn.
- Tři skupiny dostupnosti pro služba AD DS, cluster souborových serverů a cluster SQL.
- Cluster SQL se dvěma uzly.
- Cluster souborových serverů se dvěma uzly.
- Dva řadiče domény.

### <a name="required-azure-stack-hub-marketplace-items"></a>Požadované položky centra Azure Stack na webu Marketplace
Před použitím této šablony se ujistěte, že jsou ve vaší instanci centra Azure Stack k dispozici následující [položky Azure Stack centra pro Marketplace](azure-stack-marketplace-azure-items.md) :

- Základní image Windows serveru 2016 Datacenter (pro služba AD DS a virtuální počítače souborového serveru)
- SQL Server 2016 SP2 v systému Windows Server 2016 (Enterprise)
- Nejnovější rozšíření SQL IaaS 
- Nejnovější rozšíření konfigurace požadovaného stavu prostředí PowerShell 

> [!TIP]
> Další podrobnosti o požadavcích na šablony a výchozích hodnotách najdete v [souboru Readme pro šablonu](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) na GitHubu. 

### <a name="deploy-the-app-service-infrastructure"></a>Nasazení infrastruktury App Service
Pomocí kroků v této části vytvoříte vlastní nasazení pomocí šablony pro rychlý Start **AppService-\ Share-SQLServer-ha** Azure Stack hub.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **vlastní**a pak **template Deployment**.

   ![Nasazení vlastní šablony](media/app-service-deploy-ha/1.png)


3. V okně **vlastní nasazení** vyberte Upravit šablonu **Edit template** > **rychlý Start** šablony a potom pomocí rozevíracího seznamu dostupných vlastních šablon vyberte šablonu **AppService-\ Share-SQLServer-ha** . Klikněte na **OK**a pak na **Uložit**.

   ![Vyberte šablonu AppService-\ Share-SQLServer-ha.](media/app-service-deploy-ha/2.png)

4. V okně **vlastní nasazení** vyberte **Upravit parametry** a posuňte se dolů a zkontrolujte výchozí hodnoty šablon. Upravte tyto hodnoty podle potřeby, abyste zadali všechny požadované informace o parametrech, a pak klikněte na **OK**.<br><br> `ADMINPASSWORD`Pro parametry, `FILESHAREOWNERPASSWORD`, `FILESHAREUSERPASSWORD`, `SQLSERVERSERVICEACCOUNTPASSWORD`a `SQLLOGINPASSWORD` zadejte alespoň složitá hesla.
    
   ![Upravit parametry vlastního nasazení](media/app-service-deploy-ha/3.png)

5. V okně **vlastní nasazení** zajistěte, aby bylo vybráno **výchozí předplatné poskytovatele** jako předplatné, které chcete použít, a pak vytvořte novou skupinu prostředků, nebo pro vlastní nasazení vyberte existující skupinu prostředků.<br><br> V dalším kroku vyberte umístění skupiny prostředků (**místní** pro instalace ASDK) a potom klikněte na **vytvořit**. Vlastní nastavení nasazení se ověří před zahájením nasazení šablony.

    ![Vytvořit vlastní nasazení](media/app-service-deploy-ha/4.png)

6. Na portálu pro správu vyberte **skupiny prostředků** a potom název skupiny prostředků, kterou jste vytvořili pro vlastní nasazení (**App-Service-ha** v tomto příkladu). Zobrazte stav nasazení, aby se zajistilo, že se všechna nasazení úspěšně dokončila.

   > [!NOTE]
   > Dokončení nasazení šablony trvá přibližně hodinu.

   [![](media/app-service-deploy-ha/5-sm.png "Review template deployment status")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Zaznamenat výstupy šablony
Po úspěšném dokončení nasazení šablony zaznamenejte výstupy nasazení šablon. Tyto informace budete potřebovat při spuštění instalačního programu App Service.

Ujistěte se, že jste zaznamenali všechny tyto výstupní hodnoty:
- FileSharePath
- Přihlašovací
- Přihlašovací
- Hostitel
- SQLuser

Pomocí těchto kroků zjistíte výstupní hodnoty šablony:

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Na portálu pro správu vyberte **skupiny prostředků** a potom název skupiny prostředků, kterou jste vytvořili pro vlastní nasazení (**App-Service-ha** v tomto příkladu). 

3. Klikněte na **nasazení** a vyberte **Microsoft. template**.

    ![Microsoft. Nasazení šablon](media/app-service-deploy-ha/6.png)

4. Po výběru nasazení **Microsoft. template** vyberte **výstupy** a zaznamenejte výstup parametrů šablony. Tyto informace jsou požadovány při nasazení App Service.

    ![Výstup parametru](media/app-service-deploy-ha/7.png)

## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Nasazení App Service v konfiguraci s vysokou dostupností
Postupujte podle kroků v této části a nasaďte App Service pro Azure Stack centrum v konfiguraci s vysokou dostupností na základě šablony pro rychlý Start pro [AppService-soubor Share-SQLServer-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) Azure Stack centra. 

Až nainstalujete poskytovatele prostředků App Service, můžete ho zahrnout do nabídek a plánů. Uživatelé se pak můžou přihlásit k odběru služby a začít vytvářet aplikace.

> [!IMPORTANT]
> Před spuštěním instalačního programu poskytovatele prostředků se ujistěte, že jste si přečetli poznámky k verzi, které jsou součástí každé App Service vydané verze, a získejte informace o nových funkcích, opravách a známých problémech, které by mohly mít vliv na nasazení.

### <a name="prerequisites"></a>Požadavky
Před spuštěním instalačního programu App Service je potřeba provést několik kroků, jak je popsáno v části, než začnete [s App Service v článku Azure Stack centra](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> Ne všechny kroky popsané v části [než začnete s App Service článkem](azure-stack-app-service-before-you-get-started.md) jsou vyžadovány, protože nasazení šablony nakonfiguruje virtuální počítače infrastruktury za vás.

- [Stáhněte si instalační program a skripty pro pomoc s App Service](azure-stack-app-service-before-you-get-started.md#installer-and-helper-scripts).
- [Stažení položek z Marketplace centra Azure Stack](azure-stack-app-service-before-you-get-started.md#download-items-from-the-azure-marketplace).
- [Vygenerujte požadované certifikáty pro typ nasazení](azure-stack-app-service-before-you-get-started.md).
- Vytvořte aplikaci ID na základě poskytovatele identifikace, kterého jste zvolili pro Azure Stack hub. Pro [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-ad-app) nebo [Active Directory Federation Services (AD FS)](azure-stack-app-service-before-you-get-started.md#create-an-adfs-app) lze vytvořit aplikaci s ID a zaznamenat ID aplikace.
<!-- Connected --->
::: zone pivot="state-connected"
- Ujistěte se, že jste přidali image Windows serveru 2016 Datacenter do Marketplace centra Azure Stack. Tato image se vyžaduje pro App Service instalaci.
::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->
- **Windows Server 2016 Datacenter Full VM Image s aktivovanou Microsoft.NET 3.5.1 SP1**  Azure App Service v centru Azure Stack vyžaduje, aby byla na imagi používané k nasazení aktivována součást Microsoft .NET 3.5.1 SP1. Tržiště: v předdefinovaných bitových kopiích Windows serveru 2016 není tato funkce povolená a v odpojených prostředích není možné získat přístup Microsoft Update ke stažení balíčků pro instalaci prostřednictvím DISM. Proto je nutné vytvořit a použít bitovou kopii systému Windows Server 2016 s touto funkcí, která je předem povolena s odpojeným nasazením.

   Podrobnosti o vytvoření vlastní image a přidání do Marketplace najdete v tématu [Přidání vlastní image virtuálního počítače do centra Azure Stack](azure-stack-add-vm-image.md) . Při přidávání image do Marketplace Nezapomeňte zadat následující vlastnosti:

   - Vydavatel = MicrosoftWindowsServer
   - Nabídka = WindowsServer
   - SKU = 2016 – Datacenter
   - Verze = zadejte nejnovější verzi

::: zone-end

<!-- For All --> 

### <a name="steps-for-app-service-deployment"></a>Postup nasazení App Service
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

<!-- Connected --->
::: zone pivot="state-connected"
Pokud chcete nasadit poskytovatele prostředků App Service, použijte následující postup:

1. Spusťte dříve stažený instalační program App Service (**AppService. exe**) jako správce z počítače, který má přístup ke koncovému bodu správy prostředků Azure pro správu centra Azure Stack.

2. Vyberte **nasadit App Service nebo upgradujte na nejnovější verzi**.

    ![Nasazení nebo upgrade App Service](media/app-service-deploy-ha/01.png)

3. Přijměte licenční podmínky společnosti Microsoft a klikněte na tlačítko **Další**.

    ![Licenční podmínky společnosti Microsoft týkající se App Service](media/app-service-deploy-ha/02.png)

4. Přijměte licenční podmínky jiné společnosti než Microsoft a klikněte na tlačítko **Další**.

    ![Licenční podmínky od jiného výrobce než Microsoft App Service](media/app-service-deploy-ha/03.png)

5. Zadejte konfiguraci cloudového koncového bodu App Service pro vaše prostředí Azure Stackového centra.

    ![App Service konfigurace koncového bodu cloudu v App Service](media/app-service-deploy-ha/04.png)

6. **Připojte** se k předplatnému centra Azure Stack, které se má použít pro instalaci, a vyberte umístění. 

    ![Připojení k předplatnému centra Azure Stack v App Service](media/app-service-deploy-ha/05.png)

7. Vyberte možnost **použít existující virtuální síť a podsítě** a **název skupiny prostředků** pro skupinu prostředků použitou k nasazení šablony s vysokou dostupností.<br><br>Potom vyberte virtuální síť vytvořenou jako součást nasazení šablony a pak z možností rozevíracího seznamu vyberte příslušné podsítě rolí. 

    ![Výběr virtuální sítě na App Service](media/app-service-deploy-ha/06.png)

8. Poskytněte dříve zaznamenané informace o výstupech šablony pro cestu ke sdílené složce a parametry vlastníka sdílené složky. Jakmile budete hotovi, klikněte na tlačítko **Další**.

    ![Informace o výstupu sdílení souborů v App Service](media/app-service-deploy-ha/07.png)

9. Vzhledem k tomu, že počítač použitý k instalaci App Service není umístěn ve stejné virtuální síti jako souborový server, který je hostitelem App Service sdílené složky, nemůžete tento název přeložit. **Tato chyba je očekávané chování**.<br><br>Ověřte správnost informací zadaných pro cestu UNC ke sdílené složce a informace o účtech. Pak pokračujte v instalaci App Service kliknutím na tlačítko **Ano** v dialogovém okně Výstraha.

    ![V App Service se očekávalo chybové dialogové okno.](media/app-service-deploy-ha/08.png)

10. Zadejte ID aplikace identity a cestu a hesla k certifikátům identity a klikněte na **Další**:
    - Certifikát aplikace identity (ve formátu **SSO. AppService. Local. azurestack. external. pfx**)
    - Azure Resource Manager kořenový certifikát (**AzureStackCertificationAuthority. cer**)

    ![ID certifikátu aplikace a kořenového certifikátu na App Service](media/app-service-deploy-ha/008.png)

11. Potom zadejte zbývající požadované informace pro následující certifikáty a klikněte na **Další**:
    - Výchozí Azure Stack certifikát SSL centra (ve formátu **_. AppService. Local. azurestack. external. pfx**)
    - Certifikát SSL rozhraní API (ve formátu **rozhraní API. AppService. Local. azurestack. external. pfx**)
    - Certifikát vydavatele (ve formě **FTP. AppService. Local. azurestack. external. pfx**) 

    ![Další konfigurační certifikáty pro App Service](media/app-service-deploy-ha/09.png)

12. Zadejte informace o SQL Server připojení pomocí informací o připojení SQL Server z výstupů nasazení šablony vysoké dostupnosti:

    ![SQL Server informace o připojení App Service](media/app-service-deploy-ha/10.png)

13. Vzhledem k tomu, že počítač použitý k instalaci App Service není umístěný ve stejné virtuální síti jako SQL Server, který se používá k hostování App Servicech databází, nemůžete tento název přeložit.  **Toto chování je očekávané**.<br><br>Ověřte, zda jsou zadané informace pro SQL Server název a účty správné, a stisknutím klávesy **Ano** pokračujte v instalaci App Service. Klikněte na **Další**.

    ![SQL Server informace o připojení App Service](media/app-service-deploy-ha/11.png)

14. Přijměte výchozí hodnoty konfigurace role, nebo se změňte na Doporučené hodnoty a klikněte na **Další**.<br><br>Pro konfigurace s vysokou dostupností doporučujeme, aby se výchozí hodnoty pro instance rolí infrastruktury App Service změnily takto:

    |Role|Výchozí|Doporučení s vysokou dostupností|
    |-----|-----|-----|
    |Role kontroleru|2|2|
    |Role správy|1|3|
    |Role vydavatele|1|3|
    |Role front-endu|1|3|
    |Role sdíleného pracovního procesu|1|2|
    |     |     |     |

    ![Hodnoty instance role infrastruktury na App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Změnou výchozích hodnot na ty, které se doporučují v tomto Tutoral, se zvýší požadavky na hardware pro instalaci App Service. Pro podporu doporučených virtuálních počítačů, nikoli výchozích 9 jader a 16 128 MB paměti RAM pro 6 virtuálních počítačů, je potřeba celkem 18 jader a 32 256 MB paměti RAM.

15. Vyberte image platformy, která se má použít pro instalaci virtuálních počítačů infrastruktury App Service, a klikněte na **Další**:

    ![Výběr image platformy na App Service](media/app-service-deploy-ha/13.png)

16. Zadejte informace o přihlašovacích údajích role App Service infrastruktury, které se mají použít, a klikněte na **Další**:

    ![Přihlašovací údaje role infrastruktury na App Service](media/app-service-deploy-ha/14.png)

17. Přečtěte si informace, které se mají použít k nasazení App Service, a kliknutím na **Další** zahajte nasazení.

    ![Kontrola souhrnu instalace App Service](media/app-service-deploy-ha/15.png)

18. Projděte si postup nasazení App Service. Toto nasazení může trvat hodinu v závislosti na konkrétní konfiguraci nasazení a hardwaru. Po úspěšném dokončení instalačního programu vyberte možnost **ukončit**.

    ![Instalace byla dokončena pro App Service](media/app-service-deploy-ha/16.png)

::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->

## <a name="create-an-offline-installation-package"></a>Vytvoření instalačního balíčku offline

Pokud chcete nasadit Azure App Service v offline prostředí, nejdřív vytvořte instalační balíček offline na počítači, který je připojený k Internetu.

1. Spusťte instalační program AppService. exe na počítači, který je připojený k Internetu. 

1. Vyberte **Upřesnit** > **vytvořit balíček pro offline instalaci**. Dokončení tohoto kroku bude trvat několik minut.

    ! [Vytvoření offline balíčku v instalačním programu Azure App Service] čl

1. Instalační služba Azure App Service vytvoří instalační balíček offline a zobrazí cestu k němu. Můžete vybrat **Otevřít složku** a otevřít složku v Průzkumníku souborů.

    ![Instalační balíček offline se úspěšně vygeneroval v instalačním programu Azure App Service](media/azure-stack-app-service-deploy-offline/image02.png)

1. Zkopírujte instalační program (AppService. exe) a instalační balíček offline na počítač, který má připojení k vašemu centru Azure Stack.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Dokončení offline instalace Azure App Service v centru Azure Stack

1. Spusťte AppService. exe jako správce z počítače, který se může připojit ke koncovému bodu správy prostředků Azure pro správu centra Azure Stack.

1. Vyberte **Upřesnit** > **kompletní offline instalaci**.

    ! [Dokončení offline instalace v instalačním programu Azure App Service] [32]

1. Přejděte do umístění instalačního balíčku offline, který jste vytvořili dříve, a pak vyberte **Další**.

    ![Zadejte cestu k instalačnímu balíčku offline im Azure App Service instalační program](media/azure-stack-app-service-deploy-offline/image04.png)

1. Přijměte licenční podmínky společnosti Microsoft a klikněte na tlačítko **Další**.

    ![Licenční podmínky společnosti Microsoft týkající se App Service](media/app-service-deploy-ha/02.png)

1. Přijměte licenční podmínky jiné společnosti než Microsoft a klikněte na tlačítko **Další**.

    ![Licenční podmínky od jiného výrobce než Microsoft App Service](media/app-service-deploy-ha/03.png)

1. Zadejte konfiguraci cloudového koncového bodu App Service pro vaše prostředí Azure Stackového centra.

    ![App Service konfigurace koncového bodu cloudu v App Service](media/app-service-deploy-ha/04.png)

1. **Připojte** se k předplatnému centra Azure Stack, které se má použít pro instalaci, a vyberte umístění. 

    ![Připojení k předplatnému centra Azure Stack v App Service](media/app-service-deploy-ha/05.png)

1. Vyberte možnost **použít existující virtuální síť a podsítě** a **název skupiny prostředků** pro skupinu prostředků použitou k nasazení šablony s vysokou dostupností.  Potom vyberte virtuální síť vytvořenou jako součást nasazení šablony a pak z možností rozevíracího seznamu vyberte příslušné podsítě rolí. 

    ![Výběr virtuální sítě na App Service](media/app-service-deploy-ha/06.png)

1. Poskytněte dříve zaznamenané informace o výstupech šablony pro cestu ke sdílené složce a parametry vlastníka sdílené složky. Jakmile budete hotovi, klikněte na tlačítko **Další**.

    ![Informace o výstupu sdílení souborů v App Service](media/app-service-deploy-ha/07.png)

1. Vzhledem k tomu, že počítač použitý k instalaci App Service není umístěn ve stejné virtuální síti jako souborový server, který je hostitelem App Service sdílené složky, nemůžete tento název přeložit. **Tato chyba je očekávané chování**.  Ověřte správnost informací zadaných pro cestu UNC ke sdílené složce a informace o účtech. Pak pokračujte v instalaci App Service kliknutím na tlačítko **Ano** v dialogovém okně Výstraha.

    ![V App Service se očekávalo chybové dialogové okno.](media/app-service-deploy-ha/08.png)

1. Zadejte ID aplikace identity a cestu a hesla k certifikátům identity a klikněte na **Další**:
    - Certifikát aplikace identity (ve formátu **SSO. AppService. Local. azurestack. external. pfx**)
    - Azure Resource Manager kořenový certifikát (**AzureStackCertificationAuthority. cer**)

    ![ID certifikátu aplikace a kořenového certifikátu na App Service](media/app-service-deploy-ha/008.png)

1. Potom zadejte zbývající požadované informace pro následující certifikáty a klikněte na **Další**:
    - Výchozí Azure Stack certifikát SSL centra (ve formátu **_. AppService. Local. azurestack. external. pfx**)
    - Certifikát SSL rozhraní API (ve formátu **rozhraní API. AppService. Local. azurestack. external. pfx**)
    - Certifikát vydavatele (ve formě **FTP. AppService. Local. azurestack. external. pfx**) 

    ![Další konfigurační certifikáty pro App Service](media/app-service-deploy-ha/09.png)

1. Zadejte informace o SQL Server připojení pomocí informací o připojení SQL Server z výstupů nasazení šablony vysoké dostupnosti:

    ![SQL Server informace o připojení App Service](media/app-service-deploy-ha/10.png)

1. Vzhledem k tomu, že počítač použitý k instalaci App Service není umístěný ve stejné virtuální síti jako SQL Server, který se používá k hostování App Servicech databází, nemůžete tento název přeložit.  **Toto chování je očekávané**.  Ověřte, zda jsou zadané informace pro SQL Server název a účty správné, a stisknutím klávesy **Ano** pokračujte v instalaci App Service. Klikněte na **Další**.

    ![SQL Server informace o připojení App Service](media/app-service-deploy-ha/11.png)

1. Přijměte výchozí hodnoty konfigurace role, nebo se změňte na Doporučené hodnoty a klikněte na **Další**.  Pro konfigurace s vysokou dostupností doporučujeme, aby se výchozí hodnoty pro instance rolí infrastruktury App Service změnily takto:

    |Role|Výchozí|Doporučení s vysokou dostupností|
    |-----|-----|-----|
    |Role kontroleru|2|2|
    |Role správy|1|3|
    |Role vydavatele|1|3|
    |Role front-endu|1|3|
    |Role sdíleného pracovního procesu|1|2|
    |     |     |     |

    ![Hodnoty instance role infrastruktury na App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Změnou výchozích hodnot na ty, které se doporučují v tomto Tutoral, se zvýší požadavky na hardware pro instalaci App Service. Pro podporu doporučených virtuálních počítačů, nikoli výchozích 9 jader a 16 128 MB paměti RAM pro 6 virtuálních počítačů, je potřeba celkem 18 jader a 32 256 MB paměti RAM.

1. Vyberte image platformy, která se má použít pro instalaci virtuálních počítačů infrastruktury App Service, a klikněte na **Další**:

    ![Výběr image platformy na App Service](media/app-service-deploy-ha/13.png)

1. Zadejte informace o přihlašovacích údajích role App Service infrastruktury, které se mají použít, a klikněte na **Další**:

    ![Přihlašovací údaje role infrastruktury na App Service](media/app-service-deploy-ha/14.png)

1. Přečtěte si informace, které se mají použít k nasazení App Service, a kliknutím na **Další** zahajte nasazení.

    ![Kontrola souhrnu instalace App Service](media/app-service-deploy-ha/15.png)

1. Projděte si postup nasazení App Service. Toto nasazení může trvat hodinu v závislosti na konkrétní konfiguraci nasazení a hardwaru. Po úspěšném dokončení instalačního programu vyberte možnost **ukončit**.

    ![Instalace byla dokončena pro App Service](media/app-service-deploy-ha/16.png)

::: zone-end

## <a name="post-deployment-steps"></a>Kroky po nasazení

1. Je **nutné** [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database). Je také nutné synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

2. Nasadili jste App Service do existující virtuální sítě a pomocí interní IP adresy se připojíte k souborovému serveru, **musíte** přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete do skupiny zabezpečení sítě WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

- Zdroj: Any
- Rozsah zdrojových portů: *
- Cíl: IP adresy
- Rozsah cílových IP adres: rozsah IP adres pro souborový server
- Rozsah cílových portů: 445
- Protokol. TCP
- Akce: Povolit
- Priorita: 700
- Název: Outbound_Allow_SMB445

## <a name="next-steps"></a>Další kroky

Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, [přidejte databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) . Synchronizujte databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze. Můžete také spustit [skript](https://blog.sqlauthority.com/2017/11/30/sql-server-alwayson-availability-groups-script-sync-logins-replicas/) pro import přihlášení aplikační služby z původního primárního serveru na server pro převzetí služeb při selhání.

Horizontální navýšení [kapacity App Service](azure-stack-app-service-add-worker-roles.md). Je možné, že budete muset přidat další pracovní pracovníky role App Service, abyste splnili očekávanou poptávku aplikace ve vašem prostředí. Ve výchozím nastavení App Service v centru Azure Stack podporuje bezplatné a sdílené úrovně pracovních procesů. Chcete-li přidat další vrstvy pracovního procesu, je nutné přidat další role pracovního procesu.

[Nakonfigurujte zdroje nasazení](azure-stack-app-service-configure-deployment-sources.md). K podpoře nasazení na vyžádání od více poskytovatelů správy zdrojového kódu, jako jsou GitHub, BitBucket, OneDrive a DropBox, se vyžaduje další konfigurace.

[Zálohování App Service](app-service-back-up.md). Po úspěšném nasazení a konfiguraci App Service byste měli zajistit zálohování všech komponent nezbytných pro zotavení po havárii. Zálohování základních součástí pomáhá zabránit ztrátě dat a zbytečnému výpadku služeb během operací obnovení.
