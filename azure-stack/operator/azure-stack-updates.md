---
title: Správa aktualizací v centru Azure Stack | Microsoft Docs
description: Naučte se spravovat aktualizace v centru Azure Stack.
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
ms.date: 10/01/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 9fff0249c2a1c43d7742236f64492173c501eeec
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881718"
---
# <a name="manage-updates-in-azure-stack-hub-overview"></a>Přehled správy aktualizací v centru Azure Stack

Úplné a expresní aktualizace, opravy hotfix, stejně jako aktualizace ovladačů a firmwaru od výrobce OEM (Original Equipment Manufacturer), udržují Azure Stack centra aktuální. Tento článek popisuje různé typy aktualizací, kdy očekávat jejich vydání a kde najdete další informace o aktuální verzi.

> [!Note]  
> Balíčky aktualizací centra Azure Stack nelze použít na Azure Stack Development Kit (ASDK). Balíčky aktualizací jsou navržené pro integrované systémy. Další informace najdete v tématu o [opětovném nasazení ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy).

## <a name="update-package-types"></a>Aktualizace typů balíčků

Existují tři typy balíčků aktualizací pro integrované systémy:

-   **Azure Stack aktualizace softwaru centra**. Společnost Microsoft zodpovídá za kompletní životní cyklus údržby pro balíčky aktualizací softwaru společnosti Microsoft. Tyto balíčky můžou zahrnovat nejnovější aktualizace zabezpečení Windows serveru, aktualizace nesouvisející se zabezpečením a aktualizace funkcí centra Azure Stack. Balíčky aktualizací následujících stahujete přímo od Microsoftu.

    Každý balíček aktualizace má odpovídající typ, **úplný** nebo **expresní**. 
 
    Balíčky s **úplnými** aktualizacemi aktualizují fyzické hostitelské operační systémy v jednotce škálování a vyžadují větší časový interval pro správu a údržbu. 

    Balíčky **Express** Update mají rozsah a neaktualizují základní fyzické hostitelské operační systémy.

-   **Azure Stack opravy hotfix centra**. Microsoft poskytuje opravy hotfix pro Azure Stack centrum, které řeší konkrétní problém, který je často preventivní nebo časově citlivý. Každá oprava hotfix je vydána spolu s odpovídajícím článkem znalostní báze Microsoft Knowledge Base, který podrobně popisuje problém, příčinu a řešení. Můžete stahovat a instalovat opravy hotfix stejně jako běžné úplné balíčky aktualizací pro centrum Azure Stack. Opravy hotfix jsou kumulativní a můžou se instalovat během několika minut.

-   **Hardware výrobce OEM – aktualizace poskytované dodavatelem** Služby Azure Stack hub jsou zodpovědné za kompletní životní cyklus údržby (včetně pokynů) pro balíčky pro firmware a aktualizace ovladačů souvisejících s hardwarem. Kromě toho Azure Stack hub hardware partneři vlastní a udržují pokyny pro veškerý software a hardware v hostiteli životního cyklu hardwaru. Dodavatel hardwaru OEM tyto balíčky aktualizací hostuje na svém vlastním webu pro stažení.

## <a name="when-to-update"></a>Kdy aktualizovat

Tři typy aktualizací jsou vydány s následujícími tempo:

-   **Azure Stack aktualizace softwaru centra**. Společnost Microsoft obvykle vydává balíčky aktualizací softwaru každý měsíc.

-   **Azure Stack opravy hotfix centra**. Opravy hotfix jsou časově citlivé verze, které mohou být vydány kdykoli.

-   **Hardware výrobce OEM – aktualizace poskytované dodavatelem** Výrobci hardwaru OEM vydávají své aktualizace podle potřeby.

Chcete-li nadále získávat podporu, je nutné zachovat prostředí Azure Stack hub v podporované verzi softwaru Azure Stack hub. Další informace najdete v tématu [zásady obsluhy centra Azure Stack](azure-stack-update-servicing-policy.md).

## <a name="where-to-get-notice-of-an-update"></a>Kde získat upozornění na aktualizaci

Oznámení aktualizací se liší v několika faktorech, například připojení k Internetu a typ aktualizace.

- **Aktualizace a opravy softwaru společnosti Microsoft** 

    V okně aktualizace pro Azure Stack, které jsou připojené k Internetu, se zobrazí upozornění aktualizace pro aktualizace softwaru společnosti Microsoft a opravy hotfix. Pokud se okno aktualizace nezobrazí, restartujte virtuální počítač řadiče pro správu infrastruktury.

    Pokud vaše instance není připojená a chcete být upozorněni na každou verzi opravy hotfix, přihlaste se k odběru informačního kanálu [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) nebo [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) .

- **Výrobce OEM – aktualizace poskytované dodavatelem**

    Aktualizace OEM budou záviset na výrobci. Budete muset vytvořit komunikační kanál s vaším výrobcem OEM, abyste si mohli uvědomit o aktualizacích od výrobce OEM, které je potřeba použít. Další informace o výrobci OEM a procesu aktualizace OEM najdete v tématu [použití aktualizací pro výrobce OEM (Original Equipment Manufacturer) v Azure Stack](azure-stack-update-oem.md).

## <a name="update-processes"></a>Aktualizovat procesy

Jakmile víte, že máte aktualizaci, použijte ji pomocí následujících kroků.

![Proces aktualizace centra Azure Stack](./media/azure-stack-updates/azure-stack-update-process.png)

1. **Naplánujte aktualizaci**.

    Připraví centrum Azure Stack, aby se proces aktualizace co nejrychleji provedl, takže uživatelé mají minimální dopad na uživatele. Informujte uživatele o případném výpadku služeb a pak postupujte podle pokynů pro přípravu instance pro aktualizaci. Další kroky k naplánování aktualizace najdete v tématu [Kontrolní seznam aktivity aktualizace centra Azure Stack](release-notes-checklist.md).

2. **Nahrání a příprava balíčku aktualizace**.

    Pro Azure Stack prostředí centra Azure Stack připojená k Internetu se automaticky naimportují aktualizace softwaru centra a opravy hotfix do systému a připravují se na aktualizace.

    V případě prostředí a prostředí rozbočovače Internet Azure Stack odpojeného pomocí Internetu a prostředí se slabým nebo přerušovaným připojením k Internetu se balíčky aktualizací importují do úložiště Azure Stack hub prostřednictvím portálu pro správu Azure Stack centra. Další kroky při nahrávání a přípravě balíčku aktualizace najdete v tématu [nahrání a příprava balíčku aktualizace centra Azure Stack](azure-stack-update-prepare-package.md).

    Všechny balíčky aktualizací OEM se do vašeho prostředí importují ručně, bez ohledu na připojení k Internetu v systému Azure Stack hub. Další kroky pro import a přípravu balíčku aktualizace najdete v tématu [nahrání a příprava balíčku aktualizace centra Azure Stack](azure-stack-update-prepare-package.md).

3. **Použijte aktualizaci**.

    Použijte aktualizaci pomocí okna **aktualizovat** v centru Azure Stack. Během aktualizace Sledujte průběh aktualizace a vyřešte potíže. Další informace najdete v tématu [použití aktualizace centra Azure Stack](azure-stack-apply-updates.md).

## <a name="the-update-resource-provider"></a>Poskytovatel prostředku aktualizace

Azure Stack hub obsahuje poskytovatele prostředků aktualizace, který zpracovává aplikaci aktualizací softwaru společnosti Microsoft. Tento poskytovatel kontroluje, zda jsou aktualizace aplikovány na všechny fyzické hostitele, Service Fabric aplikací a modulech runtime a na všech virtuálních počítačích infrastruktury a jejich přidružených službách.

Při instalaci aktualizací můžete zobrazit stav vysoké úrovně, protože proces aktualizace cílí na různé subsystémy v Azure Stackovém centru (například na fyzických hostitelích a virtuálních počítačích infrastruktury).

## <a name="next-steps"></a>Další kroky

- Chcete-li zahájit proces aktualizace, postupujte podle kroků v tématu [Azure Stack kontrolní seznam aktivity aktualizace centra](release-notes-checklist.md).
- Informace o tom, jaké verze centra Azure Stack podporují, najdete v tématu [zásady obsluhy centra Azure Stack](azure-stack-servicing-policy.md).  
- Další informace o aktuálních a nejnovějších aktualizacích najdete v [poznámkách k verzi centra Azure Stack](release-notes.md).
