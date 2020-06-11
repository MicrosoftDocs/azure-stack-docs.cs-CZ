---
title: Zásady obsluhy centra Azure Stack
titleSuffix: Azure Stack Hub
description: Přečtěte si o zásadách obsluhy centra Azure Stack a o tom, jak zachovat integrovaný systém v podporovaném stavu.
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.reviewer: niy
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 6b2a3a281e16ff658b7124071cdb83326d0cbbde
ms.sourcegitcommit: 396f79ce073d99d14fcc71b85c4a4932334832a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636831"
---
# <a name="azure-stack-hub-servicing-policy"></a>Zásady obsluhy centra Azure Stack

Tento článek popisuje zásady obsluhy pro integrované systémy centra Azure Stack a to, co je potřeba udělat, abyste systém zachovali v podporovaném stavu.

## <a name="download-update-packages-for-integrated-systems"></a>Stáhnout balíčky aktualizací pro integrované systémy

Společnost Microsoft vydává úplné měsíční balíčky aktualizací i balíčky oprav hotfix, které řeší konkrétní problémy.

Měsíční balíčky aktualizací se hostují v zabezpečeném koncovém bodu Azure. Můžete je stáhnout ručně pomocí [nástroje Azure Stack hub](https://aka.ms/azurestackupdatedownload)pro stažení aktualizací. Pokud je jednotka škálování připojená, zobrazí se automaticky na portálu pro správu, jakmile bude **k dispozici aktualizace**. Úplné a měsíční balíčky aktualizací jsou podrobně dokumentovány v každé vydané verzi. Další informace o jednotlivých vydáních můžete kliknout na libovolnou verzi v části [aktualizace tempo verze](#update-package-release-cadence) v tomto článku.

Balíčky aktualizací oprav hotfix jsou hostovány ve stejném zabezpečeném koncovém bodu Azure. Můžete si je stáhnout pomocí vložených odkazů v každé z příslušných článků o opravě hotfix KB. například [Azure Stack 1.1809.12.114ování v centru pro opravy](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Podobně jako u úplných, měsíčních aktualizačních balíčků můžou operátory centra Azure Stack stahovat soubory. XML,. bin a. exe a importovat je pomocí postupu v části [použití aktualizací v centru Azure Stack](azure-stack-apply-updates.md). U operátorů centra Azure Stack s připojenými jednotkami škálování se tyto opravy hotfix automaticky zobrazí na portálu pro správu a **k dispozici je aktualizace**zprávy.

Pokud vaše jednotka škálování není připojená a chcete být upozorněni na každou verzi opravy hotfix, přihlaste se k odběru informačního kanálu [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) nebo [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) v každé vydané verzi.

## <a name="update-package-types"></a>Aktualizace typů balíčků

Existují dva typy balíčků aktualizací pro integrované systémy:

- **Aktualizace softwaru společnosti Microsoft**. Společnost Microsoft zodpovídá za kompletní životní cyklus údržby pro balíčky aktualizací softwaru společnosti Microsoft. Tyto balíčky můžou zahrnovat nejnovější aktualizace zabezpečení Windows serveru, aktualizace nesouvisející se zabezpečením a aktualizace funkcí centra Azure Stack. Balíčky aktualizací následujících můžete stáhnout přímo od Microsoftu.

- **Hardware výrobce OEM – aktualizace poskytované dodavatelem** Služby Azure Stack hub jsou zodpovědné za kompletní životní cyklus údržby (včetně pokynů) pro balíčky pro firmware a aktualizace ovladačů souvisejících s hardwarem. Kromě toho Azure Stack hub hardware partneři vlastní a udržují pokyny pro veškerý software a hardware v hostiteli životního cyklu hardwaru. Dodavatel hardwaru OEM tyto balíčky aktualizací hostuje na svém vlastním webu pro stažení.

## <a name="update-package-release-cadence"></a>Aktualizace tempo verze balíčku

Microsoft očekává, že se balíčky aktualizací softwaru vydávají měsíčně tempo. Je ale možné, že v měsíci máte více nebo žádné verze aktualizací. Výrobci hardwaru OEM vydávají své aktualizace podle potřeby.

Najděte si dokumentaci, jak naplánovat a spravovat aktualizace a jak zjistit aktuální verzi v tématu [Správa aktualizací – přehled](azure-stack-updates.md).

Informace o konkrétní aktualizaci, včetně toho, jak ji stáhnout, najdete v poznámkách k verzi této aktualizace:

- [Aktualizace centra Azure Stack 2002](/azure-stack/operator/release-notes?view=azs-2002)
- [Aktualizace centra Azure Stack 1910](/azure-stack/operator/release-notes?view=azs-1910)
- [Aktualizace centra Azure Stack 1908](/azure-stack/operator/release-notes?view=azs-1908)
- [Aktualizace centra Azure Stack 1907](/azure-stack/operator/release-notes?view=azs-1907)

## <a name="hotfixes"></a>Opravy hotfix

V některých případech společnost Microsoft poskytuje opravy pro Azure Stack centrum, které řeší konkrétní problém, který je často preventivní nebo časově citlivý. Každá oprava hotfix je vydána spolu s odpovídajícím článkem znalostní báze Microsoft Knowledge Base, který podrobně popisuje problémy řešené touto opravou hotfix.

Opravy hotfix se stáhnou a nainstalují stejně jako běžné úplné balíčky aktualizací pro centrum Azure Stack. Na rozdíl od úplné aktualizace se ale opravy hotfix dají instalovat během několika minut. Při instalaci oprav hotfix doporučujeme použít operátory centra Azure Stack nastavení časových intervalů pro správu a údržbu. Opravy hotfix aktualizují verzi cloudu centra Azure Stack, abyste mohli snadno zjistit, jestli se tato oprava hotfix nastavila. Samostatná oprava hotfix je k dispozici pro každou verzi centra Azure Stack, která je stále v podpoře. **Každá oprava hotfix pro konkrétní iterace je kumulativní a zahrnuje předchozí opravy hotfix pro stejnou verzi.** V příslušném článku znalostní báze si můžete přečíst další informace o použitelnosti konkrétní opravy hotfix. Viz odkazy na poznámky k verzi v předchozí části.

Informace o aktuálně dostupných opravách hotfix naleznete v poznámkách k verzi této aktualizace:

- [2002 oprava hotfix centra Azure Stack](/azure-stack/operator/release-notes?view=azs-2002#hotfixes)
- [1910 oprava hotfix centra Azure Stack](/azure-stack/operator/release-notes?view=azs-1910#hotfixes-1)
- [1908 oprava hotfix centra Azure Stack](/azure-stack/operator/release-notes?view=azs-1908#hotfixes-2)
- [1907 oprava hotfix centra Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-3)

## <a name="keep-your-system-under-support"></a>Zachování systému v rámci podpory

::: moniker range="azs-2002"

> [!IMPORTANT]  
> V rámci aktualizace centra Azure Stack 2002 společnost Microsoft dočasně rozšiřuje naše příkazy zásad podpory centra Azure Stack. Spolupracujeme se zákazníky po celém světě, kteří reagují na COVID-19 a kteří můžou dělat důležitá rozhodnutí o svých Azure Stackch systémech, jak se aktualizují a spravují, a výsledkem je, že obchodní operace datového centra budou fungovat normálně. V rámci podpory našich zákazníků nabízí Microsoft dočasnou příponu pro změnu zásad, která zahrnuje tři předchozí verze aktualizací. V důsledku toho bude podporována nově vydaná aktualizace 2002 a kterákoli ze tří předchozích verzí aktualizace (například 1910, 1908 a 1907).

::: moniker-end

Aby vaše instance centra Azure Stack zůstala v podporovaném stavu, musí instance běžet z poslední vydané verze aktualizace nebo spustit jednu z těchto dvou předchozích verzí aktualizace.

Opravy hotfix nejsou považovány za hlavní verze aktualizací. Pokud je vaše instance centra Azure Stack za *více než dvěma aktualizacemi*, je považována za nedodržující předpisy. Aby bylo možné získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi.

Pokud je například poslední dostupná verze aktualizace 1904 a předchozí dva aktualizační balíčky byly verze 1903 a 1902, zůstane podpora 1902 i 1903. 1901 ale nepodporují. Zásada má hodnotu true, pokud není k dispozici žádná verze na měsíc nebo dvě. Pokud je například aktuální verze 1807 a nebyla vydána žádná verze 1806, v rámci podpory zůstanou předchozí dva balíčky aktualizací 1805 a 1804.

Balíčky aktualizací softwaru společnosti Microsoft jsou nekumulativní a vyžadují jako součást předchozí balíček aktualizace nebo opravu hotfix. Pokud se rozhodnete jednu nebo více aktualizací odložit, zvažte celkový modul runtime, pokud chcete získat nejnovější verzi.

### <a name="resource-provider-version-support"></a>Podpora verze poskytovatele prostředků

U poskytovatelů prostředků Azure Stack hub je důležité poznamenat, že je podporovaná jenom poslední vydaná verze daného poskytovatele prostředků, která je kompatibilní s vaší podporovanou verzí centra Azure Stack, i když používáte starší verzi Azure Stack centra, která je stále v okně podpory.

Další informace o kompatibilitě poskytovatele prostředků najdete v poznámkách k verzi pro konkrétního poskytovatele prostředků.

## <a name="get-support"></a>Získat podporu

Centrum Azure Stack se řídí stejným procesem podpory jako Azure. Podnikoví zákazníci mohou postupovat podle postupu popsaného v tématu [Postup vytvoření žádosti o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Pokud jste zákazníkem poskytovatele Cloud Solution Provider (CSP), požádejte o podporu svého CSP. Další informace najdete v nejčastějších dotazech k [podpoře Azure](https://azure.microsoft.com/support/faq/).

Pomoc při řešení potíží s aktualizacemi najdete v tématu [osvědčené postupy pro řešení potíží s problémy s aktualizacemi centra Azure Stack a aktualizací](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Další kroky

- [Správa aktualizací ve službě Azure Stack Hub](azure-stack-updates.md)
- [Osvědčené postupy pro řešení potíží s problémy s aktualizacemi a aktualizacemi centra Azure Stack](azure-stack-troubleshooting.md)
