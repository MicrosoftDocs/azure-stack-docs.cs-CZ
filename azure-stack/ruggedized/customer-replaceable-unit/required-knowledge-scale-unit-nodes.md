---
title: Požadované znalosti pro práci s uzly jednotek škálování v cloudovém zařízení taktické
description: Přečtěte si o požadovaných znalostech pro práci s uzly jednotek škálování ve taktické cloudovém zařízení.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 4df7cfa84ff9b4a08d6751aa19ffdb1430a80605
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256128"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-a-tactical-cloud-appliance"></a>Požadované znalosti pro práci s uzly jednotek škálování v cloudovém zařízení taktické

Aby bylo možné dokončit procedury FRU, je nutné znát a získat přístup k následujícím koncepcím, průvodcům a webům.

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>Pracovní stanice privilegovaného přístupu a privilegovaný koncový bod

Pracovní stanice s privilegovaným přístupem (PRIVILEGOVANÝM přístupem) je vyhrazená pracovní stanice chráněná před internetovými a externími vektory hrozeb. Může se nacházet na hostiteli životního cyklu hardwaru (HLH) nebo externě na síti zákazníka pomocí směrovatelnýho přístupu k uzlům jednotek škálování centra Azure Stack.

Pokud chcete získat přístup k PRIVILEGOVANÝM přístupem, musíte se přihlásit pomocí vzdálené plochy. Získejte přihlašovací údaje a IP adresu od zákazníka.

Z tohoto počítače můžete také získat přístup k privilegovanému koncovému bodu (PEP).
Další informace o službě Privileged Access Workstation a také o PEP najdete v tématu věnovaném privilegovanému přístupu do pracovní stanice a přístup k privilegovanému koncovému bodu.

## <a name="azure-stack-hub-administrator-portal"></a>Portál pro správu centra Azure Stack

Získejte přihlašovací údaje a adresu URL portálu pro správu od zákazníka.
Další informace najdete v tématu [použití portálu pro správu](../../operator/azure-stack-manage-portals.md) 
 [v centru Azure Stack](../../operator/azure-stack-manage-portals.md).

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Dell EMC PowerEdge R640 – ruční instalace a služba

Podrobnosti o fyzickém nahrazení příslušného hardwaru najdete v tématu Instalace a odebrání R640 systémových komponent systému PowerEdge v [příručce pro instalaci a službu Dell EMC PowerEdge R640](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Přejděte do části [instalace a odebrání](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) 
 [systémových součástí](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) .

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Ruční taktické centra Microsoft Azure Stack služby cloudového zařízení

Příručka ke službě cloudové zařízení taktické hub v centru Microsoft Azure Stack obsahuje pokyny pro odebrání serverů uzlů škálování jednotky kapacity z Tracewell taktické.

## <a name="dell-emc-poweredge-idrac"></a>Dell EMC PowerEdge iDRAC

Musíte pochopit, jak můžete procházet a používat webové rozhraní Dell EMC PowerEdge iDRAC. Další informace o používání služby iDRAC najdete v tématu [integrovaná příručka Dell Remote Access Controller 9 User \' s](https://www.dell.com/support/manuals/us/en/04/poweredge-r840/idrac9_4.00.00.00_ug_new/overview-of-idrac?guid=guid-a03c2558-4f39-40c8-88b8-38835d0e9003).