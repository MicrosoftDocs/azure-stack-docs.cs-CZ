---
title: Rozdíly a požadavky na úložiště centra Azure Stack
titleSuffix: Azure Stack Hub
description: Seznamte se s rozdíly mezi Azure Stack úložiště centra a Azure Storage společně s Azure Stackmi požadavky na nasazení centra.
author: mattbriggs
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviwer: jiahan
ms.lastreviewed: 08/12/2020
ms.openlocfilehash: e49092e87f8e6801343ae0550aff4c71dd40408b
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103065"
---
# <a name="azure-stack-hub-storage-differences-and-considerations"></a>Úložiště centra Azure Stack: rozdíly a požadavky

Úložiště centra Azure Stack je sada cloudových služeb úložiště v centru Microsoft Azure Stack. Úložiště centra Azure Stack poskytuje funkce pro správu objektů blob, tabulek, front a účtů s sémantikou konzistentní vzhledem k Azure.

Tento článek shrnuje známé rozdíly v úložištích Azure Stack centra od služby Azure Storage. Také uvádí, co je potřeba vzít v úvahu při nasazení centra Azure Stack. Další informace o hlavních rozdílech mezi globálním centrem Azure a Azure Stack najdete v článku [důležité informace](azure-stack-considerations.md) .

## <a name="cheat-sheet-storage-differences"></a>Tahák list: rozdíly v úložišti

| Příznak | Azure (Global) | Azure Stack Hub |
| --- | --- | --- |
|File Storage|Podporuje cloudové sdílené složky SMB. | Zatím se nepodporuje.
|Šifrování služby Azure Storage pro neaktivní neaktivní data|256 šifrování AES. Podpora šifrování pomocí klíčů spravovaných zákazníkem v Key Vault.|BitLocker 128-bit AES Encryption. Šifrování pomocí klíčů spravovaných zákazníkem se nepodporuje.
|Typ účtu úložiště|Účty úložiště pro obecné účely V1, v2 a BLOB. |Jenom pro obecné účely v1.
|Možnosti replikace|Místně redundantní úložiště, geograficky redundantní úložiště, geograficky redundantní úložiště s přístupem pro čtení a úložiště redundantní v zóně. |Místně redundantní úložiště.
|Premium Storage|Poskytněte úložiště s vysokým výkonem a nízkou latencí. Podporují se jenom objekty blob stránky v účtech Premium Storage.|Dá se zřídit, ale bez omezení výkonu ani záruky. V účtech Premium Storage by neblokovala použití objektů blob bloku, doplňovací objekty blob, tabulky a fronty.
|Spravované disky|Podpora úrovně Premium a Standard. |Podporováno při použití verze 1808 nebo novější.
|Název objektu blob|1 024 znaků (2 048 bajtů). |880 znaků (1 760 bajtů).
|Maximální velikost objektu blob bloku|4,75 TB (100 MB X 50 000 bloků). |4,75 TB (100 MB x 50 000 bloků) pro aktualizaci 1802 nebo novější verzi. 50 000 X 4 MB (přibližně 195 GB) pro předchozí verze.
|Kopie snímku objektu blob stránky|Zálohování disků nespravovaných virtuálních počítačů Azure připojených k běžícímu virtuálnímu počítači. |Zatím se nepodporuje.
|Kopie přírůstkového snímku objektu blob stránky|Podporovány jsou objekty blob stránky Azure úrovně Premium a Standard. |Zatím se nepodporuje.
|Fakturace objektu blob stránky|Poplatky se účtují pro jedinečné stránky, ať už jsou v objektu blob, nebo ve snímku. Dokud se základní objekt BLOB neaktualizuje, neúčtují se další poplatky za snímky přidružené k objektu BLOB.|Účtují se poplatky za základní objekt BLOB a přidružené snímky. Za každý jednotlivý snímek se účtují další poplatky.
|Vrstvy úložiště pro Blob Storage|Horké, studené a archivní úrovně úložiště.|Zatím se nepodporuje.
|Obnovitelné odstranění pro úložiště objektů BLOB|Obecné k dispozici. |Zatím se nepodporuje.
|Maximální velikost objektu blob stránky|8 TB. |1 TB. 
|Velikost stránky objektu blob stránky|512 bajtů. |4 KB. 
|Klíč oddílu tabulky a velikost klíče řádku|1 024 znaků (2 048 bajtů).|400 znaků (800 bajtů).
|Snímek objektu BLOB|Maximální počet snímků jednoho objektu BLOB není omezený.|Maximální počet snímků jednoho objektu BLOB je 1 000.
|Ověřování Azure AD pro úložiště|Ve verzi Preview. |Zatím se nepodporuje.
|Neměnné objekty blob|Obecné k dispozici. |Podporováno při použití verze 2008 nebo novější.
|Pravidla brány firewall a virtuální sítě pro úložiště|Obecné k dispozici. |Zatím se nepodporuje.|

Existují také rozdíly v metrikách úložiště:

* Data transakcí v metrikách úložiště nerozlišují vnitřní nebo externí šířku pásma sítě.
* Data transakcí v metrikách úložiště nezahrnují přístup virtuálních počítačů k připojeným diskům.

## <a name="api-version"></a>Verze rozhraní API

Azure Stack úložiště centra podporuje následující verze:

Rozhraní API služby Azure Storage Services:

::: moniker range=">=azs-2008"

2008 aktualizace nebo novější verze:
- [2019-07-07](/rest/api/storageservices/version-2019-07-07)
- [2019-02-02](/rest/api/storageservices/version-2019-02-02)
- [2018-11-09](/rest/api/storageservices/version-2018-11-09)
- [2018-03-28](/rest/api/storageservices/version-2018-03-28)
- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

::: moniker-end

aktualizace 2005:

- [2019-02-02](/rest/api/storageservices/version-2019-02-02)
- [2018-11-09](/rest/api/storageservices/version-2018-11-09)
- [2018-03-28](/rest/api/storageservices/version-2018-03-28)
- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

Předchozí verze:

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05)

Rozhraní API pro správu služby Azure Storage Services:

::: moniker range=">=azs-2008"

2008 aktualizace nebo novější verze:
- [2018-02-01](/rest/api/storagerp/)
- [2017-10-01](/rest/api/storagerp/)
- [2017-06-01](/rest/api/storagerp/)
- [2016-12-01](/rest/api/storagerp/)
- [2016-05-01](/rest/api/storagerp/)
- [2016-01-01](/rest/api/storagerp/)
- [2015-06-15](/rest/api/storagerp/)
- [2015-05-01 – Preview](/rest/api/storagerp/)

::: moniker-end

aktualizace 1811 Update na 2005:

- [2017-10-01](/rest/api/storagerp/)
- [2017-06-01](/rest/api/storagerp/)
- [2016-12-01](/rest/api/storagerp/)
- [2016-05-01](/rest/api/storagerp/)
- [2016-01-01](/rest/api/storagerp/)
- [2015-06-15](/rest/api/storagerp/)
- [2015-05-01 – Preview](/rest/api/storagerp/)

Předchozí verze:

- [2016-01-01](/rest/api/storagerp/)
- [2015-06-15](/rest/api/storagerp/)
- [2015-05-01 – Preview](/rest/api/storagerp/)

## <a name="powershell-version"></a>Verze Powershellu

V případě modulu úložiště PowerShell si uvědomte verzi, která je kompatibilní s REST API.

| Modul | Podporovaná verze | Využití |
|---|---|---|
| Azure.Storage | [4.5.0](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0) | Spravuje objekty blob, fronty a tabulky v Azure Stack účty úložiště centra. |
| Az.Storage | [5.0.4](https://www.powershellgallery.com/packages/Az.Storage/5.0.4) | Vytvoří a spravuje účty úložiště v centru Azure Stack. |

Další informace o podporovaných klientských knihovnách úložiště služby Azure Stack hub najdete v tématu [Začínáme s Azure Stack centrum pro vývoj úložiště](azure-stack-storage-dev.md).

## <a name="next-steps"></a>Další kroky

* [Začínáme s nástroji pro vývoj pro úložiště Azure Stack hub](azure-stack-storage-dev.md)
* [Použití nástrojů pro přenos dat pro úložiště Azure Stack hub](azure-stack-storage-transfer.md)
* [Úvod do úložiště centra Azure Stack](azure-stack-storage-overview.md)
