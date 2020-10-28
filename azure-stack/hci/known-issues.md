---
title: Známé problémy pro Azure Stack HCI
description: Toto téma podrobně popisuje známé problémy s Azure Stack HCL.
author: myoungerman
ms.author: v-myoung
ms.topic: troubleshooting
ms.date: 10/27/2020
ms.openlocfilehash: 05884ce83f6f35f8d75c632f67dabd38fa3c2814
ms.sourcegitcommit: 75603007badd566f65d01ac2eacfe48ea4392e58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92688328"
---
# <a name="known-issues-for-azure-stack-hci"></a>Známé problémy pro Azure Stack HCI

>Platí pro: Azure Stack HCI, verze 20H2

Toto téma podrobně popisuje známé problémy pro Azure Stack HCI

- 20. října 2020 Preview (KB4580388) může způsobit selhání operace aktualizace pro clustery (CAU), pokud se očekává, že kterýkoli z virtuálních počítačů bude během aktualizace pro clustery (VM) provádět Migrace za provozu. Aby k tomu nedocházelo, změňte výchozí chování dočasně, aby funkce CAU místo Migrace za provozu používala rychlou migraci. Další informace najdete v [poznámkách k verzi](release-notes.md#october-20-2020-preview-update-kb4580388) .
- Připojení mezi místním clusterem a cloudovou službou Azure Stack HCI zatím nepodporuje [privátní propojení Azure](https://azure.microsoft.com/services/private-link).
- Cloudová služba Azure Stack HCI je k dispozici pouze v vybraných oblastech nyní.
- Když se interaktivně přihlašujete k operačnímu systému, uvítací obrazovka, která říká "Vítejte v Azure Stack HCI", ještě není lokalizována do České, maďarské, Nederland, polské, švédské a turecké (kódy národního prostředí cs-cz, HU-hu, nl-NL, pl-pl, pt-PT, sv-se, tr-TR). Kromě toho ve všech jazycích, které jsou jiné než anglické, se zadáním výzvy, například "[Y] ES/[N] o", akceptují pouze hodnoty "Y" a "N", i když se výzva sama o sobě zobrazí jako nezobrazený text jako [O] uživatelské rozhraní/[N] ve francouzštině nebo v Ein v němčině.
- Pokud vyhodnocujete Azure Stack HCI pomocí vnořené virtualizace, může dojít k chybě, jako je například technologie Hyper-V, protože podpora virtualizace není povolená, protože je Azure Stack HCL v závislosti na virtualizovaném zabezpečení. Existují dvě možná řešení: (1) místo toho použijte virtuální počítače Hyper-V generace 1. nebo (2) převeďte funkci Hyper-V do režimu VHDX virtuálního počítače offline. Z hostitele spusťte následující příkaz PowerShellu na každém virtuálním počítači, který bude fungovat jako Azure Stack uzlů HCI, když jsou vypnuté: **Install-WindowsFeature-VHD \<path> -Name Hyper-V, RSAT-Hyper-v-Tools, Hyper-V-PowerShell** .
