---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/9/2020
ms.reviewer: sijuman
ms.lastreviewed: 12/9/2020
ms.openlocfilehash: 003f6801209d5d5ab1c9c4bd1df0fa47578004ee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935138"
---
### <a name="error-thrown-when-installing-the-az-modules"></a>Při instalaci AZ modules došlo k chybě.

- Platí: Tento problém se týká 2002 a novějších verzí.
- Příčina: při instalaci modulu je vyvolána chyba. Spustí se chybová zpráva: `Register-PacakgeSource : A parameter cannot be found that matches parameter name. 'PackageManagementProvider'.` nebo chybová zpráva může obsahovat následující text: `PackageManagement\Install-Package : Cannot convert value "2.0.1-preview" to type "System.Version". Error: "Input string was not in a correct format."`
- Náprava: ve stejné relaci spusťte následující rutinu:  
    `Install-Module PowershellGet -MinimumumVersion 2.3.0 -Force`  
Zavřete svou relaci a spusťte novou relaci PowerShellu se zvýšenými oprávněními.
- Výskyt: běžné