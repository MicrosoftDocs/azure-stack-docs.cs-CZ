---
title: Azure Stack zpracování dat protokolů a zákazníků | Microsoft Docs
description: Přečtěte si, jak Azure Stack shromažďuje údaje o zákaznících a informace.
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
ms.openlocfilehash: 3e46007c07856df9ecc6b4edca4c595525b3b8ba
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342770"
---
# <a name="azure-stack-log-and-customer-data-handling"></a>Azure Stack zpracování dat protokolů a zákazníků 
*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*  

V takovém případě je společnost Microsoft v souvislosti s Azure Stack procesorem nebo podprocesorem osobních údajů, a to od 25. května 2018 následující závazky:

- "Zpracování osobních údajů; GDPR "pravidla ochrany dat" v tématu [online služby](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).
- Evropské unie Obecné nařízení o ochraně osobních údajů podmínkami v příloze 4 [podmínek pro online služby](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Jako Azure Stack se nachází v datových centrech zákazníků, Microsoft je řadič dat výhradně z dat, která se v Microsoftu sdílí prostřednictvím diagnostiky [](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep), [telemetrie](azure-stack-telemetry.md)a [fakturace](azure-stack-usage-reporting.md).  

## <a name="data-access-controls"></a>Řízení přístupu k datům 
Zaměstnanci Microsoftu, kteří jsou přiřazeni k prozkoumání konkrétního případu podpory, budou mít k šifrovaným datům oprávnění jen pro čtení. Zaměstnanci Microsoftu mají také přístup k nástrojům, které slouží k odstranění dat v případě potřeby. Veškerý přístup k zákaznickým datům je auditován a zaznamenán.  

Řízení přístupu k datům:
- Data se uchovávají po dobu maximálně 90 dní po uzavření případu.
- Zákazník má vždycky možnost kdykoli odebrat data v období od 90 dne.
- Zaměstnanci Microsoftu mají přístup k datům na základě případu a jenom podle potřeby, které vám pomůžou problém s podporou vyřešit.
- V případě, že je nutné, aby Microsoft sdílel zákaznická data s partnery OEM, je souhlas zákazníka povinný.  

### <a name="what-data-subject-requests-dsr-controls-do-customers-have"></a>Které ovládací prvky požadavků subjektu dat (DSR) jsou zákazníky?
Microsoft podporuje odstranění dat na vyžádání na žádost zákazníka. Zákazníci si můžou vyžádat, aby jeden z našich techniků podpory v každém okamžiku odstranil všechny protokoly pro daný případ, než se data trvale vymažou.  

### <a name="does-microsoft-notify-customers-when-the-data-is-deleted"></a>Oznamuje společnost Microsoft zákazníkům, že se data odstraňují?
Pro akci automatizovaného odstranění dat (90 dní po uzavření případu) nebudeme aktivně kontaktovat zákazníky a upozorněte je na odstranění.

Pro akci odstranění dat na vyžádání mají pracovníci podpory Microsoftu přístup k nástroji, který jim umožňuje odstranit data na vyžádání. Když se zákazník dokončí, může na telefonu poskytnout potvrzení.

## <a name="diagnostic-data"></a>Diagnostická data
V rámci procesu podpory můžou Azure Stack operátoři [Sdílet diagnostické protokoly](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep) s Azure Stack podpory a technickými týmy pro pomoc s řešením problémů.

Společnost Microsoft poskytuje nástroj a skript pro zákazníky, kteří budou shromažďovat a nahrávat požadované soubory protokolu diagnostiky. Po shromáždění se soubory protokolu přenesou prostřednictvím šifrovaného připojení chráněného protokolem HTTPS společnosti Microsoft. Vzhledem k tomu, že protokol HTTPS zajišťuje šifrování po drátě, není při přenosu vyžadováno žádné heslo. Po přijetí se protokoly zašifrují a uloží do doby, než se automaticky odstraní 90 dnů po uzavření případu podpory.

## <a name="telemetry-data"></a>Data telemetrie
[Telemetrie Azure Stack](azure-stack-telemetry.md) automaticky odesílá systémová data společnosti Microsoft prostřednictvím prostředí připojeného uživatele. Azure Stack operátory mají ovládací prvky pro přizpůsobení funkcí telemetrie a nastavení ochrany osobních údajů.

Společnost Microsoft nemá v úmyslu shromažďovat citlivá data, jako jsou třeba čísla kreditních karet, uživatelská jména a hesla, e-mailové adresy atd. Pokud zjistíme, že se nechtěně přijaly citlivé informace, odstraníme je.

## <a name="billing-data"></a>Fakturační údaje
[Azure Stack faktura](azure-stack-usage-reporting.md) využívá kanál pro fakturaci a využití globálního Azure a je proto v souladu s pokyny pro dodržování předpisů Microsoftu.

Operátoři Azure Stack můžou nakonfigurovat Azure Stack k přeposílání informací o využití do Azure pro účely fakturace. Tato konfigurace je nutná pro zákazníky s integrovanými systémy Azure Stack, kteří si zvolí model fakturace s průběžnými platbami podle aktuálního využití. Vytváření sestav o využití se řídí nezávisle na telemetrie a nevyžaduje se pro zákazníky s integrovanými systémy, kteří volí model kapacity nebo pro Azure Stack Development Kit uživatele. V těchto scénářích lze vytváření sestav využití vypnout pomocí [registračního skriptu](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Další kroky 
[Další informace o zabezpečení služby Azure Stack](azure-stack-security-foundations.md) 
