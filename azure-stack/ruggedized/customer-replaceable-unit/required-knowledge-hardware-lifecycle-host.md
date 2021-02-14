---
title: Požadované znalosti pro práci s hostitelem životního cyklu hardwaru
description: Přečtěte si o požadovaných znalostech pro práci s hostitelem životního cyklu hardwaru
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3d3e650e9b6b1b6c37e2f265aa5c049246600c2f
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487778"
---
# <a name="required-knowledge-for-working-with-the-hardware-lifecycle-host"></a>Požadované znalosti pro práci s hostitelem životního cyklu hardwaru

Aby bylo možné dokončit procedury FRU, je nutné znát a získat přístup k následujícím koncepcím a průvodcům.

## <a name="hardware-lifecycle-host"></a>Hostitel životního cyklu hardwaru

Hostitel životního cyklu hardwaru (HLH) je fyzický management server umístěný v horní části racku centra Azure Stack. Pro přístup k hostiteli se k němu můžete připojit pomocí jedné ze tří metod:

* Přímý (Stavový košík)
* iDRAC (port služby)
* iDRAC (přístup IP)

Pokud se nacházíte v datovém centru, můžete se k HLH připojit přímo pomocí portů VGA a USB. Například připojení k chybovému košíku.

Pokud se nacházíte v datovém centru, připojte svůj přenosný počítač k portu služby iDRAC 9 pomocí kabelu micro USB. Další informace najdete v tématu přístup k rozhraní iDRAC přes přímé připojení USB.

Spolupracujte se zákazníkem a připojte se k HLH ze sítě pro správu a pracovní stanice pro správu s IP adresou iDRAC.

> [!NOTE]
> Přímo k HLH iDRAC se můžou připojit jenom sítě, které jste dříve přidali do seznamů ACL pro přepínače.

## <a name="credentials"></a>Přihlašovací údaje

Spolupracujte se zákazníkem, abyste získali přihlašovací údaje k následujícím akcím:

* HLH
* správce
* účet iDRAC (volitelné)

Účet systému Windows s úplnými právy správce.

Pokud se nepřipojujete přímo k serveru pomocí příkazového košíku, budete k získání přístupu k virtuálnímu rozhraní KVM potřebovat přihlašovací údaje účtu iDRAC.


