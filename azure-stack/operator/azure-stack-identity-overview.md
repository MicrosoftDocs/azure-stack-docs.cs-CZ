---
title: Přehled zprostředkovatelů identity pro centrum Azure Stack
description: Přečtěte si o poskytovatelích identity, které můžete použít s Azure Stack hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 896dc2b2def823a91278fe77062b20146a3c6976
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699690"
---
# <a name="overview-of-identity-providers-for-azure-stack-hub"></a>Přehled zprostředkovatelů identity pro centrum Azure Stack

Azure Stack hub vyžaduje Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS), kterou zajišťuje služba Active Directory jako zprostředkovatel identity. Volbou poskytovatele je jednorázové rozhodnutí, které uděláte při prvním nasazení centra Azure Stack. Koncepty a podrobnosti o autorizaci v tomto článku vám pomůžou vybrat mezi zprostředkovateli identity.

Vaše volba pro Azure AD nebo AD FS je určená režimem, ve kterém nasazujete Azure Stack hub:

- Když ho nasadíte v připojeném režimu, můžete použít buď službu Azure AD, nebo AD FS.
- Když ho nasadíte v odpojeném režimu bez připojení k Internetu, podporuje se jenom AD FS.

Další informace o možnostech, které závisí na prostředí centra Azure Stack, najdete v následujících článcích:

- Sada Azure Stack hub Deployment Kit: [požadavky na identitu](azure-stack-datacenter-integration.md#identity-considerations).
- Azure Stack integrovaných systémů centra: [rozhodnutí plánování nasazení pro integrované systémy Azure Stack hub](azure-stack-connection-models.md).

## <a name="common-concepts-for-identity-providers"></a>Běžné koncepty pro zprostředkovatele identity

V dalších částech se dozvíte o běžných konceptech zprostředkovatelů identity a jejich použití v Azure Stack hub.

![Terminologie pro zprostředkovatele identity](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>Klienti adresáře a organizace

Adresář je kontejner, který obsahuje informace o *uživatelích*, *aplikacích*, *skupinách*a *instančních objektech*.

Tenant adresáře je *organizace*, jako je například Microsoft nebo vaše společnost.

- Azure AD podporuje více tenantů a dokáže podporovat i více organizací ve vlastních adresářích. Pokud používáte Azure AD a máte více tenantů, můžete aplikacím a uživatelům udělit přístup z jednoho tenanta k ostatním klientům stejného adresáře.
- AD FS podporuje jenom jednoho tenanta, a proto jenom jednu organizaci.

### <a name="users-and-groups"></a>Uživatelé a skupiny

Uživatelské účty (identity) jsou standardní účty, které ověřují jednotlivce pomocí ID a hesla uživatele. Skupiny můžou zahrnovat uživatele nebo jiné skupiny.

Způsob vytváření a správy uživatelů a skupin závisí na používaném řešení identity.

V Azure Stackovém centru jsou uživatelské účty:

- Jsou vytvořeny ve formátu *uživatelského jména\@domény* . I když AD FS mapuje uživatelské účty k instanci služby Active Directory, AD FS nepodporuje použití *\\\<domény* >\\\<formátu aliasu >.
- Dá se nastavit tak, aby používal službu Multi-Factor Authentication.
- Jsou omezeny na adresář, ve kterém jsou poprvé registrována, což je adresář organizace.
- Dá se importovat z vašich místních adresářů. Další informace najdete v tématu [Integrace místních adresářů s Azure Active Directory](/azure/active-directory/connect/active-directory-aadconnect).

Když se přihlásíte k portálu User Portal vaší organizace, použijete adresu URL *https:\//Portal.Local.azurestack.external* . Při přihlašování k portálu centra Azure Stack z jiných domén, než je ta, která se používá k registraci Azure Stackho centra, musí být název domény, který se používá k registraci centra Azure Stack, připojený k adrese URL portálu. Pokud jste například Azure Stack centrum zaregistrovali v fabrikam.onmicrosoft.com a přihlašování k uživatelskému účtu je admin@contoso.com, adresa URL, která se má použít pro přihlášení k portálu User Portal, bude: https:\//Portal.Local.azurestack.external/Fabrikam.onmicrosoft.com.

### <a name="guest-users"></a>Uživatelé typu Host

Uživatelé typu Host jsou uživatelské účty z jiných tenantů adresářů, kterým byl udělen přístup k prostředkům ve vašem adresáři. Pokud chcete zajistit podporu pro uživatele typu Host, použijte službu Azure AD a povolte podporu pro víceklientské architektury. Pokud je povolená podpora, můžete pozvat uživatele typu Host, aby měli přístup k prostředkům ve vašem tenantovi adresáře, což zase umožní spolupráci s externími organizacemi.

K pozvání uživatelů typu Host můžou používat cloudové operátory a uživatelé [Azure AD spolupráci B2B](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Pozvaní uživatelé získají přístup k dokumentům, prostředkům a aplikacím z vašeho adresáře a Vy si udržujete kontrolu nad svými vlastními prostředky a daty.

Jako uživatel typu Host se můžete přihlásit k tenantovi adresáře jiné organizace. Uděláte to tak, že název adresáře této organizace připojíte k adrese URL portálu. Pokud například patříte do organizace contoso a chcete se přihlásit k adresáři Fabrikam, použijte protokol https:\//Portal.Local.azurestack.external/Fabrikam.onmicrosoft.com.

### <a name="apps"></a>Aplikace

Aplikace můžete registrovat do služby Azure AD nebo AD FS a pak je nabízet uživatelům ve vaší organizaci.

Mezi aplikace patří:

- **Webové aplikace**: příklady zahrnují Azure Portal a Azure Resource Manager. Podporují volání webového rozhraní API.
- **Nativní klient**: příklady zahrnují Azure PowerShell, Visual Studio a Azure CLI.

Aplikace můžou podporovat dva typy tenantů:

- **Jeden tenant**: podporuje uživatele a služby jenom ze stejného adresáře, ve kterém je aplikace zaregistrovaná.

  > [!NOTE]
  > Vzhledem k tomu, že AD FS podporuje jenom jeden adresář, aplikace, které vytvoříte v topologii AD FS, jsou navržené aplikacemi pro jednoho tenanta.

- **Víceklientské**rozhraní: podporuje použití uživateli a službami v adresáři, ve kterém je aplikace zaregistrovaná, a dalších adresářích tenanta. S aplikacemi pro více tenantů se můžou uživatelé jiného adresáře tenanta (jiný tenant Azure AD) přihlásit k vaší aplikaci.

  Další informace o víceklientské architektuře najdete v tématu [Povolení víceklientské architektury](azure-stack-enable-multitenancy.md).

  Další informace o vývoji aplikace s více klienty najdete v tématu [víceklientské aplikace](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview).

Při registraci aplikace vytvoříte dva objekty:

- **Objekt aplikace**: globální reprezentace aplikace napříč všemi klienty. Tento vztah se softwarovou aplikací používá a existuje jenom v adresáři, ve kterém se aplikace poprvé zaregistrovala.

- **Instanční objekt služby**: přihlašovací údaje, které se vytvoří pro aplikaci v adresáři, ve kterém se aplikace poprvé zaregistrovala. Instanční objekt se vytvoří také v adresáři každého dalšího tenanta, ve kterém se tato aplikace používá. Tento vztah může být v softwarové aplikaci jeden až mnoho.

Další informace o aplikacích a instančních objektech zabezpečení naleznete [v tématu Application and Service Principal Objects in Azure Active Directory](/azure/active-directory/develop/active-directory-application-objects).

### <a name="service-principals"></a>Instanční objekty

Instanční objekt je sada *přihlašovacích údajů* pro aplikaci nebo službu, která uděluje přístup k prostředkům v centru Azure Stack. Použití instančního objektu odděluje oprávnění aplikace od oprávnění uživatele k aplikaci.

V každém tenantovi, ve kterém se aplikace používá, se vytvoří instanční objekt. Instanční objekt vytváří identitu pro přihlašování a přístup k prostředkům (například uživatelům) zabezpečeným tímto klientem.

- Aplikace pro jednoho tenanta má jenom jeden instanční objekt, který je v adresáři, ve kterém se nejdřív vytvořil. Tento instanční objekt se vytvoří a pošle se k použití během registrace aplikace.
- Webová aplikace nebo rozhraní API pro více tenantů má instanční objekt, který je vytvořený v každém tenantovi, kde se uživatel z tohoto tenanta souhlasí s používáním aplikace.

Přihlašovací údaje pro instanční objekty můžou být buď klíč, který se generuje prostřednictvím Azure Portal nebo certifikátu. Použití certifikátu je vhodné pro automatizaci, protože certifikáty se považují za bezpečnější než klíče.

> [!NOTE]
> Když použijete AD FS s rozbočovačem Azure Stack, může vytvořit instanční objekty pouze správce. U AD FS instanční objekty vyžadují certifikáty a vytvářejí je prostřednictvím privilegovaného koncového bodu (PEP). Další informace najdete v tématu [použití identity aplikace pro přístup k prostředkům](azure-stack-create-service-principals.md).

Další informace o instančních objektech centra Azure Stack najdete v tématu [Vytvoření instančních objektů](azure-stack-create-service-principals.md).

### <a name="services"></a>Služby

Služby v centru Azure Stack, které komunikují se zprostředkovatelem identity, se registrují jako aplikace s poskytovatelem identity. Podobně jako aplikace registrace umožňuje službě ověřování pomocí systému identit.

Všechny služby Azure používají ke zřízení své identity protokoly [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) a [webové tokeny JSON](/azure/active-directory/develop/active-directory-token-and-claims) . Vzhledem k tomu, že Azure AD a AD FS používají protokoly konzistentně, můžete k ověřování místně nebo k Azure (v připojeném scénáři) použít [knihovnu služby Azure Active Directory Authentication Library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL). Pomocí ADAL můžete také používat nástroje, jako je Azure PowerShell a Azure CLI pro správu mezi cloudy a místními prostředky.

### <a name="identities-and-your-identity-system"></a>Identity a systém identit

Mezi identity služby Azure Stack hub patří uživatelské účty, skupiny a instanční objekty.

Když nainstalujete centrum Azure Stack, několik integrovaných aplikací a služeb se automaticky zaregistruje u vašeho poskytovatele identity v tenantovi adresáře. Některé služby, které se registrují, se používají ke správě. Pro uživatele jsou k dispozici další služby. Výchozí registrace poskytují identity Core Services, které mohou vzájemně komunikovat, a s identitami, které přidáte později.

Pokud nastavíte Azure AD s využitím víceklientské architektury, některé aplikace se rozšíří do nových adresářů.

## <a name="authentication-and-authorization"></a>Ověřování a autorizace

### <a name="authentication-by-apps-and-users"></a>Ověřování pomocí aplikací a uživatelů

![Identita mezi vrstvami centra Azure Stack](media/azure-stack-identity-overview/identity-layers.png)

Pro aplikace a uživatele je architektura centra Azure Stack popsaná čtyřmi vrstvami. Interakce mezi každou z těchto vrstev může používat různé typy ověřování.

|Vrstva    |Ověřování mezi vrstvami  |
|---------|---------|
|Nástroje a klienti, jako je například portál pro správu     | Chcete-li získat přístup k prostředku v Azure Stackovém centru, nástroje a klienti používají [JSON web token](/azure/active-directory/develop/active-directory-token-and-claims) k umístění volání Azure Resource Manager. <br>Azure Resource Manager ověří JSON Web Token a prohlédne *deklarace identity* v vystaveném tokenu, aby bylo možné odhadnout úroveň oprávnění, které má uživatel nebo instanční objekt v centru Azure Stack. |
|Azure Resource Manager a jeho základní služby     |Azure Resource Manager komunikuje s poskytovateli prostředků pro přenos komunikace od uživatelů. <br> Přenáší použití *přímých imperativních* volání nebo *deklarativních* volání prostřednictvím [šablon Azure Resource Manager](/azure-stack/user/azure-stack-arm-templates).|
|Poskytovatelé prostředků     |Volání předaná poskytovatelům prostředků jsou zabezpečena pomocí ověřování založeného na certifikátech. <br>Azure Resource Manager a poskytovatel prostředků pak zůstanou v komunikaci prostřednictvím rozhraní API. Pro každé volání, které je přijato od Azure Resource Manager poskytovatel prostředků ověřuje volání s tímto certifikátem.|
|Infrastruktura a obchodní logika     |Poskytovatelé prostředků komunikují s obchodní logikou a infrastrukturou pomocí režimu ověřování podle svého výběru. Výchozí poskytovatelé prostředků dodávajících Azure Stack hub používají k zabezpečení této komunikace ověřování systému Windows.|

![Informace potřebné k ověřování](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>Ověřování pro Azure Resource Manager

Chcete-li provést ověření u poskytovatele identity a získat JSON Web Token, je nutné mít následující informace:

1. **Adresa URL pro systém identity (autorita)** : adresa URL, na které je možné získat poskytovatele identity. Například *https:\//Login.Windows.NET*.
2. **Identifikátor URI ID aplikace pro Azure Resource Manager**: jedinečný identifikátor pro Azure Resource Manager zaregistrovaný u vašeho poskytovatele identity. Je také jedinečné pro každou instalaci centra Azure Stack.
3. **Přihlašovací údaje**: přihlašovací údaje, které používáte k ověření u poskytovatele identity.
4. **Adresa URL pro Azure Resource Manager**: adresa URL je umístění služby Azure Resource Manager. Například *https:\//Management.Azure.com* nebo *https:\//Management.Local.azurestack.external*.

Když objekt zabezpečení (klient, aplikace nebo uživatel) odešle požadavek na ověření k přístupu k prostředku, požadavek musí zahrnovat:

- Přihlašovací údaje objektu zabezpečení
- Identifikátor URI ID aplikace prostředku, k němuž chce získat přístup.

Přihlašovací údaje jsou ověřeny zprostředkovatelem identity. Zprostředkovatel identity taky ověří, že identifikátor URI ID aplikace je pro registrovanou aplikaci a že objekt zabezpečení má správná oprávnění k získání tokenu pro tento prostředek. Je-li požadavek platný, je udělen JSON Web Token.

Token se pak musí předat do hlavičky žádosti, aby se Azure Resource Manager. Azure Resource Manager provede následující akce bez konkrétního pořadí:

- Ověří deklaraci identity *vystavitele* (ISS), aby zkontrolovala, že token pochází od správného zprostředkovatele identity.
- Ověří deklaraci identity *cílové skupiny* (AUD), aby se ověřilo, že token byl vydán pro Azure Resource Manager.
- Ověří, jestli je JSON Web Token podepsaný certifikátem, který je nakonfigurovaný prostřednictvím OpenID a je známý pro Azure Resource Manager.
- Přečtěte si deklarace identity *vydaná v* (IAT) a *vypršení platnosti* (EXP) a potvrďte, že je token aktivní a který lze přijmout.

Po dokončení všech ověření Azure Resource Manager používá *ID objektu* (OID) a deklarace *skupin* k vytvoření seznamu prostředků, ke kterým má objekt zabezpečení přístup.

![Diagram protokolu výměny tokenů](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> Po nasazení se Azure Active Directory oprávnění globálního správce nevyžadují. Některé operace ale můžou vyžadovat přihlašovací údaje globálního správce (například skript instalačního programu poskytovatele prostředků nebo novou funkci, která vyžaduje udělení oprávnění). Můžete buď dočasně znovu vytvořit oprávnění globálního správce účtu, nebo použít samostatný účet globálního správce, který je vlastníkem *výchozího předplatného poskytovatele*.

### <a name="use-role-based-access-control"></a>Použití Access Control na základě rolí

Access Control na základě rolí (RBAC) v centru Azure Stack je konzistentní s implementací v Microsoft Azure. Přístup k prostředkům můžete spravovat přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím. Informace o tom, jak pomocí centra Azure Stack použít RBAC, najdete v následujících článcích:

- Začněte [s Access Control na základě rolí v Azure Portal](/azure/role-based-access-control/overview).
- Ke [správě přístupu k prostředkům předplatného Azure použijte Access Control na základě rolí](/azure/role-based-access-control/role-assignments-portal).
- [Vytvořte vlastní role pro Access Control na základě rolí Azure](/azure/role-based-access-control/custom-roles).
- [Spravujte Access Control na základě rolí](azure-stack-manage-permissions.md) v centru Azure Stack.

### <a name="authenticate-with-azure-powershell"></a>Ověřování s využitím Azure PowerShellu

Podrobnosti o používání Azure PowerShell k ověřování pomocí centra Azure Stack najdete v [části Konfigurace prostředí PowerShell pro Azure Stack uživatele centra](../user/azure-stack-powershell-configure-user.md).

### <a name="authenticate-with-azure-cli"></a>Ověřování pomocí Azure CLI

Informace o použití Azure PowerShell k ověřování pomocí centra Azure Stack najdete v tématu [instalace a konfigurace rozhraní příkazového řádku Azure CLI pro použití s Azure Stack hub](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="next-steps"></a>Další kroky

- [Architektura identity](azure-stack-identity-architecture.md)
- [Integrace Datacenter – identita](azure-stack-integrate-identity.md)
