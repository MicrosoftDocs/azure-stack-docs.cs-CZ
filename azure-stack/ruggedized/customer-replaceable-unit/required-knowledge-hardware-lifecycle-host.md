---
title: Požadované znalosti pro práci s hostitelem životního cyklu hardwaru
description: Přečtěte si o požadovaných znalostech pro práci s hostitelem životního cyklu hardwaru
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d6b13e26a1df94958f2b91d7a587e6d7f7a9f4bf
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867583"
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

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Dell EMC PowerEdge R640 – ruční instalace a služba

Podrobnosti o fyzickém nahrazení příslušného hardwaru najdete v tématu věnovaném postupům pro nahrazení R640 FRU v [](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4)systému PowerEdge v 
 [příručce pro instalaci a službu Dell EMC PowerEdge R640](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Přejděte do části [instalace a odebrání systémových součástí](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) .

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Ruční taktické centra Microsoft Azure Stack služby cloudového zařízení

Příručka k taktické cloudového zařízení služby Microsoft Azure Stack hub obsahuje pokyny pro odebrání hostitele životního cyklu hardwaru z Taktickéu Tracewell.

## <a name="dell-emc-poweredge-idrac"></a>Dell EMC PowerEdge iDRAC

Musíte pochopit, jak můžete procházet a používat webové rozhraní Dell EMC PowerEdge iDRAC.

