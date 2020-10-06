---
title: Změnit nastavení ochrany osobních údajů
description: V tomto tématu najdete pokyny, jak změnit nastavení ochrany osobních údajů v operačním systému Azure Stack HCI nebo Windows serveru.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 8055e02b702fb5e585b5570171c6dabb247b623f
ms.sourcegitcommit: 79be77eb2bebf314d956258fc9bc14b2014be190
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745142"
---
# <a name="change-privacy-settings-on-individual-servers"></a>Změna nastavení ochrany osobních údajů na jednotlivých serverech

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019, Windows Server 2016

Zde je postup, jak změnit nastavení ochrany osobních údajů na serveru, na kterém je spuštěný operační systém Azure Stack HCI nebo Windows Server. Pokud chcete spravovat nastavení ochrany osobních údajů pomocí Zásady skupiny na několika serverech najednou nebo v podniku jako celku, přečtěte si téma [Správa podnikových diagnostických dat](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data).

## <a name="azure-stack-hci-or-server-core"></a>Azure Stack HCI nebo jádro serveru
Pokud server používá operační systém Azure Stack HCI nebo Windows Server s možností instalace jádra serveru, použijte následující postup:
1. Připojení k serveru v clusteru Azure Stack HCI pomocí vzdálené plochy, vzdálené správy (bezobslužného ovládání nebo řadiče pro správu základní desky) s KVM nebo přihlášením místně pomocí klávesnice a monitoru. 
1. Pokud se připojíte k serveru se systémem Azure Stack HCI, automaticky se otevře nástroj Konfigurace serveru (sconfig). Pokud se připojíte k serveru se systémem Windows Server s jádrem serveru, zadejte na příkazovém řádku příkaz `Sconfig` .
1. V poli **Zadejte číslo pro výběr možnosti:** Prompt, Type **10** a stiskněte klávesu ENTER.
1. Na příkazovém řádku s potvrzením **změny telemetrie** vyberte **Ano** , pokud chcete zobrazit následující možnosti:

    Dostupná nastavení telemetrie: **1 zabezpečení**, **2 Basic**, **3 rozšířené**, **4 úplné**

    >[!NOTE]
    > Výchozí nastavení pro Azure Stack HCI je **1 zabezpečení**.

1. V **nastavení zadat nové telemetrie:** zadejte požadovanou možnost a stiskněte klávesu ENTER.

## <a name="full-desktop"></a>Úplná plocha
Pokud na serveru běží Windows Server a má možnost úplné instalace plochy, použijte následující postup:
1. Připojte se k řídicímu panelu Správce serveru Windows serveru.

    Místně se můžete připojit pomocí klávesnice a monitoru nebo pomocí řadiče pro vzdálenou správu (bezobslužné nebo BMC) nebo vzdálené plochy. 

1. V části Správa serveru pod **řídicím panelem**vyberte **místní server**.
1. Na stránce **vlastnosti** serveru vedle možnosti **zpětná vazba & Diagnostika**vyberte **Nastavení**.

    Na stránce **Nastavení** se zobrazí **Četnost zpětné vazby** a nastavení **dat diagnostiky a použití** . 
 
1. Rozbalte nastavení **Diagnostika a data o využití** a vyberte jednu z následujících možností:
    - **Požadovaná diagnostická data**
    - **Rozšířené**
    - **Volitelná diagnostická data**

    >[!NOTE]
    > Pokud si všimněte, že **některá nastavení spravuje vaše organizace** , na stránce **Nastavení** nemusí být nastavení **Diagnotic a data o využití** k dispozici.

## <a name="next-steps"></a>Další kroky
Pokud chcete spravovat nastavení ochrany osobních údajů pomocí Zásady skupiny na několika serverech najednou nebo v podniku jako celku, přečtěte si téma:
-   [Spravovat podniková diagnostická data](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)
