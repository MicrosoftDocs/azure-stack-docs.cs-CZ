---
title: Aktualizace a upgrady
description: Přehled toho, jak se aktualizace a upgrady aplikují na Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: f56853ce64575d32ae10ccf7ac2aa0a1a640ec5b
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867526"
---
# <a name="updates-and-upgrades"></a>Aktualizace a upgrady

> Platí pro: Azure Stack HCI, verze 20H2

Azure Stack řešení HCI jsou navržená tak, aby měla předvídatelné možnosti aktualizace a upgradu.

## <a name="support-period"></a>Doba podpory

Můžete očekávat, že hardwarová služba, podpora a aktualizace zabezpečení od partnera pro hardwarové řešení společnosti Microsoft po dobu nejméně pěti let. Operační systém Azure Stack HCI podporuje společnost Microsoft po dobu dvou let s ročními aktualizacemi funkcí, které má zákazník po dobu jednoho roku nasadit.

:::image type="content" source="media/updates/lifecycle-versions.png" alt-text="Roční plán vydaných verzí Azure Stack HCI" border="false":::

## <a name="updating-azure-stack-hci"></a>Aktualizace Azure Stack HCL

Podobně jako ve Windows 10 se Azure Stack HCL vždycky udržuje v aktuálním stavu díky měsíčním aktualizacím kvality a zabezpečení od Microsoftu. Aktualizace se spouští pomocí aktualizace pro clustery, jako na Windows serveru.

Hardwarové partneři a tvůrci řešení se mohou připojit k centru pro správu systému Windows a vyvíjet rozšíření, aby se firmware, ovladače a systém BIOS serverů udržovaly v aktuálním stavu a konzistentní v rámci uzlů clusteru. Zákazníci, kteří si koupí integrovaný systém s Azure Stack předinstalovaným rozhraním HCI, mohou instalovat aktualizace řešení prostřednictvím těchto rozšíření. Zákazníci, kteří si zakoupí ověřené hardwarové uzly, můžou potřebovat aktualizace samostatně, podle doporučení dodavatele hardwaru.

Virtuální počítače je také potřeba pravidelně aktualizovat. Kromě web Windows Update a Windows Server Update Services můžete k aktualizaci virtuálních počítačů použít Azure Update Management.

## <a name="upgrading-to-azure-stack-hci"></a>Upgrade na Azure Stack HCI

Můžete upgradovat z Windows serveru 2019 na Azure Stack HCL, ale nehladce. v tuto chvíli není k dispozici žádný místní upgrade. Upgrade vyžaduje časový interval pro správu a čistou instalaci na každém serveru a každý uzel se v rámci postupného upgradu clusteru znovu sestaví.

## <a name="cluster-aware-updating"></a>Aktualizace pro clustery

Aktualizace clusteru vyžaduje orchestraci, protože některé aktualizace vyžadují restartování operačního systému a servery je potřeba restartovat po jednom, aby se zajistila dostupnost clusteru. Aktualizace pro clustery (CAU) je výchozím clusterem programu Orchestrator clusteru společnosti Microsoft, který umožňuje provádět aktualizace prostřednictvím integrovaného prostředí aktualizace v centru pro správu systému Windows nebo ručně prostřednictvím příkazů prostředí PowerShell. Aktualizace pro clustery je rozšiřitelná tak, aby podporovala moduly plug-in pro partnery, které načítají aktualizace firmwaru a ovladačů.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Aktualizace Azure Stackch clusterů HCI](../manage/update-cluster.md)
