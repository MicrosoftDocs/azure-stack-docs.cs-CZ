---
title: Správa aktualizací v Azure Stack | Microsoft Docs
description: Naučte se spravovat aktualizace v Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 2a6165d37aa813c332cd664ef905717ef7a8ffa8
ms.sourcegitcommit: dc633e862d49412a963daee481226c1543287e5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70863023"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Přehled správy aktualizací v Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Úplné a expresní aktualizace, opravy hotfix, stejně jako aktualizace ovladačů a firmwaru od výrobce OEM (Original Equipment Manufacturer), všechny pomůžou Azure Stack aktuálnost. Tento článek popisuje různé typy aktualizací, kdy očekávat jejich vydání a kde najdete další informace o aktuální verzi.

> [!Note]  
> Balíčky aktualizací Azure Stack nelze použít na Azure Stack Development Kit (ASDK). Balíčky aktualizací jsou navržené pro integrované systémy. Další informace najdete v tématu o [opětovném nasazení ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Aktualizace typů balíčků

Existují tři typy balíčků aktualizací pro integrované systémy:

-   **Azure Stack aktualizace softwaru**. Společnost Microsoft zodpovídá za kompletní životní cyklus údržby pro balíčky aktualizací softwaru společnosti Microsoft. Tyto balíčky můžou zahrnovat nejnovější aktualizace zabezpečení Windows serveru, aktualizace bez zabezpečení a Azure Stack aktualizace funkcí. Balíčky aktualizací následujících stahujete přímo od Microsoftu.

    Každý balíček aktualizace má odpovídající typ, **úplný** nebo **expresní**. 
 
    Balíčky s **úplnými** aktualizacemi aktualizují fyzické hostitelské operační systémy v jednotce škálování a vyžadují větší časový interval pro správu a údržbu. 

    Balíčky **Express** Update mají rozsah a neaktualizují základní fyzické hostitelské operační systémy.

-   **Azure Stack opravy hotfix**. Společnost Microsoft poskytuje opravy hotfix pro Azure Stack, které řeší konkrétní problém, který je často preventivní nebo časově citlivý. Každá oprava hotfix je vydána spolu s odpovídajícím článkem znalostní báze Microsoft Knowledge Base, který podrobně popisuje problém, příčinu a řešení. Opravy hotfix můžete stáhnout a nainstalovat stejně jako běžné úplné balíčky aktualizací pro Azure Stack. Opravy hotfix jsou kumulativní a můžou se instalovat během několika minut.

-   **Hardware výrobce OEM – aktualizace poskytované dodavatelem** Azure Stack hardwarovým partnerům zodpovídáme za kompletní životní cyklus údržby (včetně pokynů) pro balíčky pro firmware a aktualizace ovladačů souvisejících s hardwarem. Kromě toho Azure Stack hardwaroví partneři vlastní a udržovat pokyny pro veškerý software a hardware na hostiteli životního cyklu hardwaru. Dodavatel hardwaru OEM tyto balíčky aktualizací hostuje na svém vlastním webu pro stažení.

## <a name="when-to-update"></a>Kdy aktualizovat

Tři typy aktualizací jsou vydány s následujícím tempo:

-   **Azure Stack aktualizace softwaru**. Společnost Microsoft obvykle vydává balíčky aktualizací softwaru každý měsíc.

-   **Azure Stack opravy hotfix**. Opravy hotfix jsou časově citlivé verze, které mohou být vydány kdykoli.

-   **Hardware výrobce OEM – aktualizace poskytované dodavatelem** Výrobci hardwaru OEM vydávají své aktualizace podle potřeby.

Chcete-li nadále získávat podporu, je nutné zachovat Azure Stack prostředí na podporované Azure Stack verzi softwaru. Další informace najdete v tématu [zásady pro obsluhu Azure Stack](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Kde získat upozornění na aktualizaci

Oznámení aktualizací se liší v několika faktorech, například připojení k Internetu a typ aktualizace.

- **Aktualizace a opravy softwaru společnosti Microsoft** 

    V okně aktualizace pro Azure Stack instance, které jsou připojené k Internetu, se zobrazí výstraha aktualizace pro aktualizace softwaru společnosti Microsoft a opravy hotfix.

    Pokud vaše instance není připojená a chcete být upozorněni na každou verzi opravy hotfix, přihlaste se k odběru informačního kanálu [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) nebo [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) .

- **Výrobce OEM – aktualizace poskytované dodavatelem**

    Aktualizace OEM budou záviset na výrobci. Budete muset vytvořit komunikační kanál s vaším výrobcem OEM, abyste se dozvěděli, jestli máte aktualizace od výrobce OEM, které je potřeba použít. Další informace o výrobci OEM a procesu aktualizace OEM naleznete v tématu [apply Azure Stack Updates OEM (Original Equipment Manufacturer)](azure-stack-update-oem.md).

## <a name="update-processes"></a>Aktualizovat procesy

Jakmile víte, že máte aktualizaci, použijte ji pomocí následujících kroků.

![Proces aktualizace Azure Stack](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Naplánujte aktualizaci**.

    Připravte Azure Stack, aby se proces aktualizace co nejrychleji napravil, takže bude mít na uživatele minimální dopad. Informujte uživatele o případném výpadku služeb a pak postupujte podle pokynů pro přípravu instance pro aktualizaci. Další kroky k naplánování aktualizace najdete v tématu [Naplánování aktualizace Azure Stack](azure-stack-update-plan.md).

2. **Nahrání a příprava balíčku aktualizace**.

    Pro prostředí Azure Stack připojená k Internetu se Azure Stack aktualizace softwaru a opravy hotfix automaticky naimportují do systému a připravují se na aktualizace.

    V případě Azure Stack prostředí odpojených z Internetu a prostředí se slabým nebo přerušovaným připojením k Internetu se balíčky aktualizací importují do úložiště Azure Stack prostřednictvím portálu pro správu Azure Stack. Další kroky při nahrávání a přípravě balíčku aktualizace najdete v tématu [nahrání a příprava balíčku aktualizace Azure Stack](azure-stack-update-prepare-package.md).

    Všechny balíčky aktualizací OEM se do vašeho prostředí importují ručně bez ohledu na připojení k Internetu v systému Azure Stack. Další kroky pro import a přípravu balíčku aktualizace najdete v tématu [nahrání a příprava balíčku aktualizace Azure Stack](azure-stack-update-prepare-package.md).

3. **Použijte aktualizaci**.

    Použijte aktualizaci pomocí okna **aktualizovat** v Azure Stack. Během aktualizace Sledujte průběh aktualizace a vyřešte potíže. Další informace najdete v tématu [použití aktualizace Azure Stack](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Poskytovatel prostředku aktualizace

Azure Stack obsahuje poskytovatele prostředků aktualizace, který zpracovává aplikaci aktualizací softwaru společnosti Microsoft. Tento poskytovatel kontroluje, zda jsou aktualizace aplikovány na všechny fyzické hostitele, Service Fabric aplikací a modulech runtime a na všech virtuálních počítačích infrastruktury a jejich přidružených službách.

Při instalaci aktualizací můžete zobrazit stav vysoké úrovně, protože proces aktualizace cílí na různé subsystémy v Azure Stack (například fyzické hostitele a virtuální počítače infrastruktury).

## <a name="next-steps"></a>Další kroky

- Chcete-li zahájit proces aktualizace, postupujte podle kroků v tématu [Naplánování aktualizace Azure Stack](azure-stack-update-plan.md).
- Informace o tom, jaké verze Azure Stack jsou podporovány, najdete v tématu [zásady služby Azure Stack Servicing](azure-stack-servicing-policy.md).  
- Další informace o aktuálních a nejnovějších aktualizacích najdete v [poznámkách k verzi Azure Stack](azure-stack-release-notes-security-updates-1907.md).
