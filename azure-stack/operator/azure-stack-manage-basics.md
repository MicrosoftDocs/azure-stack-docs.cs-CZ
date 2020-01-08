---
title: Základy správy Azure Stack | Microsoft Docs
description: Seznamte se se základy správy Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.lastreviewed: 05/29/2019
ms.openlocfilehash: 32f82a832b150c4512c854d2ddbfc24941bf7228
ms.sourcegitcommit: 7626143e5d2a5e32a43162692f59306182fec854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2019
ms.locfileid: "75333052"
---
# <a name="azure-stack-administration-basics"></a>Základy správy Azure Stack

Pokud s Azure Stack administrativou nezačínáte, je třeba znát několik věcí. Tento článek poskytuje přehled vaší role jako operátor Azure Stack a to, co je potřeba k tomu, abychom svým uživatelům informovali, že jim pomohou zajistit jejich produktivitu.

## <a name="understand-the-builds"></a>Pochopení sestavení

### <a name="integrated-systems"></a>Integrované systémy

Pokud používáte Azure Stack integrovaný systém, balíčky aktualizací distribuují aktualizované verze Azure Stack. Tyto balíčky můžete importovat a použít pomocí dlaždice **aktualizace** na portálu pro správu.
 
### <a name="development-kit"></a>Vývojová sada

Pokud používáte Azure Stack Development Kit (ASDK), přečtěte si téma [co je Azure Stack?](../asdk/asdk-what-is.md) pro zjištění účelu a omezení ASDK. ASDK můžete použít jako *izolovaný prostor (sandbox)* , kde můžete vyhodnotit Azure Stack a vyvíjet a testovat aplikace v neprodukčním prostředí. Informace o nasazení najdete v tématu [nasazení Azure Stack Development Kit](../asdk/asdk-install.md).

Podobně jako u Azure provedeme rychlé inovace. Pravidelně vydáváme nová sestavení. Pokud používáte ASDK a chcete přejít na nejnovější sestavení, je nutné [znovu nasadit Azure Stack](../asdk/asdk-redeploy.md). Nemůžete použít balíčky aktualizací. Tento proces trvá čas, ale výhodou je, že můžete vyzkoušet nejnovější funkce. Dokumentace ASDK na našem webu odráží nejnovější sestavení pro vydání.

## <a name="learn-about-available-services"></a>Další informace o dostupných službách

Budete potřebovat povědomí o tom, které služby můžete uživatelům zpřístupnit. Azure Stack podporuje podmnožinu služeb Azure. Seznam podporovaných služeb bude i nadále vyvíjet.

**Základní služby**

Ve výchozím nastavení Azure Stack při nasazení Azure Stack zahrnovat následující "základní služby":

- Služby Compute
- Storage
- Sítě
- Key Vault

S těmito základními službami můžete uživatelům s minimální konfigurací nabídnout infrastrukturu jako službu (IaaS).

**Další služby**

V současné době podporujeme následující další služby PaaS (Platform as a Service):

- Aplikační služba
- Funkce Azure
- Databáze SQL a MySQL
- Kubernetes (ve verzi Preview)

Tyto služby vyžadují další konfiguraci, abyste je mohli zpřístupnit uživatelům. Další informace najdete v částech kurzy a guides\Offer služby v dokumentaci k operátorovi Azure Stack.

**Plán služby**

Azure Stack bude pokračovat v přidávání podpory pro služby Azure. Plánovaný plán najdete v tématu [Azure Stack: rozšíření Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) White Paper. Můžete také sledovat příspěvky na [blogu Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) pro nová oznámení.

## <a name="what-account-should-i-use"></a>Jaký účet mám použít?

Při správě Azure Stack je potřeba vědět o několika ohledech. Hlavně v nasazeních používá Windows Server Active Directory Federation Services (AD FS) (AD FS) jako zprostředkovatele identity místo Azure Active Directory (Azure AD). Následující požadavky na účet platí pro Azure Stack integrovaných systémů i nasazení ASDK:

|Účet|Azure AD|AD FS|
|-----|-----|-----|
|Místní správce (.\Administrator)|Správce hostitele ASDK|Správce hostitele ASDK|
|AzureStack\AzureStackAdmin|Správce hostitele ASDK<br><br>Dá se použít k přihlášení na portál správce Azure Stack.<br><br>Přístup k zobrazení a správě Service Fabricch okruhů.|Správce hostitele ASDK<br><br>Nemáte přístup k portálu Azure Stack pro správu.<br><br>Přístup k zobrazení a správě Service Fabricch okruhů.<br><br>Již není vlastníkem předplatného výchozího poskytovatele (DPS).|
|AzureStack\CloudAdmin|Umožňuje přístup k povoleným příkazům v rámci privilegovaného koncového bodu a jejich spouštění.|Umožňuje přístup k povoleným příkazům v rámci privilegovaného koncového bodu a jejich spouštění.<br><br>Nejde se přihlásit k hostiteli ASDK.<br><br>Vlastník předplatného výchozího poskytovatele (DPS).|
|Globální správce Azure AD|Používá se během instalace.<br><br>Vlastník předplatného výchozího poskytovatele (DPS).|Není k dispozici.|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jaké nástroje se používají ke správě?
 
Ke správě Azure Stack můžete použít [portál](azure-stack-manage-portals.md) pro správu nebo PowerShell. Nejjednodušší způsob, jak se naučit základní koncepty, je prostřednictvím portálu. Pokud chcete používat PowerShell, existují přípravné kroky. Než začnete, je vhodné se seznámit s tím, jak se prostředí PowerShell používá na Azure Stack. Další informace najdete v tématu [Začínáme s prostředím PowerShell v Azure Stack](../user/azure-stack-powershell-overview.md).

Azure Stack používá Azure Resource Manager jako základní mechanismus nasazení, správy a organizace. Pokud se chystáte spravovat Azure Stack a pomáhat uživatelům podpory, můžete se dozvědět víc o Správce prostředků. Další informace najdete v [Začínáme Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) dokumentu White Paper.

## <a name="your-typical-responsibilities"></a>Vaše typické povinnosti

Vaši uživatelé chtějí používat služby. Z perspektivy je hlavním úkolem, aby jim byly dostupné tyto služby. Rozhodněte, které služby se mají nabízet, a zpřístupněte tyto služby vytvořením plánů, nabídek a kvót. Další informace najdete v tématu [Přehled nabízených služeb v Azure Stack](service-plan-offer-subscription-overview.md). 

Budete také muset přidat položky do [Azure Stack Marketplace](azure-stack-marketplace.md). Nejjednodušším způsobem je [stáhnout z Azure položky Marketplace do Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Pokud chcete testovat vaše plány, nabídky a služby, můžete použít [portál User Portal](azure-stack-manage-portals.md). Nejedná se o portál pro správu.

Kromě poskytování služeb je potřeba, abyste měli k disAzure Stack pravidelné poplatky za operátora. Mezi tyto funkce patří následující úlohy:

- Přidejte uživatelské účty (pro nasazení [Azure AD](azure-stack-add-new-user-aad.md) nebo nasazení [AD FS](azure-stack-add-users-adfs.md) ).
- [Přiřazení rolí řízení přístupu na základě role (RBAC)](azure-stack-manage-permissions.md) (Tato úloha není omezena na správce.)
- [Monitorování stavu infrastruktury](azure-stack-monitor-health.md)
- Správa prostředků [sítě](azure-stack-viewing-public-ip-address-consumption.md) a [úložiště](azure-stack-manage-storage-accounts.md)
- Nahraďte špatný hardware. Například [nahraďte disk, který selhal](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Co říct uživatelům

Budete muset uživatelům sdělit, jak pracovat se službami v Azure Stack, jak se připojit k prostředí a jak se přihlásit k odběru nabídek. Kromě jakékoli vlastní dokumentace, kterou byste mohli chtít poskytnout uživatelům, můžete uživatele nasměrovat tak, aby [Azure Stack dokumentaci pro uživatele](/azure-stack/user/).

**Naučte se pracovat se službami v Azure Stack**

Aby uživatelé mohli používat služby a sestavovat aplikace v Azure Stack, musí si uvědomit informace. Například existují konkrétní požadavky na PowerShell a verzi rozhraní API. Mezi službou v Azure a ekvivalentní službou v Azure Stack taky existuje několik rozdílů funkcí. Ujistěte se, že uživatelé si přečtěte následující články:

- [Klíčové doporučení: použití služeb nebo sestavování aplikací pro Azure Stack](../user/azure-stack-considerations.md)
- [Předpoklady pro Virtual Machines v Azure Stack](../user/azure-stack-vm-considerations.md)
- [Úložiště: rozdíly a požadavky](../user/azure-stack-acs-differences.md)

Informace v těchto článcích shrnuje rozdíly mezi službou v Azure a Azure Stack. Doplňuje informace, které jsou k dispozici pro službu Azure v globální dokumentaci k Azure.

**Připojení k Azure Stack jako uživatel**

Pokud se v prostředí ASDK uživatel nepoužívá k připojení k hostiteli ASDK Vzdálená plocha, může nakonfigurovat připojení k virtuální privátní síti (VPN) pro připojení k Azure Stack. Viz [připojení k Azure Stack](../asdk/asdk-connect.md).

Uživatelé budou chtít, abyste věděli, jak [získat přístup k portálu User Portal](../user/azure-stack-use-portal.md) nebo jak se připojit prostřednictvím PowerShellu. V prostředí integrovaných systémů se adresa uživatelského portálu liší podle nasazení. Musíte uživatelům poskytnout správnou adresu URL.

Pokud používáte PowerShell, uživatelé budou muset registrovat poskytovatele prostředků předtím, než budou moci používat služby. Poskytovatel prostředků spravuje službu. Poskytovatel síťových prostředků například spravuje prostředky, jako jsou virtuální sítě, síťová rozhraní a nástroje pro vyrovnávání zatížení. Musí si [nainstalovat](azure-stack-powershell-install.md) PowerShell, [Stáhnout](azure-stack-powershell-download.md) další moduly a [nakonfigurovat](../user/azure-stack-powershell-configure-user.md) PowerShell (který zahrnuje registraci poskytovatele prostředků).

**Přihlášení k odběru nabídky**

Předtím, než může uživatel použít služby, se musí [přihlásit k odběru nabídky](azure-stack-subscribe-plan-provision-vm.md) , kterou jste vytvořili jako operátor.

## <a name="where-to-get-support"></a>Kde získat podporu

> [!Note]  
> Informace o podpoře pro dřívější verze Azure Stack (pre-1905) najdete v tématu [pomoc a podpora pro starší verze Azure Stack (pre-1905)](azure-stack-servicing-policy.md).

### <a name="integrated-systems"></a>Integrované systémy

Pro integrovaný systém existuje koordinovaný proces eskalace a rozlišení mezi společností Microsoft a našimi hardwarovými partnery OEM (Original Equipment Manufacturer).

Pokud dojde k problému s cloudovou službou, podpora je poskytována prostřednictvím služby Microsoft Customer Support Services (CSS). Chcete-li otevřít žádost o podporu, vyberte ikonu Nápověda a podpora (otazník) v pravém horním rohu portálu pro správu, vyberte možnost **Nápověda a podpora**a potom v části **Podpora** vyberte možnost **Nová žádost o podporu** .

Pokud dojde k potížím s nasazením, opravou a aktualizací, hardwarem (včetně nahraditelných jednotek v poli) nebo jakýmkoli softwarem v hardwarovém prostředí, jako je třeba software spuštěný na hostiteli životního cyklu hardwaru, obraťte se na dodavatele hardwaru výrobce OEM.

U cokoli jiného kontaktujte Microsoft CSS.

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack Development Kit (ASDK)

Pro ASDK se můžete zeptat na dotazy související s podporou na [fórech Microsoftu](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Pokud se chcete dostat do fóra, v pravém horním rohu portálu pro správu vyberte ikonu Nápověda a podpora (otazník), pak vyberte **Nápověda a podpora**a potom v části **Podpora** vyberte **fóra MSDN** . Tato fóra se pravidelně monitorují. Vzhledem k tomu, že ASDK je zkušební prostředí, není oficiální podpora nabídnuta prostřednictvím šablon stylů CSS společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

[Správa oblastí v Azure Stack](azure-stack-region-management.md)