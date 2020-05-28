---
title: Přidání Fortinet FortiGate do tržiště centra Azure Stack
description: Naučte se, jak přidat Fortinet FortiGate do svého tržiště centra Azure Stack a umožnit tak uživatelům vytvářet síťová řešení.
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 12dc8715b90aa257953b5dc13bf671c9264aea8d
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111173"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Nabídka síťového řešení v centru Azure Stack s FortiGate Fortinet

Do tržiště Azure Stack Hub můžete přidat bránu firewall FortiGate Next-Generation (NGFW). FortiGate umožňuje uživatelům vytvářet síťová řešení, jako je virtuální privátní síť (VPN), aby bylo možné Azure Stack rozbočovač a VNET peering. Síťové virtuální zařízení (síťové virtuální zařízení) řídí tok síťového provozu z hraniční sítě do jiných sítí nebo podsítí.

Další informace o FortiGate v Azure Marketplace najdete v tématu [Fortinet FortiGate Next-Generation firewall s jedním virtuálním počítačem řešení](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>Stažení požadovaných položek centra Azure Stackového tržiště

1. Otevřete portál Azure Stack centrum pro správu.

2. Vyberte **Správa Marketplace** a vyberte **Přidat z Azure**.

3. Do `Forti` vyhledávacího pole zadejte a dvakrát klikněte > vyberte **Stáhnout** a získejte nejnovější dostupné verze těchto položek:
    - Fortinet FortiGate – virtuální počítač pro Azure BYOL
    - FortiGate NGFW – nasazení s jedním virtuálním počítačem (BYOL)

    ![Fortinet centra Azure Stack FortiGate](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

4. Počkejte, dokud nebudou položky Marketplace ve stavu **staženo**. Stažení položek může trvat několik minut.

    ![Fortinet centra Azure Stack FortiGate](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Další kroky

- [Nastavení sítě VPN pro centrum Azure Stack s využitím FortiGate síťové virtuální zařízení](../user/azure-stack-network-howto-vnet-to-onprem.md)  
- [Postup propojení dvou virtuální sítě prostřednictvím partnerského vztahu](../user/azure-stack-network-howto-vnet-to-vnet.md)  
- [Jak navázat připojení VNET-to-VNET k Fortinet FortiGate síťové virtuální zařízení](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
