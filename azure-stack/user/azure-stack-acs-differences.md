---
title: Rozdíly a požadavky na úložiště Azure Stack | Microsoft Docs
description: Pochopte rozdíly mezi úložištěm Azure Stack a úložištěm Azure spolu s Azure Stackmi požadavky na nasazení.
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
ms.date: 10/2/2019
ms.author: mabrigg
ms.reviwer: xiaofmao
ms.lastreviewed: 01/30/2019
ms.openlocfilehash: e2680a91aa2b9232eb86de4338d1198fb515e6d3
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824728"
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Azure Stack úložiště: Rozdíly a aspekty

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack Storage je sada cloudových služeb úložiště v Microsoft Azure Stack. Služba Azure Stack Storage poskytuje funkce pro správu objektů blob, tabulek, front a účtů s sémantikou konzistentní vzhledem k Azure.

Tento článek shrnuje známé Azure Stack nerozdílů v úložišti služeb Azure Storage. Také uvádí, co je potřeba vzít v úvahu při nasazení Azure Stack. Další informace o rozdílech na vysoké úrovni mezi globálním Azure a Azure Stack najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

## <a name="cheat-sheet-storage-differences"></a>Tahák list: Rozdíly v úložišti

| Funkce | Azure (Global) | Azure Stack |
| --- | --- | --- |
|File Storage|Podporované cloudové sdílené složky SMB|Zatím nepodporováno
|Šifrování služby Azure Storage pro neaktivní neaktivní data|256 šifrování AES. Podpora šifrování pomocí klíčů spravovaných zákazníkem v Key Vault.|BitLocker 128-bit AES Encryption. Šifrování pomocí klíčů spravovaných zákazníkem se nepodporuje.
|Typ účtu úložiště|Účty úložiště pro obecné účely V1, v2 a BLOB|Jenom pro obecné účely v1.
|Možnosti replikace|Místně redundantní úložiště, geograficky redundantní úložiště, geograficky redundantní úložiště s přístupem pro čtení a úložiště redundantní v zóně|Místně redundantní úložiště.
|Premium Storage|Poskytněte úložiště s vysokým výkonem a nízkou latencí. Podporují se jenom objekty blob stránky v účtech Premium Storage.|Dá se zřídit, ale bez omezení výkonu ani záruky. Neblokuje použití objektů blob bloku, doplňovacích objektů blob, tabulek a front v účtech Premium Storage.
|Spravované disky|Podporovaná verze Premium a Standard|Podporováno při použití verze 1808 nebo novější.
|Název objektu blob|1 024 znaků (2 048 bajtů)|880 znaků (1 760 bajtů)
|Maximální velikost objektu blob bloku|4,75 TB (100 MB X 50 000 bloků)|4,75 TB (100 MB x 50 000 bloků) pro aktualizaci 1802 nebo novější verzi. 50 000 X 4 MB (přibližně 195 GB) pro předchozí verze.
|Kopie snímku objektu blob stránky|Zálohování disků nespravovaných virtuálních počítačů Azure připojených k běžícímu virtuálnímu počítači|Zatím se nepodporuje.
|Kopie přírůstkového snímku objektu blob stránky|Podporované objekty blob stránky Azure úrovně Premium a Standard|Zatím se nepodporuje.
|Fakturace objektu blob stránky|Poplatky se účtují pro jedinečné stránky, ať už jsou v objektu blob, nebo ve snímku. Neúčtují se další poplatky za snímky přidružené k objektu blob, dokud se neaktualizuje základní objekt BLOB.|Účtují se poplatky za základní objekty BLOB a assiociated snímky. Za každý jednotlivý snímek se účtují další poplatky.
|Vrstvy úložiště pro Blob Storage|Horké, studené a archivní úrovně úložiště.|Zatím se nepodporuje.
|Obnovitelné odstranění pro úložiště objektů BLOB|Obecné dostupné|Zatím se nepodporuje.
|Maximální velikost objektu blob stránky|8 TB|1 TB
|Velikost stránky objektu blob stránky|512 bajtů|4 KB
|Klíč oddílu tabulky a velikost klíče řádku|1 024 znaků (2 048 bajtů)|400 znaků (800 bajtů)
|Snímek objektu BLOB|Maximální počet snímků jednoho objektu BLOB není omezený.|Maximální počet snímků jednoho objektu BLOB je 1 000.
|Ověřování Azure AD pro úložiště|Ve verzi Preview|Zatím se nepodporuje.
|Neměnné objekty blob|Obecné dostupné|Zatím se nepodporuje.
|Pravidla brány firewall a virtuální sítě pro úložiště|Obecné dostupné|Zatím se nepodporuje.|

Existují také rozdíly v metrikách úložiště:

* Data transakcí v metrikách úložiště nerozlišují vnitřní nebo externí šířku pásma sítě.
* Data transakcí v metrikách úložiště nezahrnují přístup virtuálních počítačů k připojeným diskům.

## <a name="api-version"></a>Verze API

Azure Stack Storage podporuje následující verze:

Rozhraní API služby Azure Storage Services:

1811 aktualizace nebo novější verze:

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

Rozhraní API pro správu služby Azure Storage Services:

1811 aktualizace nebo novější verze:

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

Další informace o Azure Stack podporovaných klientských knihovnách pro úložiště najdete v tématu: [Začínáme s nástroji pro vývoj Azure Stackho úložiště](azure-stack-storage-dev.md)

## <a name="next-steps"></a>Další kroky

* [Začínáme s nástroji pro vývoj Azure Stackho úložiště](azure-stack-storage-dev.md)
* [Použití nástrojů pro přenos dat pro Azure Stack Storage](azure-stack-storage-transfer.md)
* [Seznámení s Azure Stack Storage](azure-stack-storage-overview.md)
