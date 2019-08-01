---
title: Podporované metriky pro Azure Monitor v Azure Stack | Microsoft Docs
description: Další informace o podporované metriky pro monitorování Azure ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.lastreviewed: 12/06/2018
ms.openlocfilehash: a66b6fce646a591efac17a5b6e4ed804dba211e7
ms.sourcegitcommit: bf4d265a3522cbfdd9dd295a0f4ad0daf2ed5eca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68692147"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack"></a>Podporované metriky pro Azure Monitor v Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Metriky ze služby Azure monitor v Azure Stack jsou načteny stejným způsobem jako v globálním Azure. Můžete vytvářet míry na portálu, je získat z rozhraní REST API nebo dotazovat pomocí Powershellu nebo rozhraní příkazového řádku.

V následujících tabulkách jsou uvedeny metriky dostupné s kanálem metriky Azure Monitor v Azure Stack. K dotazování a přístupu k těmto metrikám použijte verzi rozhraní API verze **2018-01-01** API. Další informace o profilech rozhraní API a služby Azure Stack najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| Procento CPU | Procento CPU | Procento | Average | Procento přidělených výpočetních jednotek, které jsou aktuálně používány virtuálními počítači. | Žádné dimenze |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts.

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | Použitá kapacita | B | Average | Kapacita využitého účtu | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | B | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | GeoType ApiName |
| Výchozí přenos | Výchozí přenos | B | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Kapacita služby Blob | B | Celkem | Velikost úložiště využitá Blob service účtu úložiště v bajtech | BlobType |
| BlobCount | Počet objektů blob | Počet | Celkem | Počet objektů BLOB v Blob service účtu úložiště. | BlobType |
| ContainerCount | Počet kontejnerů služby Blob | Počet | Average | Počet kontejnerů v Blob service účtu úložiště. | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | B | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | GeoType ApiName |
| Výchozí přenos | Výchozí přenos | B | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | Kapacita služby Table | B | Average | Velikost úložiště využitá Table service účtu úložiště v bajtech | Žádné dimenze |
| TableCount | Počet tabulek | Počet | Average | Počet tabulek v Table service účtu úložiště | Žádné dimenze |
| TableEntityCount | Počet entit tabulek | Počet | Average | Počet entit tabulky v Table service účtu úložiště | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | B | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | GeoType ApiName |
| Výchozí přenos | Výchozí přenos | B | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. | GeoType ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| Metrika | Metriky zobrazovaný název | Jednotka | Typ agregace | Popis | Dimenze |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | Kapacita služby Queue | B | Average | Velikost úložiště využitá Služba front účtu úložiště v bajtech | Žádné dimenze |
| QueueCount | Počet front | Počet | Average | Počet front v Služba front účtu úložiště. | Žádné dimenze |
| QueueMessageCount | Počet zpráv fronty | Počet | Average | Přibližný počet zpráv ve frontě v Služba front účtu úložiště. | Žádné dimenze |
| Transakce | Transakce | Počet | Celkem | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné a neúspěšné požadavky a také požadavky, které vygenerovaly chyby. Použijte dimenzi ResponseType pro počet různých typů odpovědí. | Hodnota ResponseType, GeoType, ApiName |
| Příchozí přenos dat | Příchozí přenos dat | B | Celkem | Množství příchozích dat v bajtech. Toto číslo zahrnuje příchozí přenos dat z externího klienta do Azure Storage a také příchozí přenosy v rámci Azure. | GeoType ApiName |
| Výchozí přenos | Výchozí přenos | B | Celkem | Objem odchozích přenosů dat v bajtech. Toto číslo zahrnuje odchozí přenos dat z externího klienta do Azure Storage a také pro výstup v rámci Azure. V důsledku toho toto číslo neodráží fakturovatelný výstup. | GeoType ApiName |
| SuccessServerLatency | Latence serveru při úspěchu | Milisekundy | Průměr | Průměrná latence služby Azure Storage ke zpracování úspěšného požadavku v milisekundách. Tato hodnota nezahrnuje latenci sítě určenou v hodnotu averagee2elatency. | GeoType ApiName |
| SuccessE2ELatency | Celková latence při úspěchu | Milisekundy | Průměr | Průměrná latence začátku do konce úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API v milisekundách. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. | GeoType ApiName |
| Dostupnost | Dostupnost | Procento | Průměr | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Vypočítejte dostupnost tím, že převezmete hodnotu TotalBillableRequests a rozdělíte ji počtem příslušných požadavků, včetně požadavků, které vytvořily neočekávané chyby. Všechny neočekávané chyby za následek sníženou dostupnost pro službu úložiště nebo zadanou operaci rozhraní API. | GeoType ApiName |

## <a name="next-steps"></a>Další postup

Další informace o [monitorování Azure ve službě Azure Stack](azure-stack-metrics-azure-data.md).
