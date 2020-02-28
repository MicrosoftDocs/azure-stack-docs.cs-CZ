---
title: Podporované metriky pro Azure Monitor v centru Azure Stack
description: Přečtěte si o podporovaných metrikách pro Azure Monitor v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: b90458e58054cd0f42564558fd3cb5a8226d6c5d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702257"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack-hub"></a>Podporované metriky pro Azure Monitor v centru Azure Stack

Metriky z Azure monitoru v Azure Stackovém centru se načítají stejným způsobem jako v globálním Azure. Můžete vytvářet míry na portálu, je získat z rozhraní REST API nebo dotazovat pomocí Powershellu nebo rozhraní příkazového řádku.

V následujících tabulkách jsou uvedeny metriky dostupné s kanálem metriky Azure Monitor v centru Azure Stack. K dotazování a přístupu k těmto metrikám použijte verzi rozhraní API verze **2018-01-01** API. Další informace o profilech rozhraní API a centru Azure Stack najdete v tématu [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Rozměry |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Procento CPU | Procento CPU | Procento | Průměr | Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači. | Žádné dimenze |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Rozměry |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Použitá kapacita | Bajty | Průměr | Kapacita využitého účtu | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | Bajty | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | GeoType ApiName |
| Výchozí přenos dat | Výchozí přenos dat | Bajty | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Rozměry |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Kapacita služby Blob | Bajty | Celkem | Velikost úložiště využitá Blob service účtu úložiště v bajtech | BlobType |
| BlobCount | Počet objektů blob | Počet | Celkem | Počet objektů BLOB v Blob service účtu úložiště. | BlobType |
| ContainerCount | Počet kontejnerů služby Blob | Počet | Průměr | Počet kontejnerů v Blob service účtu úložiště. | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | Bajty | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | GeoType ApiName |
| Výchozí přenos dat | Výchozí přenos dat | Bajty | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Rozměry |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Kapacita služby Table | Bajty | Průměr | Velikost úložiště využitá Table service účtu úložiště v bajtech | Žádné dimenze |
| TableCount | Počet tabulek | Počet | Průměr | Počet tabulek v Table service účtu úložiště | Žádné dimenze |
| TableEntityCount | Počet entit tabulek | Počet | Průměr | Počet entit tabulky v Table service účtu úložiště | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | Bajty | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | GeoType ApiName |
| Výchozí přenos dat | Výchozí přenos dat | Bajty | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Rozměry |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Kapacita služby Queue | Bajty | Průměr | Velikost úložiště využitá Služba front účtu úložiště v bajtech | Žádné dimenze |
| QueueCount | Počet front | Počet | Průměr | Počet front v Služba front účtu úložiště. | Žádné dimenze |
| QueueMessageCount | Počet zpráv fronty | Počet | Průměr | Přibližný počet zpráv ve frontě v Služba front účtu úložiště. | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | Bajty | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | GeoType ApiName |
| Výchozí přenos dat | Výchozí přenos dat | Bajty | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. | GeoType ApiName |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [službě Azure monitor v centru Azure Stack](azure-stack-metrics-azure-data.md).
