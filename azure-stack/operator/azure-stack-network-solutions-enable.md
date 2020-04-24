---
title: Nabídka síťového řešení v centru Azure Stack s FortiGate Fortinet
description: Informace o tom, jak povolit síťové řešení v Azure Stack hub pomocí Fortinet FortiGate
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 11104bc3cfd52b6e725420ad5fd71416c12a70a8
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698534"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Nabídka síťového řešení v centru Azure Stack s FortiGate Fortinet

Bránu firewall nové generace FortiGate můžete přidat do svého tržiště centra Azure Stack. FortiGate umožňuje uživatelům vytvářet síťová řešení, jako je virtuální privátní síť (VPN), aby bylo možné Azure Stack hub a VNET peering. Síťové virtuální zařízení (síťové virtuální zařízení) řídí tok síťového provozu z hraniční sítě do jiných sítí nebo podsítí. 

Další informace o FortiGate v Azure Marketplace najdete v tématu [Fortinet FortiGate Next-Generation firewall pro jedno virtuální počítač řešení](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>Stažení požadovaných položek centra Azure Stackového tržiště

1.  Otevřete portál Azure Stack centrum pro správu.

2.  Vyberte **Správa Marketplace** a vyberte **Přidat z Azure**.

3. Do `Forti` vyhledávacího pole zadejte a dvakrát klikněte > vyberte **Stáhnout** a získejte nejnovější dostupné verze těchto položek: 
    - Fortinet FortiGate – virtuální počítač pro Azure BYOL
    - FortiGate NGFW – nasazení s jedním virtuálním počítačem (BYOL)

    ![Fortinet centra Azure Stack FortiGate](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Počkejte, dokud nebudou položky Marketplace ve stavu **staženo**. Stažení položek může trvat několik minut.

    ![Fortinet centra Azure Stack FortiGate](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Další kroky

[Nastavení sítě VPN pro centrum Azure Stack s využitím FortiGate síťové virtuální zařízení](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Postup propojení dvou virtuální sítě prostřednictvím partnerského vztahu](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Jak navázat připojení VNET-to-VNET k Fortinet FortiGate síťové virtuální zařízení](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
