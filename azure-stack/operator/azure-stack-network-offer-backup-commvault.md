---
title: Přidání CommVault do tržiště Azure Stack | Microsoft Docs
description: Naučte se, jak přidat CommVault do Marketplace Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 540121b1de46eb8b1f3669a441c5aff5d8ff2e33
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167506"
---
# <a name="add-commvault-to-the-azure-stack-marketplace"></a>Přidání CommVault do webu Azure Stack Marketplace

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tento článek vás seznámí s nabídkou CommVault Live Sync k aktualizaci virtuálního počítače pro obnovení, který se nachází na samostatné jednotce škálování Azure Stack. Můžete si stáhnout a nabídnout CommVault jako řešení zálohování a replikace pro vaše uživatele. 

## <a name="notes-for-commvault"></a>Poznámky pro CommVault

- Váš uživatel musí nainstalovat software pro zálohování a replikaci na virtuální počítač ve svém zdrojovém Azure Stack předplatném. Azure Site Recovery a Azure Backup můžou nabízet umístění mimo zásobník pro ukládání záloh a imagí pro obnovení. Oba vyžadují vytvoření trezoru Recovery Services v Azure před stažením softwarových imagí, které se mají nainstalovat do Azure Stack z následujících umístění: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) a [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Je možné, že budete potřebovat licence pro software třetích stran (Pokud je zvolená).
- Vaši uživatelé můžou potřebovat asistenta připojení svého zdroje a cíle prostřednictvím brány Virtual Network (VPN) nebo veřejné IP adresy na hostiteli zálohování a replikace.
- Cílové předplatné Azure cloudu nebo předplatné pro cíl obnovení Azure Stack.
- Cílová skupina prostředků a účet Blob Storage v cíli obnovení Azure Stack.
- Některá řešení vyžadují, abyste v cílovém předplatném vytvořili virtuální počítače, které musí spustit zcela nepřetržitou, aby bylo možné přijímat změny ze zdrojového serveru. Při [zálohování virtuálního počítače v Azure Stack pomocí CommVault](../user/azure-stack-network-howto-backup-commvault.md)vytvoří CommVault Live Sync cílové virtuální počítače pro obnovení při počáteční konfiguraci a udržuje je nečinné (neběží, není fakturovatelná), dokud není potřeba změny použít během cyklu replikace.


## <a name="get-commvault-for-your-marketplace"></a>Získání CommVault pro Marketplace

1. Otevřete Azure Stack portálu pro správu.
2. Vyberte **správu Marketplace** > **Přidat z Azure**.

    ![CommVault pro Azure Stack](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Zadejte `commvault`.
4. Vyberte **Commvaultový záznam**. A pak vyberte **Stáhnout**.


## <a name="next-steps"></a>Další kroky

[Zálohování virtuálního počítače na Azure Stack pomocí CommVault](../user/azure-stack-network-howto-backup-commvault.md)

[Přehled nabízených služeb v Azure Stack](service-plan-offer-subscription-overview.md)
