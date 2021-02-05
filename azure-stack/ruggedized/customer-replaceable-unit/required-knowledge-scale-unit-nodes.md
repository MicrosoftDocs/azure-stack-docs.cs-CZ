---
title: Požadované znalosti pro práci s uzly jednotek škálování v robustním cloudovém zařízení
description: Přečtěte si o požadovaných znalostech pro práci s uzly jednotek škálování v robustním cloudovém zařízení.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 5407a6097b4b7a602a70f9ce24928df4456b151f
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99571122"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-a-ruggedized-cloud-appliance"></a>Požadované znalosti pro práci s uzly jednotek škálování v robustním cloudovém zařízení

Aby bylo možné dokončit procedury FRU, je nutné znát a získat přístup k následujícím koncepcím, průvodcům a webům.

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>Pracovní stanice privilegovaného přístupu a privilegovaný koncový bod

Pracovní stanice s privilegovaným přístupem (PRIVILEGOVANÝM přístupem) je vyhrazená pracovní stanice chráněná před internetovými a externími vektory hrozeb. Může se nacházet na hostiteli životního cyklu hardwaru (HLH) nebo externě na síti zákazníka pomocí směrovatelnýho přístupu k uzlům jednotek škálování centra Azure Stack.

Pokud chcete získat přístup k PRIVILEGOVANÝM přístupem, musíte se přihlásit pomocí vzdálené plochy. Získejte přihlašovací údaje a IP adresu od zákazníka.

Z tohoto počítače můžete také získat přístup k privilegovanému koncovému bodu (PEP).
Další informace najdete v tématu pracovní stanice s privilegovaným přístupem a přístup k privilegovanému koncovému bodu.

## <a name="azure-stack-hub-administrator-portal"></a>Portál pro správu centra Azure Stack

Získejte přihlašovací údaje a adresu URL portálu pro správu od zákazníka.
Další informace najdete v tématu [použití portálu pro správu](../../operator/azure-stack-manage-portals.md) 
 [v centru Azure Stack](../../operator/azure-stack-manage-portals.md).

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Dell EMC PowerEdge R640 – ruční instalace a služba

Další informace o fyzickém nahrazení příslušného hardwaru najdete v tématu Instalace a odebrání R640 systémových komponent na serveru PowerEdge v příručce pro [instalaci a službu Dell EMC PowerEdge R640](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4).
Přejděte do části [instalace a odebrání](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) 
 [systémových součástí](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) .

## <a name="microsoft-azure-stack-hub-ruggedized-cloud-appliance-service-manual"></a>Ruční Ruční služba cloudového zařízení ve službě Microsoft Azure Stack hub

Ruční služba cloudového zařízení služby Microsoft Azure Stack hub obsahuje pokyny pro odebrání serverů uzlů škálování jednotky z Tracewell robustních lusků.

## <a name="dell-emc-poweredge-idrac"></a>Dell EMC PowerEdge iDRAC

Musíte pochopit, jak můžete procházet a používat webové rozhraní Dell EMC PowerEdge iDRAC. Další informace o používání rozhraní iDRAC najdete v tématu [integrovaná příručka Dell Remote Access Controller 9 User \' s](https://www.dell.com/support/manuals/us/en/04/poweredge-r840/idrac9_4.00.00.00_ug_new/overview-of-idrac?guid=guid-a03c2558-4f39-40c8-88b8-38835d0e9003).
