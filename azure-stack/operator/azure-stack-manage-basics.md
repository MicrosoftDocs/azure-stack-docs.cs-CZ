---
title: Základy správy centra Azure Stack
description: Seznamte se se základy správy centra Azure Stack.
author: justinha
ms.topic: article
ms.date: 06/16/2020
ms.author: justinha
ms.lastreviewed: 06/16/2020
ms.openlocfilehash: b277eb9dcb1ac66af870db68c919e711aadc1ac7
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "94543506"
---
# <a name="azure-stack-hub-administration-basics"></a>Základy správy centra Azure Stack

Pokud s Azure Stack správy centra začínáte, je potřeba znát několik věcí. Tento článek poskytuje přehled vaší role jako operátor centra Azure Stack a to, co je potřeba k tomu, abychom svým uživatelům informovali, že jim pomohou zajistit jejich produktivitu.

## <a name="understand-the-builds"></a>Pochopení sestavení

### <a name="integrated-systems"></a>Integrované systémy

Pokud používáte integrovaný systém Azure Stack hub, balíčky aktualizací distribuují aktualizované verze centra Azure Stack. Tyto balíčky můžete importovat a použít pomocí dlaždice **aktualizace** na portálu pro správu.
 
### <a name="development-kit"></a>Vývojová sada

Pokud používáte Azure Stack Development Kit (ASDK), přečtěte si téma [co je Azure Stack centrum?](../asdk/asdk-what-is.md) , kde se dozvíte o účelu a omezeních ASDK. ASDK můžete použít jako *izolovaný prostor (sandbox)*, kde můžete vyhodnotit Azure Stack hub a vyvíjet a testovat aplikace v neprodukčním prostředí. Informace o nasazení najdete v tématu [nasazení Azure Stack Development Kit](../asdk/asdk-install.md).

Podobně jako u Azure provedeme rychlé inovace. Pravidelně vydáváme nová sestavení. Pokud používáte ASDK a chcete přejít na nejnovější sestavení, je nutné [znovu nasadit centrum Azure Stack](../asdk/asdk-redeploy.md). Nemůžete použít balíčky aktualizací. Tento proces trvá čas, ale výhodou je, že můžete vyzkoušet nejnovější funkce. Dokumentace ASDK na našem webu odráží nejnovější sestavení pro vydání.

## <a name="learn-about-available-services"></a>Další informace o dostupných službách

Budete potřebovat povědomí o tom, které služby můžete uživatelům zpřístupnit. Centrum Azure Stack podporuje podmnožinu služeb Azure. Seznam podporovaných služeb bude i nadále vyvíjet.

**Základní služby**

Ve výchozím nastavení zahrnuje Azure Stack hub při nasazení centra Azure Stack následující "základní služby":

- Compute
- Storage
- Sítě
- Key Vault

S těmito základními službami můžete uživatelům s minimální konfigurací nabídnout infrastrukturu jako službu (IaaS).

**Další služby**

V současné době podporujeme následující další služby PaaS (Platform as a Service):

- App Service
- Azure Functions
- Databáze SQL a MySQL
- Kubernetes (ve verzi Preview)

Tyto služby vyžadují další konfiguraci, abyste je mohli zpřístupnit uživatelům. Další informace najdete v částech "kurzy" a guides\Offer Services "v naší dokumentaci k operátorovi centra Azure Stack.

**Plán služby**

Centrum Azure Stack bude dál přidávat podporu pro služby Azure. Plánovaný plán najdete v tématu [centrum Azure Stack: rozšíření Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) White Paper. Můžete také monitorovat [příspěvky na blogu centra Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) pro nová oznámení.

## <a name="what-account-should-i-use"></a>Jaký účet mám použít?

Při správě centra Azure Stack je potřeba vědět o několika ohledech. Hlavně v nasazeních používá Windows Server Active Directory Federation Services (AD FS) (AD FS) jako zprostředkovatele identity místo Azure Active Directory (Azure AD). Následující požadavky týkající se účtu platí jak pro integrované systémy Azure Stack hub, tak pro nasazení ASDK:

|Účet|Azure AD|AD FS|
|-----|-----|-----|
|Místní správce (.\Administrator)|Správce hostitele ASDK|Správce hostitele ASDK|
|AzureStack\AzureStackAdmin|Správce hostitele ASDK<br><br>Dá se použít k přihlášení k portálu správce Azure Stackového centra.<br><br>Přístup k zobrazení a správě Service Fabricch okruhů.|Správce hostitele ASDK<br><br>K portálu správce centra Azure Stack nemáte přístup.<br><br>Přístup k zobrazení a správě Service Fabricch okruhů.<br><br>Již není vlastníkem předplatného výchozího poskytovatele (DPS).|
|AzureStack\CloudAdmin|Umožňuje přístup k povoleným příkazům v rámci privilegovaného koncového bodu a jejich spouštění.|Umožňuje přístup k povoleným příkazům v rámci privilegovaného koncového bodu a jejich spouštění.<br><br>Nejde se přihlásit k hostiteli ASDK.<br><br>Vlastník předplatného výchozího poskytovatele (DPS).|
|Globální správce Azure AD|Používá se během instalace.<br><br>Vlastník předplatného výchozího poskytovatele (DPS).|Neužívá se.|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jaké nástroje se používají ke správě?
 
Ke správě centra Azure Stack můžete použít [portál pro správu](azure-stack-manage-portals.md) nebo PowerShell. Nejjednodušší způsob, jak se naučit základní koncepty, je prostřednictvím portálu. Pokud chcete používat PowerShell, existují přípravné kroky. Než začnete, můžete se seznámit s tím, jak se prostředí PowerShell používá v Azure Stackovém centru. Další informace najdete v tématu [Začínáme s PowerShellem v centru Azure Stack](../user/azure-stack-powershell-overview.md).

Azure Stack centrum používá Azure Resource Manager jako základní mechanismus nasazení, správy a organizace. Pokud se chystáte spravovat centra Azure Stack a pomáhat uživatelům, můžete se dozvědět víc o Správce prostředků. Další informace najdete v [Začínáme Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) dokumentu White Paper.

## <a name="your-typical-responsibilities"></a>Vaše typické povinnosti

Vaši uživatelé chtějí používat služby. Z perspektivy je hlavním úkolem, aby jim byly dostupné tyto služby. Rozhodněte, které služby se mají nabízet, a zpřístupněte tyto služby vytvořením plánů, nabídek a kvót. Další informace najdete v tématu [Přehled nabízených služeb v centru Azure Stack](service-plan-offer-subscription-overview.md). 

Také budete muset přidat položky do [webu Azure Stack hub Marketplace](azure-stack-marketplace.md). Nejjednodušším způsobem je [stáhnout z Azure položky Marketplace do centra Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Pokud chcete testovat vaše plány, nabídky a služby, můžete použít [portál User Portal](azure-stack-manage-portals.md). Nejedná se o portál pro správu.

Kromě poskytování služeb je třeba provést běžné povinnosti operátora, aby bylo udržování služby Azure Stack hub v provozu. Mezi tyto funkce patří následující úlohy:

- Přidejte uživatelské účty (pro nasazení [Azure AD](azure-stack-add-new-user-aad.md) nebo nasazení [AD FS](azure-stack-add-users-adfs.md) ).
- [Přiřazení rolí řízení přístupu na základě role (RBAC)](azure-stack-manage-permissions.md) (Tato úloha není omezena na správce.)
- [Monitorování stavu infrastruktury](azure-stack-monitor-health.md)
- Správa prostředků [sítě](azure-stack-viewing-public-ip-address-consumption.md) a [úložiště](azure-stack-manage-storage-accounts.md)
- Nahraďte špatný hardware. Například [nahraďte disk, který selhal](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Co říct uživatelům

Budete muset uživatelům sdělit, jak pracovat se službami v centru Azure Stack, jak se připojit k prostředí a jak se přihlásit k odběru nabídek. Kromě jakékoli vlastní dokumentace, kterou byste mohli chtít poskytnout uživatelům, můžete uživatele nasměrovat na [Azure Stack dokumentaci pro uživatele centra](../user/index.yml).

**Informace o tom, jak pracovat se službami v centru Azure Stack**

Než uživatelé použijí služby a sestavují aplikace v centru Azure Stack, musí si uvědomit informace. Například existují konkrétní požadavky na PowerShell a verzi rozhraní API. Mezi službou v Azure a ekvivalentní službou ve službě Azure Stack hub jsou taky některé rozdíly ve funkcích. Ujistěte se, že uživatelé si přečtěte následující články:

- [Klíčové doporučení: použití služeb nebo sestavování aplikací pro centrum Azure Stack](../user/azure-stack-considerations.md)
- [Předpoklady pro Virtual Machines v centru Azure Stack](../user/azure-stack-vm-considerations.md)
- [Úložiště: rozdíly a požadavky](../user/azure-stack-acs-differences.md)

Informace v těchto článcích shrnuje rozdíly mezi službou v Azure a centra Azure Stack. Doplňuje informace, které jsou k dispozici pro službu Azure v globální dokumentaci k Azure.

**Připojení k Azure Stack centrum jako uživatel**

Pokud se v prostředí ASDK uživatel nepoužívá ke připojení k hostiteli ASDK Vzdálená plocha, může nakonfigurovat připojení virtuální privátní sítě (VPN) pro připojení k rozbočovači Azure Stack. Viz téma [připojit k centru Azure Stack](../asdk/asdk-connect.md).

Uživatelé budou chtít, abyste věděli, jak [získat přístup k portálu User Portal](../user/azure-stack-use-portal.md) nebo jak se připojit prostřednictvím PowerShellu. V prostředí integrovaných systémů se adresa uživatelského portálu liší podle nasazení. Musíte uživatelům poskytnout správnou adresu URL.

Pokud používáte PowerShell, uživatelé budou muset registrovat poskytovatele prostředků předtím, než budou moci používat služby. Poskytovatel prostředků spravuje službu. Poskytovatel síťových prostředků například spravuje prostředky, jako jsou virtuální sítě, síťová rozhraní a nástroje pro vyrovnávání zatížení. Musí si [nainstalovat](powershell-install-az-module.md) PowerShell, [Stáhnout](azure-stack-powershell-download.md) další moduly a [nakonfigurovat](../user/azure-stack-powershell-configure-user.md) PowerShell (který zahrnuje registraci poskytovatele prostředků).

**Přihlášení k odběru nabídky**

Předtím, než může uživatel použít služby, se musí [přihlásit k odběru nabídky](azure-stack-subscribe-plan-provision-vm.md) , kterou jste vytvořili jako operátor.

## <a name="where-to-get-support"></a>Kde získat podporu

> [!Note]  
> Informace o podpoře pro starší verze centra Azure Stack (pre-1905) najdete v tématu [pomoc a podpora pro starší verze Azure Stack centru (pre-1905)](azure-stack-servicing-policy.md).

### <a name="integrated-systems"></a>Integrované systémy

Pro integrovaný systém existuje koordinovaný proces eskalace a rozlišení mezi společností Microsoft a našimi hardwarovými partnery OEM (Original Equipment Manufacturer).

Pokud dojde k problému s cloudovou službou, podpora je poskytována prostřednictvím podpora Microsoftu. Chcete-li otevřít žádost o podporu, vyberte v pravém horním rohu portálu pro správu ikonu Nápověda a podpora (otazník). Pak v části **Podpora** vyberte **help + podpora** a pak **novou žádost o podporu** .

Pokud dojde k potížím s nasazením, opravou a aktualizací, hardwarem (včetně nahraditelných jednotek v poli) nebo jakýmkoli softwarem v hardwarovém prostředí, jako je třeba software spuštěný na hostiteli životního cyklu hardwaru, obraťte se na dodavatele hardwaru výrobce OEM.

U cokoli jiného kontaktujte podpora Microsoftu.

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack Development Kit (ASDK)

Pro ASDK se můžete zeptat na dotazy související s podporou na [fórech Microsoftu](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Pokud se chcete dostat do fóra, v pravém horním rohu portálu pro správu vyberte ikonu Nápověda a podpora (otazník), pak vyberte **Nápověda a podpora** a potom v části **Podpora** vyberte **fóra MSDN** . Tato fóra se pravidelně monitorují. Vzhledem k tomu, že ASDK je zkušební prostředí, není k dispozici žádná oficiální podpora prostřednictvím podpora Microsoftu.

## <a name="next-steps"></a>Další kroky

[Správa oblastí v centru Azure Stack](azure-stack-region-management.md)
