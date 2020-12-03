---
title: Přidání CommVault do tržiště centra Azure Stack
description: Naučte se, jak přidat CommVault do Marketplace centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: f035ce5d08e88a6fa5844e405a4fa5a335fa3e5c
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525485"
---
# <a name="add-commvault-to-azure-stack-hub-marketplace"></a>Přidání CommVault do tržiště centra Azure Stack

Tento článek vás seznámí s nabídkou CommVault Live Sync k aktualizaci virtuálního počítače pro obnovení (VM), který se nachází v samostatné jednotce škálování centra Azure Stack. Můžete si stáhnout a nabídnout CommVault jako řešení zálohování a replikace pro vaše uživatele.

## <a name="notes-for-commvault"></a>Poznámky pro CommVault

- Váš uživatel musí nainstalovat software pro zálohování a replikaci na virtuální počítač v rámci svého zdrojového předplatného Azure Stackho centra. Azure Site Recovery a Azure Backup můžou nabízet umístění mimo zásobník pro ukládání záloh a imagí pro obnovení. Oba vyžadují vytvoření trezoru Recovery Services v Azure před stažením softwarových imagí, které se mají nainstalovat do centra Azure Stack. Softwarové Image je možné stáhnout z: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) a [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  

- Je možné, že budete potřebovat licence pro software třetí strany (Pokud je zvolená).
- Vaši uživatelé můžou potřebovat pomoc při připojení svého zdroje a cíle prostřednictvím brány VPN nebo veřejné IP adresy na hostiteli zálohování a replikace.
- Cílové předplatné Azure cloudu nebo předplatné v cíli obnovení Azure Stack centru.
- Cílová skupina prostředků a účet Blob Storage v cíli obnovení Azure Stack hub.
- Některá řešení vyžadují, abyste v cílovém předplatném vytvořili virtuální počítače, které musí spustit zcela nepřetržitou, aby bylo možné přijímat změny ze zdrojového serveru. Při [zálohování virtuálního počítače v Azure Stack hub pomocí CommVault](../user/azure-stack-network-howto-backup-commvault.md)vytvoří CommVault živá synchronizace cílového virtuálního počítače pro obnovení při počáteční konfiguraci a udržuje je nečinný (neběží, není fakturovatelná), dokud se změny v průběhu replikačního cyklu nemusejí použít.

## <a name="get-commvault-for-your-marketplace"></a>Získání CommVault pro Marketplace

1. Otevřete portál Azure Stack centrum pro správu.
2. Vyberte **Správa Marketplace**  >  **Přidat z Azure**.

    ![CommVault pro centrum Azure Stack](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Zadejte `commvault`.
4. Vyberte **zkušební verzi CommVault**. A pak vyberte **Stáhnout**.

## <a name="next-steps"></a>Další kroky

- [Zálohování virtuálního počítače v Azure Stack hub pomocí CommVault](../user/azure-stack-network-howto-backup-commvault.md)
- [Přehled nabízených služeb v centru Azure Stack](service-plan-offer-subscription-overview.md)
