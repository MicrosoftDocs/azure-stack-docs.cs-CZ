---
title: Nasazení aplikací na platformy Azure a Azure Stack
description: Naučte se nasazovat aplikace do Azure a Azure Stack pomocí hybridního kanálu CI/CD.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/23/2019
ms.topic: conceptual
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/07/2018
ms.openlocfilehash: 5357fcf548971e0962bec41ad9238bf88290531c
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603098"
---
# <a name="deploy-apps-to-azure-and-azure-stack"></a>Nasazení aplikací na platformy Azure a Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Toto řešení ukazuje, jak nasadit aplikace do Azure a Azure Stack pomocí Azure Pipelines hybridní průběžná integrace a průběžného doručování (CI/CD).

Až nastavíte Azure Stack, Azure DevOps a webové aplikace podle [požadavků](#prerequisites), můžete:

> [!div class="checklist"]
> - Zaregistrujte si webovou aplikaci a nastavte přístup k Azure Pipelines v Azure Active Directory (Azure AD). 
> - Vytvoření koncových bodů Azure Pipelines pro Azure AD a Active Directory Federation Services (AD FS) (AD FS). 
> - Na server sestavení Azure Stack nainstalujte Azure Pipelines agenta sestavení. 
> - Nakonfigurujte samoobslužné nasazení aplikací do Azure a Azure Stack.
> - Vytvořte kanál sestavení, který spustí automatické sestavení na základě potvrzení kódu pro váš projekt.
> - Vytvořte kanál pro vydávání verzí, který automaticky nasadí vaše sestavení do služby Azure AD i Azure Stack. 
> - Ruční vytvoření a nasazení verzí a zobrazení souhrnů a protokolů vydaných verzí 

Další informace o CI/CD najdete v následujících článcích:

* [Co je kontinuální integrace?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Co je průběžné doručování?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="azure-stack-and-hybrid-cicd"></a>Azure Stack a hybridní CI/CD

Microsoft Azure Stack je rozšířením Azure, které přináší flexibilitu a inovace cloud computingu do místního prostředí. Jedná se o jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace v místním prostředí i ve veřejných cloudových prostředích. Můžete vytvořit aplikaci na Azure Stack a pak ji nasadit do služby Azure Stack, do Azure nebo do hybridního cloudu Azure. 

Kontinuita nasazení aplikace, zabezpečení a spolehlivost jsou kritické prvky pro vaši organizaci a vývojový tým. Model doručování Azure Pipelines hybridní CI/CD umožňuje konsolidovat vaše kanály sestavení napříč místním prostředím a veřejným cloudem a měnit umístění nasazení beze změny aplikace. Další výhody použití hybridního přístupu:

- Můžete udržovat konzistentní sadu vývojářských nástrojů v rámci místního prostředí Azure Stack a veřejného cloudu Azure.  Společná sada nástrojů usnadňuje implementaci vzorů a postupů CI/CD.
- Aplikace a služby nasazené v Azure nebo Azure Stack jsou zaměnitelné a stejný kód může běžet v libovolném umístění. Můžete využívat možnosti a funkce místního a veřejného cloudu.

> [!TIP]
> ![hybridní pillars.png](./media/azure-stack-solution-pipeline/hybrid-pillars.png)  
> [Vzor návrhu hybridního cloudu pro Azure Stack](azure-stack-edge-pattern-overview.md) kontroluje pilíře kvality softwaru pro navrhování, nasazování a provozování hybridních aplikací. Mezi kritéria kvality patří umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení. Tyto otázky návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.

## <a name="prerequisites"></a>Požadavky

- Základní znalosti Azure a Azure Stack. Pokud chcete získat další informace před nasazením tohoto řešení, přečtěte si následující články:
  
  - [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
  - [Přehled Azure Stack](../operator/azure-stack-overview.md)
  
- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
  
- Webová aplikace vytvořená v Azure Pomocí [šablony Azure Resource Manager](https://azure.microsoft.com/resources/templates/) vytvoříte webovou aplikaci, kterou můžete nasadit v místním prostředí i ve veřejném cloudu. Poznamenejte si identifikátor URI aplikace pro pozdější použití. 
  
- Je nainstalovaná [](/visualstudio/install/install-visual-studio)aplikace Visual Studio 2019.
  
- Přístup správce k organizaci [Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) , která může vytvářet kanály a [projekt](/azure/devops/organizations/projects/create-project) DevOps nebo [pracovní prostor](/azure/devops/repos/tfvc/create-work-workspaces). 
  
- Bitová kopie systému Windows Server 2016 s rozhraním .NET 3,5 pro privátní virtuální počítač s agentem sestavení (VM) Azure Pipelines sestavení v Azure Stack.
  
- Azure Stack integrovaný systém nebo Azure Stack Development Kit (ASDK) nasazený a nakonfigurovaný podle následujících pokynů. 
  
  
  Azure Stack Development Kit (ASDK) je nasazení Azure Stack s jedním uzlem, které můžete zdarma stáhnout a používat. ASDK vám umožňuje vyhodnotit Azure Stack a používat rozhraní API a nástroje Azure v neprodukčním prostředí.
  
  ASDK může nasadit libovolný uživatel s přihlašovacími údaji správce Azure AD nebo Active Directory Federation Services (AD FS) (AD FS). Partner Azure pro výrobce OEM/hardware může nasadit produkční Azure Stack. Abyste mohli provádět následující úlohy konfigurace Azure Stack, musíte být Azure Stack operátorem: 
  
  - Nasazení Azure App Service
  - Vytváření plánů a nabídek
  - Vytvoření předplatného tenanta
  - Použití bitové kopie systému Windows Server 2016
  
  > [!Note]
  > Instalace ASDK trvá přibližně sedm hodin, takže odpovídajícím způsobem proveďte plán.
    
  Nasazení a konfigurace ASDK:
  
  1. Postupujte podle podrobných pokynů k nasazení v tématu [nasazení ASDK pomocí instalačního programu](../asdk/asdk-install.md).
     
  1. K automatizaci ASDK kroků po nasazení použijte skript prostředí PowerShell [ConfigASDK. ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) .
     
  1. Nasaďte [Azure App Service](../operator/azure-stack-app-service-deploy.md) služby PaaS a Azure Stack.
     
  1. Vytvořte [plán a nabídku](../operator/azure-stack-plan-offer-quota-overview.md) v Azure Stack.
     
  1. Vytvořte [předplatné tenanta](../operator/azure-stack-subscribe-plan-provision-vm.md) pro nabídku v Azure Stack. 
     
  1. Vytvořte webovou aplikaci v rámci předplatného tenanta. Poznamenejte si novou adresu URL webové aplikace, abyste ji mohli později použít.
     
  1. Nasaďte virtuální počítač s Windows serverem 2016 s .NET 3,5 v rámci předplatného tenanta na server sestavení, na kterém běží Azure Pipelines.
  
  > [!Note]
  > Prostředí Azure Stack potřebuje ke spuštění Windows serveru a SQL Server správné image. Musí mít taky nasazený App Service.

## <a name="register-your-web-app-and-give-it-access-to-resources"></a>Registrace webové aplikace a udělení přístupu k prostředkům 

Ve službě Azure Active Directory (Azure AD) Azure Pipelines ověřuje se Azure Resource Manager pomocí instančního *objektu*. Aby bylo možné zřídit prostředky pro Azure Pipelines, instanční objekt musí mít  roli přispěvatele v předplatném Azure. 

K nakonfigurování ověřování pro vaši aplikaci můžete použít Azure Portal. 

1. Pokud chcete vytvořit instanční objekt, Zaregistrujte svoji aplikaci.
1. Pomocí *řízení přístupu na základě role (RBAC)* udělte roli **Přispěvatel** hlavní název služby (SPN).
1. Zkopírujte a uložte hodnoty ID aplikace a ID tenanta, které potřebujete k vytvoření koncových bodů pro Azure Pipelines. 
1. Vytvoří a uloží hodnotu tajného klíče aplikace.

K vytvoření instančního objektu a koncových bodů můžete [použít také skript prostředí PowerShell](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) . Tento proces vysvětluje článek [vytvoření připojení služby Azure Resource Manager s existujícím objektem služby](/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) .

 > [!Note]  
 > Pokud k vytvoření Azure Stackho koncového bodu Azure Resource Manager použijete skript prostředí PowerShell, musíte předat parametr **-azureStackManagementURL** a parametr **-Environment** . Příklad:  
 > `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="register-your-app-in-azure-ad"></a>Registrace aplikace ve službě Azure AD 

1. V [Azure Portal](https://portal.azure.com)vyberte **Azure Active Directory**a v levém navigačním panelu vyberte **Registrace aplikací** .
   
1. Vyberte **Nová registrace**.
   
1. Na stránce **Registrovat aplikaci**
   1. Zadejte název vaší webové aplikace.
   1. Vyberte podporovaný typ účtu. 
   1. V části **identifikátor URI pro přesměrování**vyberte **Web** pro typ aplikace, kterou chcete vytvořit, a zadejte identifikátor URI vaší webové aplikace. 
   1. Vyberte **Zaregistrovat**.
      
      ![Zaregistrujte svoji aplikaci.](./media/azure-stack-solution-pipeline/create-app.png) 

### <a name="assign-the-app-to-a-role"></a>Přiřazení aplikace k roli

Pro přístup k prostředkům ve vašem předplatném musíte aplikaci přiřadit k roli. Azure RBAC umožňuje řídit úroveň přístupu, který uživatelé potřebují ke své práci. Další informace o RBAC najdete v tématu [Správa přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json). Další informace o dostupných rolích najdete v [tématu RBAC: Předdefinované role](/azure/role-based-access-control/built-in-roles).

Azure Pipelines musí mít roli **přispěvatele** možnost zřídit prostředky v předplatném Azure Stack. 

Rozsah rolí můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění se dědí do oboru na nižších úrovních. Například přidání aplikace do role **Čtenář** pro skupinu prostředků znamená, že aplikace může číst skupinu prostředků a všechny její prostředky.

Přiřazení aplikace k roli přispěvatele  :

1. V Azure Portal přejděte k požadované úrovni rozsahu. Pokud například chcete přiřadit roli v oboru předplatného, vyberte **všechny služby** a **odběry**.
   
   ![Přiřazení role v oboru předplatného](./media/azure-stack-solution-pipeline/select-subscription.png)
   
1. Vyberte předplatné, ke kterému chcete aplikaci přiřadit.
   
1. V levém navigačním panelu vyberte **řízení přístupu (IAM)** .
   
1. Vyberte **Přidat přiřazení role**.
   
1. V dialogovém okně **Přidat přiřazení role** vyberte roli **Přispěvatel** . Ve výchozím nastavení se aplikace Azure AD nezobrazí v dostupných možnostech. Chcete-li najít aplikaci, vyhledejte její název a vyberte ji.
   
   ![Vybrat roli a aplikaci](./media/azure-stack-solution-pipeline/select-role.png)
   
1. Vyberte **Uložit** k dokončení přiřazení role. Aplikaci si můžete prohlédnout v seznamu uživatelů přiřazených k roli pro tento obor.

Vaše instanční objekt je nastavený. V další části se dozvíte, jak získat hodnoty, které Azure Pipelines nutné přihlašovat programově.

### <a name="get-values-for-signing-in"></a>Získat hodnoty pro přihlášení

Při vytváření koncových bodů pro Azure Pipelines musíte zadat ID tenanta a ID aplikace. Získání těchto hodnot:

1. Na portálu Azure Portal vyberte **Azure Active Directory**.
   
1. V levém navigačním panelu vyberte **Registrace aplikací**a pak vyberte svou aplikaci.
   
1. Zkopírujte a uložte **ID adresáře (tenant)** a **ID aplikace (klienta)** , které se použije pro vytváření koncových bodů.
   
   ![Kopírování adresáře (ID tenanta) a ID aplikace (klienta)](./media/azure-stack-solution-pipeline/copy-app-id.png)

### <a name="create-a-new-application-secret"></a>Vytvořit nový tajný klíč aplikace

Při vytváření koncových bodů pro Azure Pipelines musí aplikace ověřovat. Může [použít certifikát](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) nebo tajný klíč aplikace. Pokud jste nasadili Azure Stack s AD FS jako poskytovatel identity, musíte použít certifikát.

Pomocí postupu v části [certifikáty a tajné](/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets) kódy vytvořte a nahrajte nový certifikát. 

Nebo, pokud chcete vytvořit nový tajný klíč aplikace:

1. Na portálu Azure Portal vyberte **Azure Active Directory**.
   
1. V levém navigačním panelu vyberte **Registrace aplikací**a pak vyberte svou aplikaci.
   
1. V levém navigačním panelu vyberte **certifikáty & tajných**kódů.
   
1. V části **tajné klíče klienta**vyberte **nový tajný klíč klienta**.
   
1. Do **Přidat tajný klíč klienta**zadejte popis, vyberte vypršení platnosti a vyberte **Přidat**.
   
1. Zkopírujte **hodnotu** nového tajného klíče. Abyste se mohli přihlásit jako aplikace, musíte zadat hodnotu. Tuto hodnotu je důležité uložit hned, protože po opuštění této stránky se znovu nezobrazí.
   
   ![Zkopírujte tajnou hodnotu, protože ji nemůžete později načíst.](./media/azure-stack-solution-pipeline/copy-secret.png)

## <a name="create-endpoints"></a>Vytváření koncových bodů

Vytvořením koncových bodů může Azure Pipelines Build nasazovat aplikace služby Azure AD, aby se Azure Stack. Azure Pipelines se připojí k agentu sestavení, který se připojí k Azure Stack.

Po nastavení oprávnění pro vytváření koncových bodů můžete vytvořit koncové body pro Azure AD nebo AD FS. 

- Pokud jste nasadili Azure Stack se službou Azure AD jako poskytovatel identity, můžete k vytvoření připojení služby Azure Resource Manager pro nasazení Azure použít certifikát nebo tajný kód aplikace. 
  
- Pokud jste jako poskytovatele identity pro Azure Stack použili Active Directory Federation Services (AD FS) (AD FS), musíte připojení služby vytvořit pomocí certifikátu, nikoli klientského klíče pro ověřování. 

### <a name="set-endpoint-creation-permissions"></a>Nastavení oprávnění pro vytváření koncových bodů

1. V organizaci a projektu Azure DevOps vyberte **nastavení projektu**. 
   
1. Vyberte **zabezpečení**a v části **skupiny DevOps Azure**vyberte **správce koncových bodů**.
   
1. Na kartě **Členové** vyberte **Přidat**.
   
1. V části **Přidat uživatele a skupiny**vyberte ze seznamu uživatelská jména, včetně sebe a pak vyberte **Uložit změny**.
   
   ![Přidat člena](./media/azure-stack-solution-pipeline/endpoint-permissions.png)
   
1. V seznamu **skupiny DevOps Azure** vyberte **autoři koncových bodů**a zopakováním předchozích kroků přidejte uživatele do skupiny Creators **Endpoint Creators** . 

### <a name="create-an-endpoint-for-azure-ad-or-ad-fs-deployments"></a>Vytvoření koncového bodu pro nasazení Azure AD nebo AD FS

Podle pokynů v části [vytvoření připojení služby Azure Resource Manager s existujícím objektem služby](/azure/devops/pipelines/library/connect-to-azure#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) vytvořte koncový bod připojení služby.  

K vyplnění formuláře použijte následující hodnoty: 

- **Název připojení**: Zadejte uživatelsky přívětivý název, který se má použít při odkazování na toto připojení služby.
  
- **Prostředí**: Vyberte název prostředí, například **AzureCloud** nebo **AzureStack**. Pokud v rozevíracím seznamu nevidíte AzureStack, přečtěte si téma [připojení k Azure Stack](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#connect-to-azure-stack).
  
- **Adresa URL prostředí**: Pokud jste nevybrali **AzureCloud**, zadejte adresu URL prostředí, například *https:\//Management.Local.azurestack.external*.
  
- **Úroveň oboru**: Vyberte úroveň rozsahu, kterou potřebujete, třeba **předplatné**. 
  
- **ID**předplatného: Zadejte ID předplatného.
  
- **Název**předplatného: Zadejte své uživatelské jméno z Azure Stack.
  
- **ID klienta instančního objektu**: Zadejte **ID aplikace (klienta)** , které jste předtím uložili. 
  
- Klíč nebo **certifikát** **instančního objektu služby** : Vyberte jednu nebo druhou možnost. 
  
  > [!NOTE]
  > Chcete-li vytvořit AD FS koncový bod, je nutné použít certifikát pro ověřování. 
  
  - V poli **klíč instančního objektu**zadejte hodnotu tajného klíče klienta, kterou jste uložili dříve.
  - Pokud zvolíte možnost **certifikát**, zadejte obsah části certifikátu a privátního klíče souboru certifikátu *. pem* . 
    
    > [!NOTE]
    > Chcete-li převést soubor *. pfx* na soubor certifikátu *. pem* , `openssl pkcs12 -in file.pfx -out file.pem -nodes -password pass:<password_here>`spusťte příkaz.
  
- **ID tenanta**: Zadejte **ID adresáře (tenanta)** , které jste předtím uložili.
  
- **Vázán Neověřeno**: Vyberte **ověřit připojení** a ověřte nastavení připojení k instančnímu objektu.
  
  > [!NOTE]
  > Pokud Váš koncový bod Azure Resource Manager není přístupný pro Internet, ověření připojení se nezdaří. Tato možnost je očekávaná a můžete ověřit připojení vytvořením kanálu vydání s jednoduchým úkolem.

## <a name="install-and-configure-the-build-agent"></a>Instalace a konfigurace agenta sestavení 

Použití hostovaného agenta sestavení v Azure Pipelines je pohodlnou možností pro sestavování a nasazování webových aplikací. Azure automaticky provádí údržbu a upgrady agenta, což umožňuje průběžné a nepřerušované vývojové cykly.

Ve službě Azure DevOps vytvořte osobní přístupový token (PAT), který se použije pro Azure Stack. Pak pomocí PAT nasaďte a nakonfigurujte agenta sestavení na virtuálním počítači Azure Stack sestavení serveru. 
   
### <a name="create-a-personal-access-token"></a>Vytvoření osobního přístupového tokenu

1. Přihlaste se k Azure DevOps a v pravém horním rohu vyberte **můj profil** . 
   
1. Na stránce vašeho profilu rozbalte rozevírací seznam vedle názvu vaší Azure Stack organizace a vyberte **Spravovat zabezpečení**. 
   
   ![Správa zabezpečení](media/azure-stack-solution-pipeline/managesecurity.png)
   
1. Na stránce **osobní přístupové tokeny** vyberte **Nový token**.
   
   ![Osobní přístupové tokeny](media/azure-stack-solution-pipeline/pats.png)
   
1. Na stránce **Vytvoření nového osobního přístupového tokenu** vyplňte informace o tokenu a vyberte **vytvořit**. 
   
   ![Vytvoření PAT](media/azure-stack-solution-pipeline/createpat.png)
   
1. Zkopírujte a uložte token. Po opuštění webové stránky se znovu nezobrazí.
   
   ![Upozornění PAT](media/azure-stack-solution-pipeline/patwarning.png)
   
### <a name="install-and-configure-the-agent-on-the-build-server"></a>Instalace a konfigurace agenta na serveru sestavení

1. Připojte se k virtuálnímu počítači sestavovacího serveru, který jste nasadili na Azure Stack hostitele.
   
1. Stáhněte si image agenta sestavení. 
   
1. V příkazovém řádku správce nasaďte agenta jako službu pomocí vaší PAT a spusťte ji.
   
1. Přejděte do složky pro extrahovaný agent sestavení a spusťte soubor *config. cmd* . Soubor *config. cmd* aktualizuje složku agenta sestavení pomocí dalších souborů.
   
   ![Instalace a konfigurace agenta sestavení](media/azure-stack-solution-pipeline/buildagent.png)

Nyní, když jste vytvořili koncový bod a nainstalovali Azure Pipelines agenta sestavení na serveru sestavení, je Azure Pipelines pro Azure Stack připojení připraveno k použití. Agent sestavení v Azure Stack získá pokyny od Azure Pipelines a potom agent přenáší informace koncového bodu pro komunikaci s Azure Stack.

Místo toho, aby se jednotliví agenti spravovali samostatně, můžete uspořádat agenty do *fondů agentů*. Fond agentů definuje hranice sdílení pro všechny agenty v tomto fondu. Fondy agentů jsou vymezeny na organizaci Azure DevOps, což znamená, že můžete sdílet fond agentů napříč projekty. Další informace o fondech agentů najdete v tématu [vytváření fondů agentů a front](/azure/devops/pipelines/agents/pools-queues).

## <a name="create-build-and-release-pipelines"></a>Vytváření kanálů sestavení a vydání 

Azure Pipelines poskytuje vysoce konfigurovatelný a spravovatelný kanál pro vydání do více prostředí, jako je vývoj, příprava, kontrola kvality (QA) a produkce. Proces vydání může zahrnovat vyžadování schválení v určitých fázích životního cyklu aplikace.

V této části řešení:

- Klonovat, připojit a přidat kód do projektu Azure DevOps v aplikaci Visual Studio.
- Vytvořte samoobslužné nasazení webové aplikace.
- Nakonfigurujte kanály sestavení a vydávání CI/CD.

Hybridní CI/CD může platit pro kód aplikace i kód infrastruktury. Pomocí [Azure Resource Manager Hybrid Templates](https://azure.microsoft.com/resources/templates/) můžete nasadit kód webové aplikace Azure do místních a veřejných cloudů.

### <a name="clone-your-project"></a>Klonovat projekt

1. V aplikaci Visual Studio **Team Explorer**vyberte ikonu **připojit** a přihlaste se ke svojí organizaci Azure DevOps. 
   
1. Vyberte **Spravovat připojení** > **připojit k projektu**. 
   
   ![Připojit se k projektu z Team Explorer](media/azure-stack-solution-pipeline/connecttoprojectteamexp.png)

1. V dialogovém okně **připojit k projektu** vyberte projekt webové aplikace, nastavte místní cestu a pak vyberte **klonovat** pro naklonování úložiště místně.
   
   ![Klonovat úložiště v připojení k projektu](media/azure-stack-solution-pipeline/cloneproject.png)

### <a name="create-a-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Vytvoření samoobslužného nasazení webové aplikace pro App Services v obou cloudech

1. V aplikaci Visual Studio **Průzkumník řešení**otevřete soubor *WebApplication. csproj* a přidejte `<RuntimeIdentifier>win10-x64</RuntimeIdentifier>`. Další informace o tomto kroku naleznete v části [samostatné nasazení](/dotnet/core/deploying/#self-contained-deployments-scd).
   
   ![Konfigurace RuntimeIdentifier](media/azure-stack-solution-pipeline/runtimeidentifier.png)
   
1. Uložte svou práci a použijte **Team Explorer** ke kontrole kódu v projektu.

### <a name="create-a-build-pipeline-and-run-a-build"></a>Vytvoření kanálu sestavení a spuštění sestavení

1. Ve webovém prohlížeči otevřete svoji organizaci a projekt Azure DevOps.
   
1. V levém navigačním panelu vyberte**sestavení** **kanálů** > a pak vyberte **Nový kanál**. 
   
1. V části **Vybrat šablonu**vyberte šablonu **ASP.NET Core** a pak vyberte **použít**. 
   
1. Na stránce konfigurace v levém podokně vyberte **publikovat** .
   
1. V pravém podokně v části **argumenty**přidejte `-r win10-x64` do konfigurace. 
   
   ![Přidat argument kanálu sestavení](media/azure-stack-solution-pipeline/buildargument.png)
   
1. V horní části stránky vyberte **uložit & frontu** .
   
1. V dialogovém okně **Spustit kanál** vyberte **Uložit a spustit**. 
   
[Sestavení samostatného nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) publikuje artefakty, které mohou běžet na platformě Azure i Azure Stack.

### <a name="create-a-release-pipeline"></a>Vytvoření kanálu verze

Vytvoření kanálu pro vydávání verzí je posledním krokem v procesu konfigurace hybridní CI/CD. Kanál pro vydávání verzí slouží k vytvoření vydání a nasazení sestavení.

1. V projektu Azure DevOps vyberte v levém navigačním panelu**vydané verze** **kanálů** > a pak vyberte **Nový kanál**. 
   
1. Na stránce **Vyberte šablonu** vyberte **Azure App Service nasazení**a pak vyberte **použít**.
   
   ![Výběr šablony vydané verze](media/azure-stack-solution-pipeline/releasetemplate.png)
   
1. Na kartě **kanál** vyberte **Přidat artefakt** v levém podokně. V pravém podokně vyberte sestavení webové aplikace, které jste právě vytvořili, z rozevírací nabídky **zdroj (kanál sestavení)** a vyberte **Přidat**.
   
   ![Přidejte artefakt sestavení](media/azure-stack-solution-pipeline/addartifact.png)
   
1. Na kartě **kanál** v části **fáze**vyberte hypertextový odkaz ve **fázi 1** a **Zobrazte úlohy fáze**.
   
   ![Zobrazit úlohy fáze](media/azure-stack-solution-pipeline/viewstagetasks.png)
   
1. Na kartě **úlohy** jako **název fáze**zadejte *Azure* . 
   
1. V části **parametry**vyberte vaše předplatné z rozevíracího seznamu **předplatné Azure** a zadejte **název služby App Service**.
   
   ![Vyberte předplatné a zadejte App Service název.](media/azure-stack-solution-pipeline/stage1.png)
   
1. V levém podokně vyberte **Spustit na agentovi**. V pravém podokně vyberte možnost **hostované VS2017** z rozevíracího seznamu **fond agentů** , pokud ještě není vybraná.
   
   ![Vybrat hostovaného agenta](media/azure-stack-solution-pipeline/agentjob.png)
   
1. V levém podokně vyberte **nasadit Azure App Service**a v pravém podokně vyhledejte **balíček nebo složku** pro sestavení webové aplikace Azure.
   
   ![Vybrat balíček nebo složku](media/azure-stack-solution-pipeline/packageorfolder.png)
   
1. V dialogovém okně **Vyberte soubor nebo složku** vyberte **OK**.
   
1. V pravém horním rohu stránky **Nový kanál verze** vyberte **Uložit** .
   
   ![Uložit změny](media/azure-stack-solution-pipeline/save-devops-icon.png)
   
1. Na kartě **kanál** vyberte **Přidat artefakt**. Vyberte svůj projekt a vyberte Azure Stack sestavení v rozevírací nabídce **zdroj (sestavení kanálu)** . Vyberte **Přidat**. 
   
1. Na kartě **kanál** v části **fáze**vyberte **Přidat**.
   
1. V nové fázi vyberte hypertextový odkaz pro **zobrazení úloh fáze**. Jako název fáze zadejte *Azure Stack* . 
   
   ![Zobrazit novou fázi](media/azure-stack-solution-pipeline/newstage.png)
   
1. V části **parametry**vyberte koncový bod Azure Stack a jako **název služby App Service**zadejte název vaší Azure Stack webové aplikace.
   
1. V části **Spustit na agentu**vyberte v rozevíracím seznamu **fond agentů** Azure Stack Server sestavovacího agenta.
   
1. Pro **Azure App Service nasazení**vyberte **balíček nebo složku** pro sestavení Azure Stack a pak v dialogovém okně **Vyberte soubor nebo složku** vyberte **OK** .
   
1. Na kartě **proměnné** Najděte proměnnou s názvem **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Nastavte hodnotu proměnné na **true**a nastavte její obor na **Azure Stack**.
   
   ![Konfigurovat proměnnou](media/azure-stack-solution-pipeline/setvariable.png)
   
1. Na kartě **kanál** vyberte pro každý artefakt ikonu **triggeru průběžného nasazování** a nastavte ji na **povoleno**.  
   
   ![Nastavit Trigger průběžného nasazování](media/azure-stack-solution-pipeline/contindeploymentenabled.png)
   
1. Vyberte ikonu **podmínky před nasazením** ve fázi Azure Stack a nastavte Trigger na **po vydání**.
   
   ![Nastavit aktivační událost pro podmínky předběžného nasazení](media/azure-stack-solution-pipeline/predeployafterrelease.png)
   
1. Vyberte **Uložit** v pravém horním rohu stránky **Nový kanál vydání** a uložte tak kanál pro vydávání verzí.

> [!Note]
> Některá nastavení pro úlohy vydání můžou být při vytvoření kanálu verze ze šablony automaticky definovaná jako [vlastní proměnné](/azure/devops/pipelines/release/variables?view=vsts#custom-variables) . Tato nastavení nelze upravovat v nastavení úlohy, ale můžete je upravit v položkách nadřazené fáze.

## <a name="release-the-app"></a>Uvolnění aplikace

Teď, když máte kanál verze, můžete ho použít k vytvoření vydání a nasazení aplikace. 

Vzhledem k tomu, že Trigger průběžného nasazování je nastaven v kanálu verze, změna zdrojového kódu spustí nové sestavení a automaticky vytvoří novou verzi. Tuto novou verzi ale vytvoříte a spustíte ručně.

Vytvoření a nasazení vydané verze:

1. Na stránce nový kanál verze vyberte **vytvořit vydání** v pravém horním rohu. 
   
   ![Vytvoření vydané verze ](media/azure-stack-solution-pipeline/createreleaseicon.png)
   
1. Na stránce **vytvořit novou verzi** :
   1. V části **kanál**vyberte fázi **Azure** , abyste změnili aktivační událost z automatizované na ruční. 
   1. V části artefakty se ujistěte, že jsou vybrané správné artefakty.
   1. Zadejte **Popis verze**a pak vyberte **vytvořit**. 
   
   Banner označuje, že se vytvořila nová verze. Kliknutím na odkaz název verze můžete zobrazit stránku souhrnu vydání s podrobnostmi o vydané verzi, jako je třeba stav nasazení.
   
1. Pokud chcete nasadit ruční vydání, vyberte fázi **Azure** , vyberte **nasadit**a pak v dialogovém okně fáze vyberte **nasadit** . 
   
1. Po úspěšném dokončení nasazení otevřete nasazenou aplikaci v prohlížeči. Například pro web Azure App Services otevřete adresu URL `https://<your-app-name>.azurewebsites.net`.

### <a name="monitor-and-track-releases"></a>Monitorování a sledování verzí

Pokud chcete zobrazit další informace o nasazení, můžete vybrat stav hypertextového odkazu ve fázi vydání. 

![Souhrnná stránka verze](media/azure-stack-solution-pipeline/releasesummary.png)

Správce může snadno sledovat celkový průběh vydaných verzí a zjistit, které verze čekají na schválení.

![Stránka souhrnu vydané verze se zobrazením nedokončeného schválení](media/azure-stack-solution-pipeline/releasepending.png)

Můžete si zobrazit protokoly vydaných verzí ze všech nasazení: 

1. V projektu Azure DevOps vyberte na levé straně vydaná**vydání** **kanálů** > a pak vyberte verzi. 
   
1. Na stránce souhrnu vydané verze umístěte ukazatel myši na nebo vyberte fázi a pak vyberte **protokoly**. 
   
   V protokolu verze v levém podokně se zobrazuje stav každé operace pro každou fázi. V pravém podokně se během nasazení zobrazuje živý protokol z agenta. Po dokončení nasazení se v pravém podokně zobrazí celý soubor protokolu. 
   
1. V levém podokně vyberte libovolný krok, abyste viděli soubor protokolu pro tento krok, například **schválení před nasazením**. 
   
1. Pokud chcete zobrazit schválení, v pravém podokně vyberte **Zobrazit schválení** , abyste viděli, kdo verzi schválil nebo zamítl, a další podrobnosti. 
   
Zobrazení protokolů jednotlivých kroků usnadňuje trasování a ladění částí celkového nasazení. **Všechny protokoly** si také můžete stáhnout jako soubor *. zip* .
   
![Protokol verze](media/azure-stack-solution-pipeline/releaselog.png)

## <a name="next-steps"></a>Další postup

Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](/azure/architecture/patterns).
