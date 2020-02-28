---
title: Přehled shromažďování protokolů diagnostiky centra Azure Stack
description: Vysvětluje shromažďování diagnostických protokolů v centru Azure Stack Help a podpora, včetně kolekce na vyžádání a automatického shromažďování protokolů.
author: justinha
ms.topic: article
ms.date: 11/07/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 05d54d3db2429faa410cc67a46fba234d285a1af
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700047"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Přehled shromažďování protokolů diagnostiky centra Azure Stack 

Centrum Azure Stack je rozsáhlá kolekce komponent společně propojená a vzájemně spolupracuje. Všechny tyto komponenty generují vlastní jedinečné protokoly. Díky tomu mohou diagnostikovat problémy náročný úkol, zejména chyby přicházející z více interakcí Azure Stack komponent centra. Aby bylo možné vyřešit tuto výzvu, navrhli jsme prostředí pro shromažďování diagnostických protokolů. 

Před 1907 se v diagnostickém prostředí, které je součástí [služby test-AzureStack](azure-stack-diagnostic-test.md) , ověřil stav systému a pomocí [privilegovaného koncového bodu (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) shromažďuje protokoly pro řešení potíží. 

Od verze 1907 se na stránce **pomoc a podpora** přidá jednodušší prostředí pomocí **shromažďování protokolů diagnostiky**. 
**Shromažďování protokolů diagnostiky** je součástí průběžné investice za účelem zlepšení zkušeností operátorů centra Azure Stack s procesem řešení potíží. Pomocí těchto vylepšení můžou operátoři rychle shromažďovat a sdílet diagnostické protokoly se službou zákaznické podpory Microsoftu (CSS). Protokoly se můžou ukládat do kontejneru objektů BLOB v Azure, kde můžete podle potřeby přizpůsobit přístup.    
   
**Shromažďování protokolů diagnostiky** funguje dvěma různými způsoby:

- **Automatická kolekce**: Pokud je povoleno (doporučeno), kolekce protokolů se automaticky aktivuje konkrétními upozorněními na stav a uloženými v účtu úložiště Azure.
- **Shromažďovat protokoly nyní**: Jedná se o možnost na vyžádání, kde můžete zvolit shromažďování protokolů z 1-4 hodinových oken za posledních sedm dní.

![Snímek obrazovky s možnostmi shromažďování diagnostických protokolů](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

**Shromažďování protokolů diagnostiky** má jednoduché uživatelské rozhraní a nevyžaduje prostředí PowerShell. Protokoly se shromažďují spolehlivě i v případě, že jsou služby infrastruktury mimo provoz.
Pokud vaše zásada umožňuje sdílení diagnostických protokolů pomocí šablony stylů CSS, je **shromažďování protokolů diagnostiky** doporučovanou metodou shromažďování dat od verze 1907. [PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs) byste měli použít jenom ke shromažďování protokolů, pokud není k dispozici **shromažďování diagnostických protokolů** v nápovědě a podpoře.

## <a name="automatic-diagnostic-log-collection"></a>Shromažďování automatických protokolů diagnostiky 

Když je aktivní [konkrétní upozornění na stav](azure-stack-configure-automatic-diagnostic-log-collection.md#automatic-diagnostic-log-collection-alerts) , automaticky se spustí shromažďování protokolů diagnostiky a proaktivně nahraje diagnostické protokoly z centra Azure Stack do objektu BLOB úložiště v Azure, což výrazně zkracuje dobu potřebnou ke sdílení diagnostických protokolů s CSS. Diagnostické protokoly jsou shromažďovány pouze v případě, že je vyvolána výstraha.  

Další informace o automatickém shromažďování protokolů najdete v tématu [Konfigurace automatického shromáždění protokolů diagnostiky centra Azure Stack](azure-stack-configure-automatic-diagnostic-log-collection.md).

## <a name="on-demand-diagnostic-log-collection"></a>Shromažďování protokolů diagnostiky na vyžádání

V případě shromažďování na vyžádání se diagnostické protokoly odesílají z centra Azure Stack do objektu BLOB úložiště v Azure, když operátor centra Azure Stack ručně spustí kolekci.
Šablona stylů CSS poskytne adresu URL sdíleného přístupového podpisu (SAS) k objektu BLOB úložiště ve vlastnictví šablon stylů CSS. Operátor centra Azure Stack může hned kliknout na **shromáždit protokoly** a zadat adresu URL SAS. Diagnostické protokoly se pak nahrají přímo do objektu BLOB šablon stylů CSS, aniž by bylo nutné dodržet zprostředkující sdílenou složku. 

Další informace o shromažďování protokolů na vyžádání najdete v tématu o tom, jak [teď shromažďovat diagnostické protokoly centra Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md).

## <a name="bandwidth-considerations"></a>Požadavky na šířku pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, jestli běží na vyžádání nebo automaticky. Průměrná velikost pro automatické shromažďování protokolů je okolo 2 GB, zatímco velikost kolekce protokolů na vyžádání závisí na počtu shromažďovaných hodin. 

V následující tabulce jsou uvedeny požadavky pro prostředí s omezenými nebo měřenými připojeními k Azure.

| Síťové připojení | Dopad |
|--------------------|--------|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. | 
| Sdílené připojení | Nahrávání může mít vliv i na jiné aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. |

Další informace najdete v tématu [osvědčené postupy pro automatické shromažďování protokolů Azure Stack hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md).

## <a name="see-also"></a>Viz také

[Azure Stack zpracování dat protokolů a zákazníků centra](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Osvědčené postupy pro automatické shromažďování protokolů Azure Stack hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
