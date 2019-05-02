---
title: Azure stack úložiště rozdíly a aspekty | Dokumentace Microsoftu
description: Znát rozdíly mezi úložiště služby Azure stack a Azure storage, spolu s důležité informace o nasazení služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: 3d1fb9e7a3d25e9eeab0f1f2a0cc1a48ed6112a2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985987"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Úložiště Azure Stack: Rozdíly a aspekty

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Úložiště Azure Stack je sada cloudových služeb úložiště ve službě Microsoft Azure Stack. Úložiště Azure Stack nabízí objektů blob, tabulky, fronty a funkce správy účtu se sémantikou konzistentních s Azure.

Tento článek shrnuje známé rozdíly Azure Stack Storage ze služby Azure Storage. Také uvádí, co je třeba zvážit při nasazení Azure Stack. Další informace o nejvýraznějších rozdílů mezi globální Azure a Azure Stack, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

## <a name="cheat-sheet-storage-differences"></a>Tahák: Rozdíly úložiště

| Funkce | Azure (globální) | Azure Stack |
| --- | --- | --- |
|File Storage|Cloudové sdílené složky SMB podporované|Není dosud podporován.
|Šifrování služby Azure storage pro neaktivní uložená data|256bitového šifrování AES. Podpora šifrování pomocí klíčů spravovaných zákazníkem ve službě Key Vault.|Nástroj BitLocker 128bitové šifrování AES. Šifrování pomocí klíčů spravovaných zákazníkem není podporováno.
|Typ účtu úložiště|Účty pro obecné účely V1, V2 a Blob storage|Pouze pro obecné účely V1.
|Možnosti replikace|Místně redundantní úložiště, geograficky redundantní úložiště, geograficky redundantní úložiště jen pro čtení a zónově redundantní úložiště|Místně redundantní úložiště.
|Premium Storage|Plně podporované.|Je možné zřídit, ale bez omezení výkonu nebo záruk.
|Spravované disky|Premium a standard podporována|Podporovány, pokud používáte verzi 1808 nebo novější.
|Název objektu blob|1 024 znaků (2 048 bajtů)|880 znaků (1,760 bajty)
|Maximální velikost objektu blob bloku|4,75 TB (100 MB × 50 000 bloků)|4,75 TB (100 MB × 50 000 bloků) pro verzi 1802 update nebo novější verze. 50 000 × 4 MB (přibližně 195 GB) pro předchozí verze.
|Kopie snímků objektů blob stránky|Zálohování Azure nespravovaných disků virtuálních počítačů připojený spuštěný virtuální počítač nepodporuje|Ještě není podporované.
|Kopie přírůstkový snímek objektu blob stránky|Premium a objekty BLOB stránky standardní Azure, které jsou podporovány|Ještě není podporované.
|Fakturace objektů blob stránky|Poplatky se účtují pro jedinečný stránky, ať už jsou v objektu blob nebo ve snímku. Nebude se účtovat další poplatky za snímky přidružené tomuto objektu blob dokud základní objekt blob se aktualizuje.|Poplatky se účtují pro základní snímky objektů blob a assiociated. Bude se účtovat další poplatky za každé jednotlivé snímku.
|Úrovně úložiště pro ukládání objektů blob|Horké studené a archivní úroveň úložiště.|Ještě není podporované.
|Obnovitelného odstranění pro úložiště objektů blob|Obecně dostupná|Ještě není podporované.
|Maximální velikost objektu blob stránky|8 TB|1 TB
|Velikost stránky objektu blob stránky|512 bajtů|4 KB
|Velikost klíče tabulky klíče oddílu a řádku|1 024 znaků (2 048 bajtů)|až 400 znaků (800 bajtů)
|Snímek objektu BLOB|Maximální počet snímků jeden objekt blob není omezený.|Maximální počet snímků jeden objekt blob je 1 000.
|Ověřování Azure AD pro úložiště|Ve verzi Preview|Ještě není podporované.
|Neměnné objekty BLOB|Obecně dostupná|Ještě není podporované.
|Brána firewall a pravidla virtuální sítě pro úložiště|Obecně dostupná|Ještě není podporované.|

Existují také rozdíly pomocí metrik storage:

* Transakce data v metrikách storage nerozlišují šířky pásma sítě interní nebo externí.
* Transakce data v metrikách storage neobsahuje virtuální počítač přístup k připojené disky.

## <a name="api-version"></a>Verze API

S úložištěm Azure Stack se podporují následující verze:

Rozhraní API pro služby Azure Storage:

1811 update nebo novější verze:

- [2017-11-09](https://docs.microsoft.com/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](https://docs.microsoft.com/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Předchozí verze:

- [2017-04-17](https://docs.microsoft.com/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](https://docs.microsoft.com/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](https://docs.microsoft.com/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](https://docs.microsoft.com/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](https://docs.microsoft.com/rest/api/storageservices/version-2015-04-05)

Rozhraní API pro správu služby Azure Storage:

1811 update nebo novější verze:

- [2017-10-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2017-06-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-12-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-05-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Předchozí verze:

- [2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-06-15](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)
- [2015-05-01-preview](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN)

Další informace o klientských knihoven pro úložiště Azure Stack podporovány naleznete v tématu: [Začínáme s Azure Stack nástroje pro vývoj úložišť](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Další postup

* [Začínáme s Azure Stack Storage vývojové nástroje](azure-stack-storage-dev.md)
* [Použití nástrojů pro přenos dat pro úložiště Azure Stack](azure-stack-storage-transfer.md)
* [Úvod do úložiště Azure Stack](azure-stack-storage-overview.md)
