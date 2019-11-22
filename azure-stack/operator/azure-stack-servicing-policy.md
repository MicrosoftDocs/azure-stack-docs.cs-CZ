---
title: Zásady pro obsluhu Azure Stack | Microsoft Docs
description: Přečtěte si o zásadách pro obsluhu Azure Stack a o tom, jak zachovat integrovaný systém v podporovaném stavu.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 28eb11be532608648761f5dcfe2d2d7f2337b80e
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298799"
---
# <a name="azure-stack-servicing-policy"></a>Zásady obsluhy Azure Stack

Tento článek popisuje zásady pro obsluhu Azure Stack integrovaných systémů a to, co je potřeba udělat, abyste systém zachovali v podporovaném stavu.

## <a name="download-update-packages-for-integrated-systems"></a>Stáhnout balíčky aktualizací pro integrované systémy

Společnost Microsoft vydává úplné měsíční balíčky aktualizací i balíčky oprav hotfix, které řeší konkrétní problémy.

Měsíční balíčky aktualizací se hostují v zabezpečeném koncovém bodu Azure. Můžete je stáhnout ručně pomocí nástroje pro stažení [aktualizací Azure Stack](https://aka.ms/azurestackupdatedownload). Pokud je jednotka škálování připojená, zobrazí se automaticky na portálu pro správu, jakmile bude **k dispozici aktualizace**. Úplné a měsíční balíčky aktualizací jsou podrobně dokumentovány v každé vydané verzi. Další informace o jednotlivých vydáních můžete kliknout na libovolnou verzi v části [aktualizace tempo verze](#update-package-release-cadence) v tomto článku.

Balíčky aktualizací oprav hotfix jsou hostovány ve stejném zabezpečeném koncovém bodu Azure. Můžete si je stáhnout pomocí vložených odkazů v každé z příslušných článků o opravě hotfix KB. například [Azure Stack hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Podobně jako u úplných, měsíčních aktualizačních balíčků můžou operátory Azure Stack stahovat soubory. XML,. bin a. exe a importovat je pomocí postupu v části [použití aktualizací v Azure Stack](azure-stack-apply-updates.md). U operátorů Azure Stack s připojenými jednotkami škálování se tyto opravy hotfix automaticky zobrazují na portálu pro správu a **k dispozici je aktualizace**zprávy.

Pokud vaše jednotka škálování není připojená a chcete být upozorněni na každou verzi opravy hotfix, přihlaste se k odběru informačního kanálu [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) nebo [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) , který jste si poznamenali v každé verzi.

## <a name="update-package-types"></a>Aktualizace typů balíčků

Existují dva typy balíčků aktualizací pro integrované systémy:

- **Aktualizace softwaru společnosti Microsoft**. Společnost Microsoft zodpovídá za kompletní životní cyklus údržby pro balíčky aktualizací softwaru společnosti Microsoft. Tyto balíčky můžou zahrnovat nejnovější aktualizace zabezpečení Windows serveru, aktualizace bez zabezpečení a Azure Stack aktualizace funkcí. Balíčky aktualizací následujících můžete stáhnout přímo od Microsoftu.

- **Hardware výrobce OEM – aktualizace poskytované dodavatelem** Azure Stack hardwarovým partnerům zodpovídáme za kompletní životní cyklus údržby (včetně pokynů) pro balíčky pro firmware a aktualizace ovladačů souvisejících s hardwarem. Kromě toho Azure Stack hardwaroví partneři vlastní a udržovat pokyny pro veškerý software a hardware na hostiteli životního cyklu hardwaru. Dodavatel hardwaru OEM tyto balíčky aktualizací hostuje na svém vlastním webu pro stažení.

## <a name="update-package-release-cadence"></a>Aktualizace tempo verze balíčku

Microsoft očekává, že se balíčky aktualizací softwaru vydávají měsíčně tempo. Je ale možné, že v měsíci budete mít několik nebo žádné aktualizační verze. Výrobci hardwaru OEM vydávají své aktualizace podle potřeby.

Najděte si dokumentaci, jak naplánovat a spravovat aktualizace a jak zjistit aktuální verzi v tématu [Správa aktualizací – přehled](azure-stack-updates.md).

Informace o konkrétní aktualizaci, včetně toho, jak ji stáhnout, najdete v poznámkách k verzi této aktualizace:

- [Aktualizace Azure Stack 1910](/azure-stack/operator/release-notes?view=azs-1910)
- [Aktualizace Azure Stack 1908](/azure-stack/operator/release-notes?view=azs-1908)
- [Aktualizace Azure Stack 1907](/azure-stack/operator/release-notes?view=azs-1907)
- [Aktualizace Azure Stack 1906](/azure-stack/operator/release-notes?view=azs-1906)

## <a name="hotfixes"></a>Opravy hotfix

*Platí pro: integrované systémy Azure Stack*

V některých případech společnost Microsoft poskytuje opravy hotfix pro Azure Stack, které řeší konkrétní problém, který je často preventivní nebo časově citlivý.  Každá oprava hotfix je vydána spolu s odpovídajícím článkem znalostní báze Microsoft Knowledge Base, který podrobně popisuje problém, příčinu a řešení.

Opravy hotfix se stáhnou a nainstalují stejně jako běžné úplné aktualizace balíčků pro Azure Stack. Na rozdíl od úplné aktualizace se ale opravy hotfix dají instalovat během několika minut. Při instalaci oprav hotfix doporučujeme Azure Stack Operators nastavit časové intervaly pro správu a údržbu. Opravy hotfix aktualizují verzi vašeho cloudu Azure Stack, abyste mohli snadno zjistit, jestli se tato oprava hotfix nastavila. Samostatná oprava hotfix je k dispozici pro každou verzi Azure Stack, která je stále v podpoře. Každá oprava konkrétní iterace je kumulativní a obsahuje předchozí aktualizace pro stejnou verzi. V příslušném článku znalostní báze si můžete přečíst další informace o použitelnosti konkrétní opravy hotfix. Viz odkazy na poznámky k verzi v předchozí části.

Informace o aktuálně dostupných opravách hotfix naleznete v poznámkách k verzi této aktualizace:

- [Oprava hotfix Azure Stack 1910](/azure-stack/operator/release-notes?view=azs-1910#hotfixes)
- [Oprava hotfix Azure Stack 1908](/azure-stack/operator/release-notes?view=azs-1908#hotfixes)
- [Oprava hotfix Azure Stack 1907](/azure-stack/operator/release-notes?view=azs-1907#hotfixes)
- [Oprava hotfix Azure Stack 1906](/azure-stack/operator/release-notes?view=azs-1906#hotfixes)

## <a name="keep-your-system-under-support"></a>Zachování systému v rámci podpory

Aby instance Azure Stack zůstala v podporovaném stavu, musí instance běžet z poslední vydané verze aktualizace nebo spustit jednu z těchto dvou předchozích verzí aktualizace.

Opravy hotfix nejsou považovány za hlavní verze aktualizací. Pokud je vaše instance Azure Stack za *více než dvěma aktualizacemi*, je považována za nedodržující předpisy. Aby bylo možné získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi.

Pokud je například poslední dostupná verze aktualizace 1904 a předchozí dva aktualizační balíčky byly verze 1903 a 1902, zůstane podpora 1902 i 1903. 1901 ale nepodporují. Zásada má hodnotu true, pokud není k dispozici žádná verze na měsíc nebo dvě. Pokud je například aktuální verze 1807 a nebyla vydána žádná verze 1806, v rámci podpory zůstanou předchozí dva balíčky aktualizací 1805 a 1804.

Balíčky aktualizací softwaru společnosti Microsoft jsou nekumulativní a vyžadují jako součást předchozí balíček aktualizace nebo opravu hotfix. Pokud se rozhodnete jednu nebo více aktualizací odložit, zvažte celkový modul runtime, pokud chcete získat nejnovější verzi.

## <a name="get-support"></a>Získat podporu

Azure Stack se řídí stejným procesem podpory jako Azure. Podnikoví zákazníci mohou postupovat podle postupu popsaného v tématu [Postup vytvoření žádosti o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Pokud jste zákazníkem poskytovatele Cloud Solution Provider (CSP), požádejte o podporu svého CSP. Další informace najdete v nejčastějších dotazech k [podpoře Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Další kroky

- [Správa aktualizací ve službě Azure Stack](azure-stack-updates.md)
