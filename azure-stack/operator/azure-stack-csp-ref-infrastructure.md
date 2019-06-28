---
title: Využití sestav infrastruktury pro poskytovatele cloudových služeb pro Azure Stack | Dokumentace Microsoftu
description: Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití u klientů, které jsou obsluhovány pomocí Cloud Service Provider (CSP) nastane a předá ji do Azure.
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
ms.openlocfilehash: 0b4ff1e6c76bedc4618bfa527b0045d7bfce41af
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419473"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Využití sestav infrastruktury pro poskytovatele cloudových služeb

Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití, protože dojde k a předá ji do Azure. V Azure Azure Commerce zpracovává data o využití a poplatky za využití pro příslušné předplatné Azure. K tomu dojde za stejným způsobem jako sledování využití se sleduje v globální cloud Azure.

Některé koncepty jsou konzistentní mezi globální Azure a Azure Stack. Azure Stack je místní odběry, které splňují podobnou roli s předplatným Azure. Místní odběry jsou platné pouze místně. Místní odběry se mapují na předplatná Azure, když využití je předán do Azure.

Azure Stack je místní využití měřičů. Místní využití je namapována na měřiče použít v Azure commerce. ID měřiče se však liší. K dispozici další měřiče místně než ten, který společnost Microsoft používá pro účely fakturace.

Existuje několik rozdílů mezi jaké jsou ceny za služby ve službě Azure Stack a Azure. Například ve službě Azure Stack, poplatky za virtuální počítače je jenom na základě hodiny jader vcore /, se stejná sazba pro všechny řady virtuálních počítačů, na rozdíl od Azure. Důvodem je, že globální Azure různé ceny odrážejí jiný hardware. Ve službě Azure Stack poskytuje zákazník hardware, proto neexistuje žádný důvod platby prostřednictvím různých účtovat sazby platné pro různé třídy virtuálního počítače.

Můžete získat informace o měřiče služby Azure Stack, který je používán obchodování a jejich cenu v partnerském centru stejným způsobem jako u služby Azure:

1. V partnerském centru, přejděte na **nabídce řídicího panelu**a pak vyberte **prodávat**a pak vyberte **cenami a nabídkami**.
2. V části **založená na využití služby**vyberte **aktuální**.
3. Otevřít **Azure v ceníku CSP globální** tabulky.
4. Filtrovat podle **oblasti = Azure Stack**.

## <a name="terms-used-for-billing-and-usage"></a>Termíny používané pro fakturaci a využití

Následující terminologie a Koncepty jsou použity pro využití a fakturace ve službě Azure Stack:

| Termín | Definice |
| --- | --- |
| Přímé partner CSP | Přímé partner Cloud Service Provider (CSP) obdrží fakturu přímo od společnosti Microsoft pro Azure a Azure Stackem využití a faktur zákazníkům přímo. |
| Nepřímé CSP | Nepřímí prodejci pracovat nepřímý poskytovatel (označované také jako distributor). Získávání koncovým zákazníkům; je prodejcům v systému nepřímý poskytovatel má fakturační vztah se společností Microsoft, spravuje fakturace zákazníkům a poskytuje další služby, třeba odborné pomoci. |
| Koncového zákazníka | Koncovým zákazníkům se podniky a instituce státní správy, které vlastní aplikace a další úlohy, které běží ve službě Azure Stack. |

## <a name="next-steps"></a>Další postup

- Další informace o programu CSP najdete v tématu [Cloudová řešení](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](azure-stack-billing-and-chargeback.md).
