---
title: Nabídnout síťové řešení v Azure Stack s Fortinet FortiGate | Microsoft Docs
description: Informace o tom, jak povolit síťové řešení v Azure Stack pomocí Fortinet FortiGate
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 27012c491054043f45004d76787538091864577c
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962438"
---
# <a name="offer-a-network-solution-in-azure-stack-with-fortinet-fortigate"></a>Nabídka síťového řešení v Azure Stack s Fortinet FortiGate

K Azure Stack Marketplace můžete přidat bránu firewall nové generace (FortiGate). FortiGate umožňuje uživatelům vytvářet síťová řešení, jako je virtuální privátní síť (VPN), aby bylo možné Azure Stack a VNET peering. Síťové virtuální zařízení (síťové virtuální zařízení) řídí tok síťového provozu z hraniční sítě do jiných sítí nebo podsítí. 

Další informace o FortiGate v Azure Marketplace najdete v tématu [Fortinet FortiGate Next-Generation firewall pro jedno virtuální počítač řešení](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-marketplace-items"></a>Stažení požadovaných položek Azure Stack Marketplace

1.  Otevřete portál Azure Stack správce.

2.  Vyberte **Správa Marketplace** a vyberte **Přidat z Azure**.

3. Do vyhledávacího pole zadejte `Forti` a dvakrát klikněte > vyberte **Stáhnout** , abyste získali nejnovější dostupné verze těchto položek: 
    - Fortinet FortiGate – virtuální počítač pro Azure BYOL
    - FortiGate NGFW – nasazení s jedním virtuálním počítačem (BYOL)

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Počkejte, dokud nebudou položky Marketplace ve stavu **staženo**. Stažení položek může trvat několik minut.

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Další kroky

[Nastavení sítě VPN pro Azure Stack s využitím FortiGate síťové virtuální zařízení](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Postup propojení dvou virtuální sítě prostřednictvím partnerského vztahu](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Jak navázat připojení VNET-to-VNET k Fortinet FortiGate síťové virtuální zařízení](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
