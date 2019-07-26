---
title: Osvědčené postupy pro automatické shromažďování protokolů Azure Stack | Microsoft Docs
description: Osvědčené postupy pro automatické shromažďování protokolů v Azure Stack nápovědě a podpoře
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
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 566119a6c81e19962d648172ac5274ca6ab51b66
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497142"
---
# <a name="best-practices-for-automatic-azure-stack-log-collection"></a>Osvědčené postupy pro automatické shromažďování protokolů Azure Stack 

*Platí pro: Azure Stack integrovaných systémů*


Toto téma popisuje osvědčené postupy pro správu automatického shromažďování protokolů diagnostiky pro Azure Stack. 

## <a name="collecting-logs-from-multiple-azure-stack-systems"></a>Shromažďování protokolů z více Azure Stack systémů

Nastavte jeden kontejner objektů BLOB pro každou Azure Stack jednotku škálování, ze které chcete shromažďovat protokoly. Další informace o konfiguraci kontejneru objektů BLOB najdete v tématu [Konfigurace automatického shromažďování protokolů Azure Stack diagnostiky](azure-stack-configure-automatic-diagnostic-log-collection.md). Osvědčeným postupem je ukládat jenom diagnostické protokoly ze stejné Azure Stack jednotky škálování v jednom kontejneru objektů BLOB. 

## <a name="retention-policy"></a>Zásady uchovávání informací

Vytvořte [pravidlo správy životního cyklu](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) Azure Blob Storage pro správu zásad uchovávání protokolů. Doporučujeme uchovávat protokoly diagnostiky po dobu 30 dnů. Pokud chcete vytvořit pravidlo správy životního cyklu ve službě Azure Storage, přihlaste se k Azure Portal, klikněte na **účty úložiště**, klikněte na kontejner objektů BLOB a v části **BLOB Service**klikněte na **Správa životního cyklu**.

![Snímek obrazovky znázorňující správu životního cyklu v Azure Portal](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>Vypršení platnosti tokenu SAS

Nastavte vypršení platnosti adresy URL SAS na dva roky. Pokud jste někdy obnovili klíče účtu úložiště, nezapomeňte znovu vygenerovat adresu URL SAS. Token SAS byste měli spravovat podle osvědčených postupů. Další informace najdete v tématu [osvědčené postupy při použití SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).


## <a name="bandwidth-consumption"></a>Spotřeba šířky pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, jestli je shromažďování protokolů na vyžádání nebo automatické. 

Pro shromažďování protokolů na vyžádání závisí velikost kolekce logs na tom, kolik hodin se shromažďuje. V posledních sedmi dnech můžete zvolit libovolné posunuté okno v 1-4 hodinách. 

Pokud je povoleno automatické shromažďování protokolů diagnostiky, služba sleduje kritické výstrahy. Po vygenerování kritické výstrahy a trvá zhruba 30 minut služba shromažďuje a nahrává vhodné protokoly. Velikost kolekce protokolů je v průměru přibližně 2 GB. V případě chyby opravy a aktualizace se automatické shromažďování protokolů spustí pouze v případě, že je vyvolána kritická výstraha a trvá zhruba 30 minut. Doporučujeme vám postupovat podle [pokynů k monitorování opravy a aktualizace](azure-stack-updates.md).
Monitorování výstrah, shromažďování protokolů a nahrávání jsou pro uživatele transparentní. 



V systému v pořádku nebudou protokoly shromažďovány vůbec. V systému, který není v pořádku, může shromažďování protokolů v jednom dni běžet dvakrát nebo třikrát, ale obvykle jenom jednou. Ve většině případů to může v nejhorším scénáři trvat až desetkrát za den.  

Následující tabulka může pomáhat prostředím s omezenými nebo měřenými připojeními k Azure, které berou v úvahu dopad povolování automatického shromažďování protokolů.

| Síťové připojení | Dopad |
|--------------------|--------|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. | 
| Sdílené připojení | Nahrávání může mít vliv i na jiné aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. |


## <a name="managing-costs"></a>Správa nákladů

[Poplatky za službu Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) závisí na množství dat uložených každý měsíc a dalších faktorech, jako je třeba redundance dat. Pokud nemáte existující účet úložiště, můžete se přihlásit k Azure Portal, kliknout na **účty úložiště**a podle postupu [vytvořit adresu URL SAS kontejneru objektů BLOB v Azure](azure-stack-configure-automatic-diagnostic-log-collection.md).

Osvědčeným postupem je vytvořit [zásadu správy životního cyklu](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) Azure Blob Storage, která minimalizuje průběžné náklady na úložiště. Další informace o tom, jak nastavit účet úložiště, najdete v tématu [Konfigurace automatického shromažďování protokolů Azure Stack diagnostiky](azure-stack-configure-automatic-diagnostic-log-collection.md).


