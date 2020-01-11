---
title: Úvod do úložiště centra Azure Stack | Microsoft Docs
description: Přečtěte si o Azure Stack služby úložiště hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 50ca47c66e31417378e5088bf6c0c9f7cb406cc1
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878319"
---
# <a name="introduction-to-azure-stack-hub-storage"></a>Úvod do úložiště centra Azure Stack

## <a name="overview"></a>Přehled

Úložiště centra Azure Stack je sada služeb cloudového úložiště, která odpovídá službám poskytovaným Azure Storage. Mezi tyto služby patří objekty blob, tabulky a fronty.

## <a name="azure-stack-hub-storage-services"></a>Služby úložiště Azure Stack hub

Úložiště centra Azure Stack poskytuje následující tři služby:

- **Blob Storage**

    Úložiště objektů BLOB ukládá nestrukturovaná data objektů. Objekt BLOB může být libovolný typ textu nebo binárních dat, jako je dokument, soubor médií nebo instalační program aplikace.

- **Table Storage**

    Úložiště tabulek ukládá strukturované datové sady. Table Storage je úložiště dat NoSQL klíčového atributu, které umožňuje rychlý vývoj a rychlý přístup k velkým objemům dat.

- **Queue Storage**

    Queue Storage poskytuje spolehlivé zasílání zpráv pro zpracování pracovních postupů a komunikaci mezi komponentami cloudových služeb.

Účet úložiště centra Azure Stack je zabezpečený účet, který poskytuje přístup ke službám v úložišti centra Azure Stack. Váš účet úložiště poskytuje jedinečný obor názvů pro vaše prostředky úložiště. Následující diagram znázorňuje vztahy mezi prostředky úložiště centra Azure Stack v účtu úložiště:

![Přehled úložiště centra Azure Stack](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Úložiště blobů

Pro uživatele s velkým množstvím nestrukturovaných dat objektů, které se mají uložit v cloudu, nabízí úložiště objektů BLOB efektivní a škálovatelné řešení. Úložiště objektů blob můžete použít k ukládání obsahu, jako je například:

- Dokumenty
- Sociální data, jako jsou fotografie, videa, hudba nebo blogy
- Zálohy souborů, počítačů, databází a zařízení
- Obrázky a text pro webové aplikace
- Konfigurační data pro cloudové aplikace
- Velké objemy dat, jako jsou protokoly a další velké datové sady

Každý objekt blob se organizuje do kontejneru. Kontejnery také nabízejí praktický způsob přiřazení zásad zabezpečení skupinám objektů. Účet úložiště může obsahovat libovolný počet kontejnerů a kontejner může obsahovat libovolný počet objektů BLOB (až do limitu účtu úložiště).

Úložiště objektů BLOB nabízí tři typy objektů BLOB:

- **Objekty blob bloku**

    Objekty blob bloku jsou optimalizované pro streamování a ukládání objektů cloudu. Jsou dobrou volbou pro ukládání dokumentů, mediálních souborů, záloh a dalších podobných souborů.

- **Doplňovací objekty blob**

    Doplňovací objekty blob jsou podobné objektům blob bloku, ale jsou optimalizované pro doplňovací operace. Doplňovací objekt blob se může aktualizovat jen přidáním nového bloku na konec. Doplňovací objekty blob jsou dobrou volbou pro takové scénáře, jako je například protokolování, kde se nová data potřebují zapisovat jen na konec objektu blob.

- **Objekty blob stránky**

    Objekty blob stránky jsou optimalizované tak, aby představovaly disky IaaS a podporovaly náhodné zápisy o velikosti až 1 TB. IaaS disk, který je připojen k virtuálnímu počítači centra Azure Stack, je VHD uložený jako objekt blob stránky.

### <a name="table-storage"></a>Úložiště tabulek

Moderní aplikace často vyžadují úložiště dat s větší škálovatelností a flexibilitou než předchozí generace softwaru. Table Storage nabízí vysoce dostupné, široce škálovatelné úložiště, aby se vaše aplikace mohla automaticky škálovat tak, aby splňovala požadavky uživatelů. Table Storage je úložiště NoSQLch klíčů a atributů od Microsoftu – má návrh bez schématu, takže se liší od tradičních relačních databází. S úložištěm dat bez schématu je snadné přizpůsobit data podle potřeb vaší aplikace. Úložiště tabulek je snadno použitelné, takže vývojáři můžou rychle vytvářet aplikace.

Table Storage je úložiště atributů klíčů, což znamená, že každá hodnota v tabulce je uložená s názvem typové vlastnosti. Název vlastnosti se používá pro filtrování a zadání kritérií výběru. Kolekce vlastností a jejich hodnot tvoří entitu. Vzhledem k tomu, že tabulka úložiště je bez schématu, dvě entity ve stejné tabulce mohou obsahovat různé kolekce vlastností a tyto vlastnosti mohou být různých typů.

Tabulkové úložiště můžete použít k ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení a dalších typů metadat, které vaše služba vyžaduje. V dnešních internetových aplikacích databáze NoSQL jako úložiště tabulek nabízí oblíbenou alternativu k tradičním relačním databázím.

Účet úložiště může obsahovat libovolný počet tabulek a tabulka může obsahovat libovolný počet entit až do limitu kapacity účtu úložiště.

### <a name="queue-storage"></a>Úložiště front

Při navrhování aplikací pro škálování se součásti aplikací často odpojí, aby se mohly škálovat nezávisle. Queue Storage poskytuje spolehlivé řešení zasílání zpráv pro asynchronní komunikaci mezi součástmi aplikací, ať už běží v cloudu, na ploše, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Účet úložiště může obsahovat libovolný počet front a fronta může obsahovat libovolný počet zpráv (až do limitu kapacity účtu úložiště). Jednotlivé zprávy můžou mít velikost až 64 KB.

## <a name="next-steps"></a>Další kroky

- [Úložiště konzistentní s Azure: rozdíly a požadavky](azure-stack-acs-differences.md)

- Další informace o Azure Storage najdete v tématu [Úvod do Microsoft Azure Storage](/azure/storage/common/storage-introduction)
