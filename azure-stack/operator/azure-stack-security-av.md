---
title: Aktualizovat antivirovou ochranu v programu Windows Defender
titleSuffix: Azure Stack
description: Naučte se aktualizovat antivirovou ochranu v programu Windows Defender na Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: dc2a58e9b6e8701246a279b1f632ffcdd937c7ca
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780639"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Aktualizace antivirové ochrany v programu Windows Defender v Azure Stack

[Antivirová ochrana v programu Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) je antimalwarové řešení, které zajišťuje zabezpečení a antivirovou ochranu. Každá součást infrastruktury Azure Stack (hostitelé a virtuální počítače Hyper-V) je chráněná pomocí antivirové ochrany v programu Windows Defender. V případě aktuálnosti ochrany budete potřebovat pravidelné aktualizace definic, strojů a platforem v programu Windows Defender. Způsob použití aktualizací závisí na konfiguraci.

## <a name="connected-scenario"></a>Připojený scénář

[Poskytovatel prostředků Azure Stack Update](azure-stack-updates.md#the-update-resource-provider) stahuje definice antimalwaru a aktualizace modulu několikrát denně. Každá součást infrastruktury Azure Stack získá aktualizaci od poskytovatele prostředků aktualizace a automaticky použije aktualizaci.

U aktualizací pro antimalwarovou platformu použijte [měsíční aktualizaci Azure Stack](azure-stack-apply-updates.md). Měsíční aktualizace Azure Stack zahrnuje aktualizace antivirové ochrany v programu Windows Defender pro daný měsíc.

## <a name="disconnected-scenario"></a>Odpojený scénář

 Použijte [měsíční aktualizaci Azure Stack](azure-stack-apply-updates.md). Měsíční aktualizace Azure Stack zahrnuje definice antivirových programů v programu Windows Defender, modul a aktualizace platforem pro daný měsíc.

## <a name="next-steps"></a>Další kroky

[Další informace o zabezpečení služby Azure Stack](azure-stack-security-foundations.md)
