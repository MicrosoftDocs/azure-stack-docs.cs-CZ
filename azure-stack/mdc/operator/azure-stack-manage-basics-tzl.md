---
title: Základy správy centra Azure Stack
titleSuffix: Azure Stack Hub
description: Seznamte se se základy správy centra Azure Stack.
author: justinha
ms.topic: article
ms.date: 10/26/2020
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 6638ff430482a270b172155160d1599c2844781a
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329156"
---
# <a name="azure-stack-hub-administration-basics"></a>Základy správy centra Azure Stack

Pokud s Azure Stack správy centra začínáte, je potřeba znát několik věcí. Tento článek poskytuje přehled vaší role jako operátor centra Azure Stack a o tom, co je potřeba k tomu, abychom svým uživatelům informovali, že jim pomohou zvýšit produktivitu.

## <a name="understand-the-builds"></a>Pochopení sestavení

Pokud používáte integrovaný systém Azure Stack hub, aktualizované verze centra Azure Stack se distribuují prostřednictvím aktualizačních balíčků. Tyto balíčky můžete importovat a použít pomocí dlaždice **aktualizace** na portálu pro správu.

## <a name="learn-about-available-services"></a>Další informace o dostupných službách

Mějte na paměti, které služby můžete uživatelům zpřístupnit. Centrum Azure Stack podporuje podmnožinu služeb Azure. Seznam podporovaných služeb bude i nadále vyvíjet.

### <a name="foundational-services"></a>Základní služby

Ve výchozím nastavení zahrnuje Azure Stack hub při nasazení Azure Stack hub následující základní služby:

- Compute
- Storage
- Sítě
- Key Vault

S těmito základními službami můžete uživatelům s minimální konfigurací nabídnout infrastrukturu jako službu (IaaS).

### <a name="additional-services"></a>Další služby

Podporujeme následující další služby PaaS (Platform as a Service):

- App Service
- Azure Functions
- Databáze SQL a MySQL
- Kubernetes
- IoT Hub
- Centrum událostí

Tyto služby vyžadují další konfiguraci, abyste je mohli zpřístupnit uživatelům. Další informace najdete v tématech **kurzy** a návody, **které**  >  **nabízí služby** v [dokumentaci k operátorovi centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/).

### <a name="service-roadmap"></a>Plán služby

Centrum Azure Stack bude dál přidávat podporu pro služby Azure. Plánovaný plán najdete v tématu [centrum Azure Stack: rozšíření Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409) White Paper. Můžete také monitorovat [příspěvky na blogu centra Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) pro nová oznámení.

## <a name="what-account-should-i-use"></a>Jaký účet mám použít?

Při správě centra Azure Stack je potřeba vědět o několika ohledech. To platí hlavně v nasazeních, která používají Windows Server Active Directory Federation Services (AD FS) (AD FS) jako zprostředkovatele identity místo Azure Active Directory (Azure AD).

| **Účet** | **Azure** | **AD FS** |
|---|---|---|
| Místní správce (.\Administrator) |   |
| Globální správce Azure AD | Používá se během instalace. <br> Vlastník výchozího zprostředkovatele | Neužívá se. |
| Účet pro rozšířené úložiště|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>Jaké nástroje se používají ke správě?

Ke správě centra Azure Stack můžete použít [portál pro správu](../../operator/azure-stack-manage-portals.md) nebo PowerShell. Nejjednodušší způsob, jak se naučit základní koncepty, je prostřednictvím portálu. Pokud chcete používat PowerShell, existují přípravné kroky. Než začnete, můžete se seznámit s tím, jak se prostředí PowerShell používá v Azure Stackovém centru. Další informace najdete v tématu [Začínáme s PowerShellem v centru Azure Stack](../../user/azure-stack-powershell-overview.md).

Azure Stack centrum používá Azure Resource Manager jako základní mechanismus nasazení, správy a organizace. Pokud se chystáte spravovat centra Azure Stack a pomáhat uživatelům, měli byste se dozvědět víc o Správce prostředků. Další informace najdete v [Začínáme Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) dokumentu White Paper.

## <a name="your-typical-responsibilities"></a>Vaše typické povinnosti

Vaši uživatelé chtějí používat služby. Z perspektivy je hlavním úkolem, aby jim byly dostupné tyto služby. Rozhodněte, které služby nabízet a zpřístupněte tyto služby vytvořením plánů, nabídek a kvót. Další informace najdete v tématu [Přehled nabízených služeb v centru Azure Stack](../../operator/service-plan-offer-subscription-overview.md).

Také budete muset přidat položky do [webu Azure Stack hub Marketplace](../../operator/azure-stack-marketplace.md). Nejjednodušším způsobem je [stáhnout z Azure položky Marketplace do centra Azure Stack](../../operator/azure-stack-download-azure-marketplace-item.md).

Pokud chcete testovat vaše plány, nabídky a služby, můžete použít [portál User Portal](../../operator/azure-stack-manage-portals.md). Nejedná se o portál pro správu.

Kromě poskytování služeb je třeba provést běžné povinnosti operátora, aby bylo udržování služby Azure Stack hub v provozu. Mezi tyto funkce patří následující úlohy:

- Přidejte uživatelské účty pro nasazení [služby Azure AD](../../operator/azure-stack-add-new-user-aad.md) .
- [Nastavte přístupová oprávnění pomocí řízení přístupu na základě rolí](../../operator/azure-stack-manage-permissions.md). (Tato úloha není omezena na správce.)
- [Monitorování stavu infrastruktury](../../operator/azure-stack-monitor-health.md)
- Správa prostředků [sítě](../../operator/azure-stack-viewing-public-ip-address-consumption.md) a [úložiště](../../operator/azure-stack-manage-storage-accounts.md)
- [Spusťte a zastavte centrum Azure Stack](../../operator/azure-stack-start-and-stop.md).
- [Provoz rozšířeného úložiště](../../user/azure-stack-network-howto-extend-datacenter.md).
- [Spravovat IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub).
- [Správa centra událostí](https://docs.microsoft.com/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [Spravovat App Service](https://docs.microsoft.com/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- Nahraďte špatný hardware. Tady je seznam [nahraditelných částí](../../operator/azure-stack-replace-component.md).
- [Získejte podporu](https://docs.microsoft.com/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).

## <a name="operator-tasks"></a>Úkoly operátora

Tady je seznam každodenních, týdenních a měsíčních úloh pro operátor:

# <a name="daily"></a>[Každý den](#tab/daily)

1. Kontrolovat výstrahy.
2. Ověřte stav zálohování.
3. Aktualizuje signatura v programu Defender (odpojené systémy).
4. Zkontroluje stav systému Isilon a události v OneFS.
5. Ověřte Isilonou kapacitu.

# <a name="weekly"></a>[Každý týden](#tab/weekly)

1. Podívejte se na kapacitu.
2. Spusťte `isi status –verbose` v Avocent připojení.

# <a name="monthly"></a>[Měsíčně](#tab/monthly)

1. Použijte měsíční balíčky aktualizací (Microsoft & OEM).
2. Ověřte zálohu pomocí ASDK.
3. Spravovat tržiště centra Azure Stack (zachovat aktuální)
4. Aktualizujte firmware přepínače & Avocent.
5. Uvolní kapacitu úložiště.

# <a name="ondemand"></a>[OnDemand](#tab/ondemand)

1. Rotace tajných kódů.
2. Vytváření a aktualizace nabídek, plánů a kvót.
3. Použijte balíčky oprav hotfix.
4. Použijte balíčky oprav hotfix.
5. Rozšířit kapacitu (uzly & IPSpace).
6. Spusťte `isi status –verbose` v Avocent připojení.
7. Obnovte účty úložiště.
8. Zastavte systém.
9. Shromažďování protokolů diagnostiky.

---

## <a name="what-to-tell-your-users"></a>Co říct uživatelům

Budete muset uživatelům sdělit, jak pracovat se službami v centru Azure Stack, jak se připojit k prostředí a jak se přihlásit k odběru nabídek. Kromě jakékoli vlastní dokumentace, kterou byste mohli chtít poskytnout uživatelům, můžete uživatele nasměrovat na [Azure Stack dokumentaci pro uživatele centra](https://docs.microsoft.com/azure-stack/user/).

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>Informace o tom, jak pracovat se službami v centru Azure Stack

Než uživatelé použijí služby a sestavují aplikace v centru Azure Stack, musí si uvědomit informace. Například existují konkrétní požadavky na PowerShell a verzi rozhraní API. Mezi službou v Azure a ekvivalentní službou ve službě Azure Stack hub jsou taky některé rozdíly ve funkcích. Ujistěte se, že uživatelé si přečtěte následující články:

- [Rozdíly mezi centrem Azure Stack a Azure při používání služeb a sestavování aplikací](../../user/azure-stack-considerations.md)
- [Funkce virtuálního počítače centra Azure Stack](../../user/azure-stack-vm-considerations.md)
- [Úložiště centra Azure Stack: rozdíly a požadavky](../../user/azure-stack-acs-differences.md)

Informace v těchto článcích shrnuje rozdíly mezi službou v Azure a centra Azure Stack. Doplňuje informace, které jsou k dispozici pro službu Azure v globální dokumentaci k Azure.

### <a name="connect-to-azure-stack-hub-as-a-user"></a>Připojení k Azure Stack centrum jako uživatel

Uživatelé budou chtít, abyste věděli, jak [získat přístup k portálu User Portal](../../user/azure-stack-use-portal.md) nebo jak se připojit prostřednictvím PowerShellu. V prostředí integrovaných systémů se adresa uživatelského portálu liší podle nasazení. Musíte uživatelům poskytnout správnou adresu URL.

Pokud používáte PowerShell, uživatelé budou muset registrovat poskytovatele prostředků předtím, než budou moci používat služby. Poskytovatel prostředků spravuje službu. Poskytovatel síťových prostředků například spravuje prostředky, jako jsou virtuální sítě, síťová rozhraní a nástroje pro vyrovnávání zatížení. Musí si [nainstalovat](../../operator/azure-stack-powershell-install.md) PowerShell, [Stáhnout](../../operator/azure-stack-powershell-download.md) další moduly a [nakonfigurovat](../../user/azure-stack-powershell-configure-user.md) PowerShell (který zahrnuje registraci poskytovatele prostředků).

### <a name="subscribe-to-an-offer"></a>Přihlášení k odběru nabídky

Předtím, než může uživatel použít služby, se musí [přihlásit k odběru nabídky](../../operator/azure-stack-subscribe-plan-provision-vm.md) , kterou jste vytvořili jako operátor.

## <a name="where-to-get-support"></a>Kde získat podporu

Informace o podpoře pro starší verze centra Azure Stack (pre-1905) najdete v článku [zásady obsluhy centra Azure Stack](../../operator/azure-stack-servicing-policy.md).

Pro integrovaný systém existuje koordinovaný proces eskalace a rozlišení mezi společností Microsoft a našimi hardwarovými partnery OEM (Original Equipment Manufacturer).

Pokud dojde k problému s cloudovou službou, podpora je poskytována prostřednictvím služby Microsoft Customer Support Services (CSS). Chcete-li otevřít žádost o podporu, vyberte v pravém horním rohu portálu pro správu ikonu Nápověda a podpora (otazník). Pak v části **Podpora** vyberte **help + podpora** a pak **novou žádost o podporu** .

Pokud dojde k potížím s nasazením, opravou a aktualizací, hardwarem (včetně nahraditelných jednotek v poli) nebo jakýmkoli softwarem v hardwarovém prostředí, jako je například software spuštěný na hostiteli životního cyklu hardwaru, obraťte se nejprve na dodavatele hardwaru výrobce OEM.

U cokoli jiného kontaktujte Microsoft CSS.

## <a name="next-steps"></a>Další kroky

- [Správa oblastí v centru Azure Stack](../../operator/azure-stack-region-management.md)
