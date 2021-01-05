---
title: Azure Monitor podporované metriky v centru Azure Stack
description: Přečtěte si o podporovaných metrikách pro Azure Monitor v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 50064d480c1900fdd03fd08c182427b1a77399d5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874026"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack-hub"></a>Podporované metriky pro Azure Monitor v centru Azure Stack

Metriky z Azure monitoru v Azure Stackovém centru se načítají stejným způsobem jako v globálním Azure. Můžete vytvořit své míry na portálu, získat je z REST API nebo je pomocí PowerShellu nebo rozhraní příkazového řádku dotazovat.

V následujících tabulkách jsou uvedeny metriky dostupné s kanálem metriky Azure Monitor v centru Azure Stack. K dotazování a přístupu k těmto metrikám použijte verzi rozhraní API verze **2018-01-01** API. Další informace o profilech rozhraní API a centru Azure Stack najdete v tématu [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace | Popis | Dimenze |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Procento CPU | Procento CPU | Procento | Průměr | Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači. | Žádné dimenze |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Využitá kapacita | Bajty | Průměr | Kapacita využitého účtu | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | ResponseType, typ ApiName |
| Příchozí přenos dat | Příchozí přenos dat | Bajty | Celkem | Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | Typ ApiName |
| Výchozí přenos dat | Výchozí přenos dat | Bajty | Celkem | Objem výstupních dat (v bajtech). Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | Typ ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekund | Průměr | Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | Typ ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekund | Průměr | Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | Typ ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. | Typ ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace | Popis | Dimenze |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Kapacita objektu BLOB | Bajty | Celkem | Velikost úložiště využitá Blob service účtu úložiště v bajtech | BlobType |
| BlobCount | Počet objektů BLOB | Počet | Celkem | Počet objektů BLOB v Blob service účtu úložiště. | BlobType |
| ContainerCount | Počet kontejnerů objektů BLOB | Počet | Průměr | Počet kontejnerů v Blob service účtu úložiště. | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | ResponseType, typ ApiName |
| Příchozí přenos dat | Příchozí přenos dat | Bajty | Celkem | Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | Typ ApiName |
| Výchozí přenos dat | Výchozí přenos dat | Bajty | Celkem | Objem výstupních dat (v bajtech). Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | Typ ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekund | Průměr | Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | Typ ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekund | Průměr | Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | Typ ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. | Typ ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Kapacita tabulky | Bajty | Průměr | Velikost úložiště využitá Table service účtu úložiště v bajtech | Žádné dimenze |
| TableCount | Počet tabulek | Počet | Průměr | Počet tabulek v Table service účtu úložiště | Žádné dimenze |
| TableEntityCount | Počet entit tabulky | Počet | Průměr | Počet entit tabulky v Table service účtu úložiště | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | ResponseType, typ ApiName |
| Příchozí přenos dat | Příchozí přenos dat | Bajty | Celkem | Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | Typ ApiName |
| Výchozí přenos dat | Výchozí přenos dat | Bajty | Celkem | Objem výstupních dat (v bajtech). Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | Typ ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekund | Průměr | Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | Typ ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekund | Průměr | Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | Typ ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. | Typ ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

| Metric | Zobrazovaný název metriky | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Kapacita fronty | Bajty | Průměr | Velikost úložiště využitá Služba front účtu úložiště v bajtech | Žádné dimenze |
| QueueCount | Počet front | Počet | Průměr | Počet front v Služba front účtu úložiště. | Žádné dimenze |
| QueueMessageCount | Počet zpráv ve frontě | Počet | Průměr | Přibližný počet zpráv ve frontě v Služba front účtu úložiště. | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | ResponseType, typ ApiName |
| Příchozí přenos dat | Příchozí přenos dat | Bajty | Celkem | Množství příchozích dat v bajtech Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | Typ ApiName |
| Výchozí přenos dat | Výchozí přenos dat | Bajty | Celkem | Objem výstupních dat (v bajtech). Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | Typ ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekund | Průměr | Průměrná latence používaná Azure Storage ke zpracování úspěšné žádosti v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | Typ ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekund | Průměr | Průměrná koncová latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API (v milisekundách) Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | Typ ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. | Typ ApiName |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [službě Azure monitor v centru Azure Stack](azure-stack-metrics-azure-data.md).
