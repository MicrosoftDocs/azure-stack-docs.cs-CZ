---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: e456f17c75b129a3f45c64b9b55e75d21ecb2973
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935137"
---
### <a name="when-installing-az-module-falsely-throws-admin-rights-required-error"></a>Při instalaci AZ Module false vyvolá chyba požadovaná oprávnění správce.

- Platí: Tento problém se týká 2002 a novějších verzí.
- Příčina: při instalaci modulu z příkazového řádku se zvýšenými oprávněními se vyvolá chyba. Chyba říká, `Administrator rights required` .
- Náprava: zavřete relaci a spusťte novou relaci PowerShellu se zvýšenými oprávněními. Ujistěte se, že neexistuje existující AZ. Modul Accounts byl načten v relaci.
- Výskyt: běžné