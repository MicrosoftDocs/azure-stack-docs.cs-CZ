---
title: Nabídka CommVault položky Marketplace v Azure Stack-MDC
description: Nasaďte CommVault z webu Azure Stack Marketplace pro modulární datové centrum (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: c2f98725a93dbd471f257d8a038a989d9f892c68
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910835"
---
# <a name="offer-commvault-marketplace-item-in-azure-stack---modular-data-center-mdc"></a>Nabídka CommVault položky Marketplace Azure Stack v modulárním datovém centru (MDC)

*Platí pro: modulární datové centrum, Azure Stack centrum je robustní*

CommVault podporuje zálohování a obnovení následujících typů prostředků v centru Azure Stack:

- Zálohování na úrovni virtuálního počítače
  - Virtuální počítač IaaS
  - Nespravované disky
  - Spravované disky
  - Další informace najdete v tématu [zálohování Virtual Machines](https://documentation.commvault.com/commvault/v11/article?p=86503.htm).

- Záloha účtu úložiště
  - Objekt blob
  - Další informace najdete v tématu [Přehled služby Azure Blob Storage](https://documentation.commvault.com/commvault/v11/article?p=30063.htm).

- Zálohování založené na agentovi
  - Hostovaný operační systém – Windows a Linux
  - Aplikace--SQL, MySQL
  - Další informace najdete v tématu [Agenti zálohování](https://documentation.commvault.com/commvault/v11/article?p=14333.htm).

CommVault můžete nasadit na externím počítači a vzdáleně chránit prostředky v Azure Stackm centru. Kromě toho je možné nasadit CommVault jako virtuální zařízení v centru Azure Stack. Kompletní pokyny z CommVault jsou k dispozici na svém dokumentačním webu, který se zabývá [Azure Stack hub](https://documentation.commvault.com/commvault/v11/article?p=86486.htm). CommVault také zveřejňuje [úplný seznam funkcí pro Microsoft Azure](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm).

## <a name="deploy-from-azure-stack-hub-marketplace"></a>Nasazení z webu centra Azure Stack Marketplace

CommVault publikuje obrázek BYOL v Azure Marketplace a umožňuje, aby se image pro syndikaci Azure Stack centru. Minimální verze požadovaná k zálohování virtuálních počítačů v Azure Stack je SP16. Pokud máte v úmyslu používat virtuální zařízení, nezapomeňte aktualizovat na SP16 (nejnovější [dlouhodobě podporovaná verze](https://documentation.commvault.com/commvault/v11/article?p=2617.htm)) nebo SP17 (nejnovější dostupná prodejní verze).

| Cloud        | Verze | K dispozici pro syndikaci | Další aktualizace |
|--------------|---------|---------------------------|-------------|
| Veřejný Azure | SP13    | Ano                       | Bude doplněno         |
| Gov Azure    | SP13    | Bude doplněno                       | Bude doplněno         |

## <a name="download-from-azure-marketplace"></a>Stáhnout z Azure Marketplace

Obsluha centra Azure Stack může stahovat položky do místního webu Azure Stack Marketplace pro připojená a odpojená prostředí. V připojeném prostředí může operátor procházet seznam dostupných položek, které se mají přidat z Azure:

![Přidat z Azure](media/azure-stack-commvault-offer-tzl/add-from-azure.png)

## <a name="upload-and-publish-manually"></a>Ruční odeslání a publikování

V odpojených prostředích musí být položka stažená z Azure a pak se do centra Azure Stack nahrála ručně. Další informace najdete v tématu [Úplné pokyny pro připojená a odpojená prostředí](../../operator/azure-stack-download-azure-marketplace-item.md).

## <a name="deployment-considerations"></a>Aspekty nasazování

- Nasazení externích do centra Azure Stack
- Nasadit jako virtuální zařízení do centra Azure Stack
- Knihovna disků vs cloudová knihovna
- Síťový řádek aspektů pohledu
- Izolace na úrovni předplatného

## <a name="next-steps"></a>Další kroky

- Další informace o ochraně virtuálních počítačů s IaaS najdete v tématu Ochrana virtuálních počítačů v centru Azure Stack.
