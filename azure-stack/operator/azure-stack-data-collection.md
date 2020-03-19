---
title: Azure Stack zpracování dat protokolů a zákazníků centra
description: Přečtěte si, jak Azure Stack centrum shromažďuje zákaznická data a informace.
author: JustinHall
ms.topic: article
ms.date: 02/24/2020
ms.author: justinha
ms.reviewer: chengwei
ms.lastreviewed: 02/24/2020
ms.openlocfilehash: fc7d819bce237b98c359bc7c4bc43bc478d84952
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512571"
---
# <a name="azure-stack-hub-log-and-customer-data-handling"></a>Azure Stack zpracování dat protokolů a zákazníků centra 

V případě, že je Microsoft v souvislosti s centrem Azure Stack procesorem nebo podprocesorem osobních údajů, společnost Microsoft provádí všem zákazníkům platná 25. května 2018 následující závazky:

- "Zpracování osobních údajů; GDPR "pravidla ochrany dat" v tématu [online služby](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).
- Evropské unie Obecné nařízení o ochraně osobních údajů podmínkami v příloze 4 [podmínek pro online služby](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Jako centrum Azure Stack se nachází v datacentrech zákazníků, Microsoft je řadič dat výhradně z dat, která se v Microsoftu sdílí prostřednictvím [diagnostiky](azure-stack-diagnostic-log-collection-overview-tzl.md), [telemetrie](azure-stack-telemetry.md)a [fakturace](azure-stack-usage-reporting.md).  

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
V rámci procesu podpory mohou operátoři centra Azure Stack [Sdílet diagnostické protokoly](azure-stack-diagnostic-log-collection-overview-tzl.md) s pracovníky podpory centra Azure Stack a technickými týmy pro pomoc s řešením problémů.

Společnost Microsoft poskytuje nástroj a skript pro zákazníky, kteří budou shromažďovat a nahrávat požadované soubory protokolu diagnostiky. Po shromáždění se soubory protokolu přenesou prostřednictvím šifrovaného připojení chráněného protokolem HTTPS společnosti Microsoft. Vzhledem k tomu, že protokol HTTPS zajišťuje šifrování po drátě, není při přenosu vyžadováno žádné heslo. Po přijetí se protokoly zašifrují a uloží do doby, než se automaticky odstraní 90 dnů po uzavření případu podpory.

## <a name="telemetry-data"></a>Data telemetrie
[Telemetrii centra Azure Stack](azure-stack-telemetry.md) automaticky odesílá systémová data společnosti Microsoft prostřednictvím prostředí připojeného uživatele. Operátoři centra Azure Stack mají ovládací prvky pro přizpůsobení funkcí telemetrie a nastavení ochrany osobních údajů.

Společnost Microsoft nemá v úmyslu shromažďovat citlivá data, jako jsou třeba čísla kreditních karet, uživatelská jména a hesla, e-mailové adresy atd. Pokud zjistíme, že se nechtěně přijaly citlivé informace, odstraníme je.

## <a name="billing-data"></a>Fakturační údaje
Při [fakturaci centra Azure Stack](azure-stack-usage-reporting.md) využíváme kanál pro fakturaci a využití globálního Azure a je proto v souladu s pokyny pro dodržování předpisů Microsoftu.

Operátoři centra Azure Stack můžou nakonfigurovat centrum Azure Stack a předají informace o využití do Azure pro účely fakturace. Tato konfigurace je nutná pro zákazníky s integrovanými systémy Azure Stack hub, kteří si vyberou model fakturace s průběžnými platbami. Vytváření sestav o využití se řídí nezávisle na telemetrie a nevyžaduje se pro zákazníky s integrovanými systémy, kteří volí model kapacity nebo pro Azure Stack Development Kit uživatele. V těchto scénářích lze vytváření sestav využití vypnout pomocí [registračního skriptu](azure-stack-usage-reporting.md).


## <a name="next-steps"></a>Další kroky 
[Další informace o zabezpečení centra Azure Stack](azure-stack-security-foundations.md) 
