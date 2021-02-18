---
title: Zásady obsluhy centra Azure Stack
titleSuffix: Azure Stack Hub
description: Přečtěte si o zásadách obsluhy centra Azure Stack a o tom, jak zachovat integrovaný systém v podporovaném stavu.
author: sethmanheim
ms.topic: article
ms.date: 02/17/2020
ms.author: sethm
ms.reviewer: niy
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 95686f1fc9ae56cefe2063c03e8e80d0e156af83
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100648027"
---
# <a name="azure-stack-hub-servicing-policy"></a>Zásady obsluhy centra Azure Stack

Tento článek popisuje zásady obsluhy pro integrované systémy centra Azure Stack a to, co je potřeba udělat, abyste systém zachovali v podporovaném stavu.

## <a name="download-update-packages-for-integrated-systems"></a>Stáhnout balíčky aktualizací pro integrované systémy

Společnost Microsoft vydává úplné balíčky aktualizací i balíčky oprav hotfix, které řeší konkrétní problémy.

Úplné balíčky aktualizací se hostují v zabezpečeném koncovém bodu Azure. Můžete je stáhnout ručně pomocí [nástroje Azure Stack hub](https://aka.ms/azurestackupdatedownload)pro stažení aktualizací. Pokud je jednotka škálování připojená, zobrazí se automaticky na portálu pro správu, jakmile bude **k dispozici aktualizace**. Další informace o jednotlivých vydáních můžete kliknout na libovolnou verzi v části [aktualizace tempo verze](#update-package-release-cadence) v tomto článku.

Balíčky aktualizací oprav hotfix jsou hostovány ve stejném zabezpečeném koncovém bodu Azure. Můžete si je stáhnout pomocí vložených odkazů v každé z příslušných článků o opravě hotfix KB. například [Azure Stack 1.1809.12.114ování v centru pro opravy](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Podobně jako u úplných, měsíčních aktualizačních balíčků můžou operátory centra Azure Stack stahovat soubory. XML a. zip a importovat je pomocí postupu v části [použití aktualizací v centru Azure Stack](azure-stack-apply-updates.md). U operátorů centra Azure Stack s připojenými jednotkami škálování se tyto opravy hotfix automaticky zobrazí na portálu pro správu a **k dispozici je aktualizace** zprávy.

Pokud vaše jednotka škálování není připojená a chcete být upozorněni na každou verzi opravy hotfix, přihlaste se k odběru [informačního kanálu RSS](https://azurestackhubdocs.azurewebsites.net/xml/hotfixes.rss) , abyste byli informováni o jednotlivých vydáních oprav hotfix.

## <a name="update-package-types"></a>Aktualizace typů balíčků

Existují dva typy balíčků aktualizací pro integrované systémy:

- **Aktualizace softwaru společnosti Microsoft**. Společnost Microsoft zodpovídá za kompletní životní cyklus údržby pro balíčky aktualizací softwaru společnosti Microsoft. Tyto balíčky můžou zahrnovat nejnovější aktualizace zabezpečení Windows serveru, aktualizace nesouvisející se zabezpečením a aktualizace funkcí centra Azure Stack. Balíčky aktualizací následujících můžete stáhnout přímo od Microsoftu.

- **Hardware výrobce OEM – aktualizace poskytované dodavatelem** Služby Azure Stack hub jsou zodpovědné za kompletní životní cyklus údržby (včetně pokynů) pro balíčky pro firmware a aktualizace ovladačů souvisejících s hardwarem. Kromě toho Azure Stack hub hardware partneři vlastní a udržují pokyny pro veškerý software a hardware v hostiteli životního cyklu hardwaru. Dodavatel hardwaru OEM tyto balíčky aktualizací hostuje na svém vlastním webu pro stažení.

## <a name="update-package-release-cadence"></a>Aktualizace tempo verze balíčku

Microsoft očekává, že se balíčky aktualizací softwaru vydávají víckrát během roku.

Výrobci hardwaru OEM vydávají své aktualizace podle potřeby. Nejnovější aktualizace hardwaru získáte od výrobce OEM.

Najděte si dokumentaci, jak naplánovat a spravovat aktualizace a jak zjistit aktuální verzi v tématu [Správa aktualizací – přehled](azure-stack-updates.md).

Informace o konkrétní aktualizaci, včetně toho, jak ji stáhnout, najdete v poznámkách k verzi této aktualizace:

- [Aktualizace centra Azure Stack 2008](./release-notes.md?preserve-view=true&view=azs-2008)
- [Aktualizace centra Azure Stack 2005](./release-notes.md?preserve-view=true&view=azs-2005)
- [Aktualizace centra Azure Stack 2002](./release-notes.md?preserve-view=true&view=azs-2002)

## <a name="hotfixes"></a>Opravy hotfix

V některých případech společnost Microsoft poskytuje opravy pro Azure Stack centrum, které řeší konkrétní problém, který je často preventivní nebo časově citlivý. Každá oprava hotfix je vydána s odpovídajícím článkem znalostní báze Microsoft Knowledge Base (KB), který podrobně popisuje problémy řešené v této opravě hotfix.

Počínaje verzí Build 2005 se při aktualizaci na novou hlavní verzi (například 1.2002. x na 1.2005. x) automaticky nainstalují nejnovější opravy hotfix (pokud nějaké jsou) v nové hlavní verzi. Od tohoto okamžiku předem, pokud byla vydána oprava hotfix pro sestavení, měli byste ji nainstalovat.

Opravy hotfix se stáhnou a nainstalují stejně jako běžné úplné balíčky aktualizací pro centrum Azure Stack. Na rozdíl od úplné aktualizace se ale opravy hotfix dají instalovat během několika minut. Při instalaci oprav hotfix doporučujeme použít operátory centra Azure Stack nastavení časových intervalů pro správu a údržbu. Opravy hotfix aktualizují verzi cloudu centra Azure Stack, abyste mohli snadno zjistit, jestli se tato oprava hotfix nastavila. Samostatná oprava hotfix je k dispozici pro každou verzi centra Azure Stack, která je stále v podpoře. **Každá oprava hotfix pro konkrétní iterace je kumulativní a zahrnuje předchozí opravy hotfix pro stejnou verzi.** Další informace o použitelnosti konkrétní opravy hotfix najdete v příslušném článku znalostní báze. Viz odkazy na poznámky k verzi v předchozí části.

Informace o aktuálně dostupných opravách hotfix naleznete v poznámkách k verzi této aktualizace:

- [2005 oprava hotfix centra Azure Stack](./release-notes.md?preserve-view=true&view=azs-2005#hotfixes)
- [2002 oprava hotfix centra Azure Stack](./release-notes.md?preserve-view=true&view=azs-2002#hotfixes-1)

## <a name="keep-your-system-under-support"></a>Zachování systému v rámci podpory

Aby vaše instance centra Azure Stack zůstala v podporovaném stavu, musí instance běžet z poslední vydané verze aktualizace nebo spustit jednu z těchto dvou předchozích verzí aktualizace.

Musíte mít také aktivní smlouvu o podpoře s hardwarovým partnerem, který systém vyrobil. Společnost Microsoft není schopna podporovat vás bez smlouvy o podpoře hardwaru.

Opravy hotfix nejsou považovány za hlavní verze aktualizací. Pokud je vaše instance centra Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi.

Pokud je například poslední dostupná verze aktualizace 1904 a předchozí dva aktualizační balíčky byly verze 1903 a 1902, zůstane podpora 1902 i 1903. 1901 ale nepodporují. Zásada má hodnotu true, pokud není k dispozici žádná verze na měsíc nebo dvě. Pokud je například aktuální verze 1807 a nebyla vydána žádná verze 1806, v rámci podpory zůstanou předchozí dva balíčky aktualizací 1805 a 1804.

Balíčky aktualizací softwaru společnosti Microsoft jsou nekumulativní a vyžadují jako součást předchozí balíček aktualizace nebo opravu hotfix. Pokud se rozhodnete jednu nebo více aktualizací odložit, zvažte celkový modul runtime, pokud chcete získat nejnovější verzi.

### <a name="resource-provider-version-support"></a>Podpora verze poskytovatele prostředků

U poskytovatelů prostředků Azure Stack hub je důležité poznamenat, že je podporovaná jenom poslední vydaná verze daného poskytovatele prostředků, která je kompatibilní s vaší podporovanou verzí centra Azure Stack, i když používáte starší verzi Azure Stack centra, která je stále v okně podpory.

Další informace o kompatibilitě poskytovatele prostředků najdete v poznámkách k verzi pro konkrétního poskytovatele prostředků.

## <a name="get-support"></a>Získání podpory

Centrum Azure Stack se řídí stejným procesem podpory jako Azure. Podnikoví zákazníci mohou postupovat podle postupu popsaného v tématu [Postup vytvoření žádosti o podporu Azure](/azure/azure-supportability/how-to-create-azure-support-request). Pokud jste zákazníkem poskytovatele Cloud Solution Provider (CSP), požádejte o podporu svého CSP. Další informace najdete v nejčastějších dotazech k [podpoře Azure](https://azure.microsoft.com/support/faq/).

Pomoc při řešení potíží s aktualizacemi najdete v tématu [osvědčené postupy pro řešení potíží s problémy s aktualizacemi centra Azure Stack a aktualizací](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Další kroky

- [Správa aktualizací ve službě Azure Stack Hub](azure-stack-updates.md)
- [Osvědčené postupy pro řešení potíží s problémy s aktualizacemi a aktualizacemi centra Azure Stack](azure-stack-troubleshooting.md)