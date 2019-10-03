---
title: Přehled shromažďování protokolů diagnostiky Azure Stack | Microsoft Docs
description: Vysvětluje shromažďování diagnostických protokolů v Azure Stack nápovědě a podpoře, včetně kolekce na vyžádání a automatického shromažďování protokolů.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: cd65f51867a4626e8f9b288c5113909bfa32b1c2
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829195"
---
# <a name="overview-of-azure-stack-diagnostic-log-collection"></a>Přehled shromažďování diagnostických protokolů Azure Stack 

*Platí pro: Azure Stack integrovaných systémů*

Azure Stack je rozsáhlá kolekce součástí pracujících společně a vzájemně spolupracuje. Všechny tyto komponenty generují vlastní jedinečné protokoly. Díky tomu mohou diagnostikovat problémy náročný úkol, zejména chyby pocházející z více interakcí Azure Stack komponent. Aby bylo možné vyřešit tuto výzvu, navrhli jsme prostředí pro shromažďování diagnostických protokolů. 

Před 1907 se v diagnostickém prostředí, které je součástí [služby test-AzureStack](azure-stack-diagnostic-test.md) , ověřil stav systému a pomocí [privilegovaného koncového bodu (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs) shromažďuje protokoly pro řešení potíží. 

Od verze 1907 se na stránce **pomoc a podpora** přidá jednodušší prostředí pomocí **shromažďování protokolů diagnostiky**. 
**Shromažďování protokolů diagnostiky** je součástí trvalé investice za účelem zlepšení zkušeností operátorů Azure Stack pomocí procesu řešení potíží. Pomocí těchto vylepšení můžou operátoři rychle shromažďovat a sdílet diagnostické protokoly se službou zákaznické podpory Microsoftu (CSS). Protokoly se můžou ukládat do kontejneru objektů BLOB v Azure, kde můžete podle potřeby přizpůsobit přístup.    
   
**Shromažďování protokolů diagnostiky** funguje dvěma různými způsoby:

- **Automatická kolekce**: Pokud je povoleno (doporučeno), kolekce protokolů se automaticky aktivuje konkrétními upozorněními na stav a uloženými v účtu úložiště Azure.
- **Shromažďovat protokoly hned teď**: Jedná se o možnost na vyžádání, kde se můžete rozhodnout pro shromažďování protokolů z posledních sedmi dnů od 1-4 hodinových oken.

![Snímek obrazovky s možnostmi shromažďování diagnostických protokolů](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

**Shromažďování protokolů diagnostiky** má jednoduché uživatelské rozhraní a nevyžaduje prostředí PowerShell. Protokoly se shromažďují spolehlivě i v případě, že jsou služby infrastruktury mimo provoz.
Pokud vaše zásada umožňuje sdílení diagnostických protokolů pomocí šablony stylů CSS, je **shromažďování protokolů diagnostiky** doporučovanou metodou shromažďování dat od verze 1907. [PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs) byste měli použít jenom ke shromažďování protokolů, pokud není k dispozici **shromažďování diagnostických protokolů** v nápovědě a podpoře.

## <a name="automatic-diagnostic-log-collection"></a>Shromažďování automatických protokolů diagnostiky 

Když je aktivní [specifická výstraha o stavu](azure-stack-configure-automatic-diagnostic-log-collection.md#automatic-diagnostic-log-collection-alerts) , spustí automatické shromažďování protokolů diagnostiky a proaktivně nahraje diagnostické protokoly z Azure Stack do objektu BLOB úložiště v Azure. tím se podstatně zkrátí doba nutná ke sdílení diagnostických protokolů pomocí šablon stylů CSS. Diagnostické protokoly jsou shromažďovány pouze v případě, že je vyvolána výstraha.  

Další informace o automatickém shromažďování protokolů najdete v tématu [Konfigurace automatického shromažďování protokolů Azure Stack pro diagnostiku](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="on-demand-diagnostic-log-collection"></a>Shromažďování protokolů diagnostiky na vyžádání

V případě shromažďování na vyžádání se diagnostické protokoly odesílají z Azure Stack do objektu BLOB úložiště v Azure, když Azure Stack operátor ručně spustí kolekci.
Šablona stylů CSS poskytne adresu URL sdíleného přístupového podpisu (SAS) k objektu BLOB úložiště ve vlastnictví šablon stylů CSS. Operátor Azure Stack může nyní kliknout na **shromáždit protokoly** a zadat adresu URL SAS. Diagnostické protokoly se pak nahrají přímo do objektu BLOB šablon stylů CSS, aniž by bylo nutné dodržet zprostředkující sdílenou složku. 

Další informace o shromažďování protokolů na vyžádání najdete v tématu o tom, jak [teď shromažďovat protokoly diagnostiky Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md).

## <a name="bandwidth-considerations"></a>Požadavky na šířku pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, jestli běží na vyžádání nebo automaticky. Průměrná velikost pro automatické shromažďování protokolů je okolo 2 GB, zatímco velikost kolekce protokolů na vyžádání závisí na počtu shromažďovaných hodin. 

V následující tabulce jsou uvedeny požadavky pro prostředí s omezenými nebo měřenými připojeními k Azure.

| Síťové připojení | Dopad |
|--------------------|--------|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. | 
| Sdílené připojení | Nahrávání může mít vliv i na jiné aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. |

Další informace najdete v tématu [osvědčené postupy pro automatické shromažďování protokolů Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Viz také:

[Azure Stack zpracování dat protokolů a zákazníků](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Osvědčené postupy pro automatické shromažďování protokolů Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
