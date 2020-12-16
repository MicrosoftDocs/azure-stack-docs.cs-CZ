---
title: Využití infrastruktury vytváření sestav pro poskytovatele Cloud Solution Provider – centrum Azure Stack
description: Seznamte se s infrastrukturou vytváření sestav využití, která se používá ke sledování využití pro klienty používané poskytovatelem Cloud Solution Provider (CSP).
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 820cd9db933ee017f4b7a1890c277870d1bd23bd
ms.sourcegitcommit: a53ea4a28e715c80a99fa89e9d364bc4556558de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97576985"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>Využití infrastruktury vytváření sestav pro poskytovatele cloudových řešení

Centrum Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití při jejich výskytu a předá ji do Azure. V Azure Azure Commerce zpracovává data o [využití a účtuje náklady](azure-stack-billing-and-chargeback.md) na příslušné předplatné Azure. Tento proces funguje stejným způsobem jako sledování využití v globálním cloudu Azure.

Některé koncepce jsou v rámci globálního centra Azure a Azure Stack konzistentní. Centrum Azure Stack má místní předplatná, která splňují podobné role k předplatnému Azure. Místní odběry jsou platné pouze místně. Místní předplatná jsou namapována na předplatná Azure, když se využití předává do Azure.

Azure Stack centrum má měřiče využití v místním prostředí. Místní použití je namapované na měřiče používané v Azure Commerce. ID měřičů se ale liší. K dispozici je více měřičů, než je ten, který používá společnost Microsoft pro účely fakturace.

Existují některé rozdíly mezi tím, jak se účtují služby Azure Stack hub a Azure. Například v centru Azure Stack se poplatky za virtuální počítače zakládají jenom na Vcore/hod a stejnou sazbou pro všechny série virtuálních počítačů, na rozdíl od Azure. Důvodem je to, že v globálním Azure různé ceny odrážejí jiný hardware. V centru Azure Stack zákazník poskytuje hardware, takže neexistuje žádný důvod, proč se za různé třídy virtuálních počítačů účtují různé sazby.

Můžete získat informace o měřičích centra Azure Stack používaných při obchodování a jejich cenách v partnerském centru. Tento proces je stejný jako u služeb Azure:

1. V partnerském centru přejděte do nabídky **řídicí panel** , vyberte **prodávat** a pak vyberte **ceny a nabídky**.
2. V části **služby založené na používání** vyberte **aktuální**.
3. Otevřete tabulku **Azure v globálním seznamu ceníků CSP** .
4. Filtr v **oblasti = Azure Stack hub**.

## <a name="terms-used-for-billing-and-usage"></a>Výrazy používané pro fakturaci a využití

Pro účely využití a fakturace v centru Azure Stack se používají následující pojmy a koncepty:

| Označení | Definice |
| --- | --- |
| Přímý poskytovatel CSP | Přímý poskytovatel CSP obdrží fakturu přímo od Microsoftu k Azure a využití centra Azure Stack a účtuje zákazníky přímo. |
| Nepřímý poskytovatel CSP | Nepřímí prodejci pracují s nepřímým poskytovatelem (také označovaným jako distributor). Prodejci přikupují koncové zákazníky; nepřímý poskytovatel obsahuje fakturační vztah s Microsoftem, spravuje fakturaci zákazníků a poskytuje další služby, jako je podpora produktů. |
| Koncový zákazník | Koncoví zákazníci jsou podniky a státní úřady, které vlastní aplikace a další úlohy spuštěné v Azure Stackovém centru. |

## <a name="next-steps"></a>Další kroky

- Další informace o programu CSP najdete v tématu [cloudová řešení](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Další informace o tom, jak načíst informace o využití prostředků z centra Azure Stack, najdete [v tématu využití a fakturace v centru Azure Stack](azure-stack-billing-and-chargeback.md).
