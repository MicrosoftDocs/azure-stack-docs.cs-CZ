---
title: Známé problémy pro Azure Stack HCI
description: Toto téma podrobně popisuje známé problémy s Azure Stack HCL.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.openlocfilehash: 21e56fbb34c89446b0dd0e395046a9c851f391dc
ms.sourcegitcommit: f2d16c3148da50d679940e024267995b85ce6332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91407748"
---
# <a name="known-issues-for-azure-stack-hci"></a>Známé problémy pro Azure Stack HCI

>Platí pro: Azure Stack HCI, verze 20H2

Toto téma podrobně popisuje známé problémy pro Azure Stack HCI

- Připojení mezi místním clusterem a cloudovou službou Azure Stack HCI zatím nepodporuje [privátní propojení Azure](https://azure.microsoft.com/services/private-link).
- Cloudová služba Azure Stack HCI je k dispozici pouze v vybraných oblastech nyní.
- Azure Stack HCI byl oznámen 21. července 2020. Může trvat několik dní, než se rozšíření Azure Portal uživatelského rozhraní stane viditelným po celém světě. Během této doby se váš cluster může zobrazit jako výchozí stránka prostředků v Azure Portal. Děkujeme za vaši trpělivost.
- Když se interaktivně přihlašujete k operačnímu systému, uvítací obrazovka, která říká "Vítejte v Azure Stack HCI", ještě není lokalizována do České, maďarské, Nederland, polské, švédské a turecké (kódy národního prostředí cs-cz, HU-hu, nl-NL, pl-pl, pt-PT, sv-se, tr-TR). Kromě toho ve všech jazycích, které jsou jiné než anglické, se zadáním výzvy, například "[Y] ES/[N] o", akceptují pouze hodnoty "Y" a "N", i když se výzva sama o sobě zobrazí jako nezobrazený text jako [O] uživatelské rozhraní/[N] ve francouzštině nebo v Ein v němčině.
- Pokud vyhodnocujete Azure Stack HCI pomocí vnořené virtualizace, může dojít k chybě, jako je například technologie Hyper-V, protože podpora virtualizace není povolená, protože je Azure Stack HCL v závislosti na virtualizovaném zabezpečení. Existují dvě možná řešení: (1) místo toho použijte virtuální počítače Hyper-V Generation 1. nebo (2) převeďte funkci Hyper-V do režimu VHDX virtuálního počítače offline. Z hostitele spusťte následující příkaz PowerShellu na každém virtuálním počítači, který bude fungovat jako Azure Stack uzlů HCI, když jsou vypnuté: **Install-WindowsFeature-VHD \<path> -Name Hyper-V, RSAT-Hyper-v-Tools, Hyper-V-PowerShell**.
