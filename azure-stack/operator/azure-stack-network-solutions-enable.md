---
title: Nabízet síťové řešení v Azure Stackovém centru s FortiGateem Fortinet | Microsoft Docs
description: Informace o tom, jak povolit síťové řešení v Azure Stack hub pomocí Fortinet FortiGate
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: dde1ba144f14bd084e2e1050b1401143a06f61c1
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534647"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Nabídka síťového řešení v centru Azure Stack s FortiGate Fortinet

Bránu firewall nové generace FortiGate můžete přidat do svého tržiště centra Azure Stack. FortiGate umožňuje uživatelům vytvářet síťová řešení, jako je virtuální privátní síť (VPN), aby bylo možné Azure Stack hub a VNET peering. Síťové virtuální zařízení (síťové virtuální zařízení) řídí tok síťového provozu z hraniční sítě do jiných sítí nebo podsítí. 

Další informace o FortiGate v Azure Marketplace najdete v tématu [Fortinet FortiGate Next-Generation firewall pro jedno virtuální počítač řešení](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>Stažení požadovaných položek centra Azure Stackového tržiště

1.  Otevřete portál Azure Stack centrum pro správu.

2.  Vyberte **Správa Marketplace** a vyberte **Přidat z Azure**.

3. Do vyhledávacího pole zadejte `Forti` a dvakrát klikněte > vyberte **Stáhnout** , abyste získali nejnovější dostupné verze těchto položek: 
    - Fortinet FortiGate – virtuální počítač pro Azure BYOL
    - FortiGate NGFW – nasazení s jedním virtuálním počítačem (BYOL)

    ![Fortinet centra Azure Stack FortiGate](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Počkejte, dokud nebudou položky Marketplace ve stavu **staženo**. Stažení položek může trvat několik minut.

    ![Fortinet centra Azure Stack FortiGate](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Další kroky

[Nastavení sítě VPN pro centrum Azure Stack s využitím FortiGate síťové virtuální zařízení](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Postup propojení dvou virtuální sítě prostřednictvím partnerského vztahu](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Jak navázat připojení VNET-to-VNET k Fortinet FortiGate síťové virtuální zařízení](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
