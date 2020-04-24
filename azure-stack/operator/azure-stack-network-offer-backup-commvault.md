---
title: Přidání CommVault do tržiště centra Azure Stack
description: Naučte se, jak přidat CommVault do Marketplace centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: d79137c5a00b4762cf1f7729000f8912f13ba066
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661356"
---
# <a name="add-commvault-to-the-azure-stack-hub-marketplace"></a>Přidání CommVault do tržiště centra Azure Stack

Tento článek vás seznámí s nabídkou CommVault Live Sync k aktualizaci virtuálního počítače pro obnovení, který se nachází na samostatné jednotce škálování centra Azure Stack. Můžete si stáhnout a nabídnout CommVault jako řešení zálohování a replikace pro vaše uživatele. 

## <a name="notes-for-commvault"></a>Poznámky pro CommVault

- Váš uživatel musí nainstalovat software pro zálohování a replikaci na virtuální počítač v rámci svého zdrojového předplatného centra Azure Stack. Azure Site Recovery a Azure Backup můžou nabízet umístění mimo zásobník pro ukládání záloh a imagí pro obnovení. Oba vyžadují vytvoření trezoru Recovery Services v Azure před stažením softwarových imagí, které se mají nainstalovat do centra Azure Stack z těchto umístění: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) a [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Je možné, že budete potřebovat licence pro software třetích stran (Pokud je zvolená).
- Vaši uživatelé můžou potřebovat asistenta připojení svého zdroje a cíle prostřednictvím brány Virtual Network (VPN) nebo veřejné IP adresy na hostiteli zálohování a replikace.
- Cílové předplatné Azure cloudu nebo předplatné v cíli obnovení Azure Stack centru.
- Cílová skupina prostředků a účet Blob Storage v cíli obnovení Azure Stack centru.
- Některá řešení vyžadují, abyste v cílovém předplatném vytvořili virtuální počítače, které musí spustit zcela nepřetržitou, aby bylo možné přijímat změny ze zdrojového serveru. Při [zálohování virtuálního počítače v Azure Stack hub pomocí CommVault](../user/azure-stack-network-howto-backup-commvault.md)vytvoří CommVault živá synchronizace cílový virtuální počítač pro obnovení při počáteční konfiguraci a udržuje je nečinný (není spuštěná, není fakturovatelná), dokud není potřeba změny použít během cyklu replikace.


## <a name="get-commvault-for-your-marketplace"></a>Získání CommVault pro Marketplace

1. Otevřete portál pro správu centra Azure Stack.
2. Vyberte **Správa** > Marketplace**Přidat z Azure**.

    ![CommVault pro centrum Azure Stack](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Zadejte `commvault`.
4. Vyberte **zkušební verzi CommVault**. A pak vyberte **Stáhnout**.


## <a name="next-steps"></a>Další kroky

[Zálohování virtuálního počítače v Azure Stack hub pomocí CommVault](../user/azure-stack-network-howto-backup-commvault.md)

[Přehled nabízených služeb v centru Azure Stack](service-plan-offer-subscription-overview.md)
