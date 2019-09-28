---
title: Využití infrastruktury vytváření sestav pro poskytovatele cloudových řešení pro Azure Stack | Microsoft Docs
description: Seznamte se s infrastrukturou vytváření sestav využití, která se používá ke sledování využití pro klienty používané poskytovatelem Cloud Solution Provider (CSP).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 1a3c59ab7650c9cd2337e8256556f8a449feacec
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342806"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>Využití infrastruktury vytváření sestav pro poskytovatele cloudových řešení

Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití při jejich výskytu a předá ji do Azure. V Azure Azure Commerce zpracovává data o využití a účtuje náklady na příslušné předplatné Azure. Tento proces funguje stejným způsobem jako sledování využití v globálním cloudu Azure.

Některé koncepty jsou konzistentní mezi globálním Azure a Azure Stack. Azure Stack má místní odběry, které splňují podobnou roli pro předplatné Azure. Místní odběry jsou platné pouze místně. Místní předplatná jsou namapována na předplatná Azure, když se využití předává do Azure.

Azure Stack má měřiče využití v místním prostředí. Místní použití je namapované na měřiče používané v Azure Commerce. ID měřičů se ale liší. K dispozici je více měřičů, než je ten, který používá společnost Microsoft pro účely fakturace.

Mezi tím, jak se účtují služby Azure Stack a Azure, dochází k nějakým rozdílům. Například v Azure Stack platí, že poplatky za virtuální počítače jsou založené jenom na Vcore/hodinách a stejnou sazbu pro všechny řady virtuálních počítačů, na rozdíl od Azure. Důvodem je to, že v globálním Azure různé ceny odrážejí jiný hardware. V Azure Stack zákazník poskytuje hardware, proto neexistuje žádný důvod, proč se za různé třídy virtuálních počítačů účtují různé sazby.

Můžete získat informace o Azure Stack měřičů používaných v obchodu a jejich cenách v partnerském centru. Tento proces je stejný jako u služeb Azure:

1. V partnerském centru přejděte do **nabídky řídicí panel**, vyberte **prodávat**a pak vyberte **ceny a nabídky**.
2. V části **služby založené na používání**vyberte **aktuální**.
3. Otevřete tabulku **Azure v globálním seznamu ceníků CSP** .
4. Filter on **region = Azure Stack**.

## <a name="terms-used-for-billing-and-usage"></a>Výrazy používané pro fakturaci a využití

Následující pojmy a koncepty se používají pro účely využití a fakturace v Azure Stack:

| Termín | Definice |
| --- | --- |
| Přímý poskytovatel CSP | Partner s přímým cloudovým řešením (CSP) obdrží fakturu přímo od Microsoftu k Azure a využití Azure Stack a zákazníkům fakturuje přímo. |
| Nepřímý CSP | Nepřímí prodejci pracují s nepřímým poskytovatelem (také označovaným jako distributor). Prodejci přikupují koncové zákazníky; nepřímý poskytovatel obsahuje fakturační vztah s Microsoftem, spravuje fakturaci zákazníků a poskytuje další služby, jako je podpora produktů. |
| Koncový zákazník | Koncoví zákazníci jsou podniky a státní úřady, které vlastní aplikace a další úlohy, které běží na Azure Stack. |

## <a name="next-steps"></a>Další kroky

- Další informace o programu CSP najdete v tématu [cloudová řešení](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Další informace o tom, jak načíst informace o využití prostředků z Azure Stack, najdete [v tématu využití a fakturace v Azure Stack](azure-stack-billing-and-chargeback.md).
