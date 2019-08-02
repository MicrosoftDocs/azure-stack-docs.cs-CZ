---
title: Azure Stack zpracování protokolů a dat | Microsoft Docs
description: Přečtěte si, jak Azure Stack shromažďuje informace.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: chengwei
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 495b75359cb8c859e532885a1c9fa284691bd90f
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68493808"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack zpracování dat protokolů a zákazníků 
*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*  

V případě, že je společnost Microsoft v souvislosti s Azure Stack procesorem nebo podprocesorem osobních údajů, společnost Microsoft provádí všem zákazníkům platným 25. května 2018 závazky v (a) v "zpracování osobních údajů"; GDPR podmínek pro účely ochrany dat v [online službách](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) a (b) Obecné nařízení o ochraně osobních údajů v článku věnovaném podmínkám používání služeb online [Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)v příloze 4. 

Jako Azure Stack se nachází v datových centrech zákazníků, Microsoft je řadič dat výhradně z dat, která se v Microsoftu sdílí prostřednictvím diagnostiky [](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep), [telemetrie](azure-stack-telemetry.md)a [fakturace](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Řízení přístupu k datům 
Zaměstnanci Microsoftu, kteří jsou přiřazeni k prozkoumání konkrétního případu podpory, budou mít k šifrovaným datům oprávnění jen pro čtení. Zaměstnanci Microsoftu mají také přístup k nástrojům, které slouží k odstranění dat v případě potřeby. Veškerý přístup k zákaznickým datům je auditován a zaznamenán.  

Řízení přístupu k datům:
- Data se uchovávají po dobu maximálně 90 dní po uzavření případu.
- Zákazník má vždycky možnost kdykoli odebrat data v období od 90 dne.
- Zaměstnanci Microsoftu mají přístup k datům na základě případu a jenom podle potřeby, které vám pomůžou problém s podporou vyřešit. 
- V případě, že je nutné, aby Microsoft sdílel zákaznická data s partnery OEM, je souhlas zákazníka povinný.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Které ovládací prvky požadavků subjektu dat (DSR) jsou zákazníky?
Jak bylo zmíněno dříve, společnost Microsoft podporuje odstranění dat na vyžádání na žádost zákazníka. Zákazníci si můžou vyžádat, aby náš pracovník podpory odstranil všechny protokoly pro daný případ kdykoli před tím, než se data trvale smaže.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Oznamuje společnost Microsoft zákazníkům, že se data odstraňují?
Pro akci automatizovaného odstranění dat (90 dní po uzavření případu) nebudeme aktivně kontaktovat zákazníky a upozorněte je na odstranění. 

V případě akce odstranění dat na vyžádání má pracovník podpory Microsoftu přístup k nástroji, kde může iniciovat odstranění dat na vyžádání, a po jeho dokončení můžou na telefonu poskytnout potvrzení.

## <a name="diagnostic-data"></a>Diagnostická data
V rámci procesu podpory můžou Azure Stack operátoři [Sdílet diagnostické protokoly](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) s Azure Stack podpory a technickými týmy pro usnadnění řešení potíží.

Společnost Microsoft poskytuje nástroj a skript pro zákazníky, kteří budou shromažďovat a nahrávat požadované soubory protokolu diagnostiky. Po shromáždění se soubory protokolu přenesou prostřednictvím šifrovaného připojení chráněného protokolem HTTPS společnosti Microsoft. Vzhledem k tomu, že protokol HTTPS zajišťuje šifrování po drátě, není pro přenos v cestě nutné žádné heslo. Po přijetí se protokoly zašifrují a uloží do doby, než se automaticky odstraní 90 dní po uzavření případu podpory.

## <a name="telemetry-data"></a>Data telemetrie
[Telemetrie Azure Stack](azure-stack-telemetry.md) automaticky odesílá systémová data společnosti Microsoft prostřednictvím prostředí připojeného uživatele. Azure Stack operátory mají ovládací prvky pro přizpůsobení funkcí telemetrie a nastavení ochrany osobních údajů.

Společnost Microsoft nemá v úmyslu shromažďovat citlivá data, jako jsou třeba čísla kreditních karet, uživatelská jména a hesla, e-mailové adresy nebo podobné citlivé informace. Pokud zjistíme, že se nechtěně přijaly citlivé informace, odstraníme je. 

## <a name="billing-data"></a>Fakturační údaje
[Azure Stack faktura](azure-stack-usage-reporting.md) využívá kanál pro fakturaci a využití globálního Azure a je proto v souladu s pokyny pro dodržování předpisů Microsoftu.

Operátoři Azure Stack můžou nakonfigurovat Azure Stack k přeposílání informací o využití do Azure pro účely fakturace. Tato možnost je vyžadována pro zákazníky s více Azure Stack uzly, kteří používají model fakturace s průběžnými platbami. Vytváření sestav o využití se řídí nezávisle na telemetrie a není vyžadováno pro zákazníky s více uzly, kteří volí model kapacity nebo pro Azure Stack Development Kit uživatele. V těchto scénářích lze vytváření sestav využití vypnout pomocí [registračního skriptu](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Další kroky 
[Další informace o zabezpečení služby Azure Stack](azure-stack-security-foundations.md) 
