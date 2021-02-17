---
title: Správa logických sítí tenanta
description: Toto téma obsahuje podrobné pokyny k vytváření, aktualizaci a odstraňování logických sítí po nasazení síťového adaptéru pomocí centra pro správu systému Windows.
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/02/2021
ms.openlocfilehash: 5cf88e5befd551eb7789388807c9c1e4df671dc9
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562955"
---
# <a name="manage-tenant-logical-networks"></a>Správa logických sítí tenanta

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019, Windows Server 2016

Toto téma obsahuje podrobné pokyny k vytváření, aktualizaci a odstraňování logických sítí po nasazení síťového adaptéru pomocí centra pro správu systému Windows. Logická síť softwarově definované sítě (SDN) je tradiční síť založená na síti VLAN.

Díky modelování sítě založené na síti VLAN jako logické sítě SDN můžete použít zásady sítě na úlohy, které jsou k těmto sítím připojené. Můžete například použít seznamy řízení přístupu (ACL) zabezpečení pro úlohy, které jsou připojené k logickým sítím SDN. Použití seznamů ACL chrání úlohy založené na síti VLAN z externích i interních útoků.

## <a name="create-a-logical-network"></a>Vytvoření logické sítě
Pomocí následujících kroků v centru pro správu systému Windows vytvořte logickou síť.

:::image type="content" source="./media/tenant-logical-networks/create-logical-network.png" alt-text="Snímek obrazovky centra pro správu systému Windows, která zobrazuje pole název logických sítí." lightbox="./media/tenant-logical-networks/create-logical-network.png":::

1. Na domovské obrazovce centra pro správu systému Windows pod položkou **všechna připojení** vyberte cluster, na kterém chcete vytvořit logickou síť.
1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **logické sítě**.
1. V části **logické sítě** vyberte kartu **inventář** a pak vyberte **Nová**.
1. V podokně **logické sítě** zadejte název logické sítě.
1. V části **logická podsíť** vyberte **Přidat**.

    :::image type="content" source="./media/tenant-logical-networks/create-logical-network-subnet.png" alt-text="Snímek obrazovky centra pro správu systému Windows, která ukazuje podokno logické podsítě." lightbox="./media/tenant-logical-networks/create-logical-network-subnet.png":::

1. V podokně **logická podsíť** zadejte název podsítě a zadejte následující informace:
    1. **ID sítě VLAN** pro síť.
    1. **Předpona adresy** v zápisu CIDR (Classless prosměrování mezi doménami).
    1. **Výchozí brána** pro síť.
    1. Adresa serveru **DNS** , v případě potřeby.
    1. Zaškrtněte políčko **Veřejná logická síť** , pokud chcete, aby logická síť poskytovala připojení externích klientů.
1. V části **fondy IP adres logické podsítě** vyberte **Přidat** a zadejte následující informace:
    1. Název logického **fondu IP adres**.
    1. **Počáteční IP adresa**.
    1. **Koncová IP adresa**. Počáteční a koncová IP adresa musí být v rámci předpony adresy zadané pro podsíť.
    1. Vyberte **Přidat**.
1. Na stránce **logická podsíť** vyberte **Přidat**.
1. Na stránce **logické sítě** vyberte **Odeslat**.
1. V seznamu **logické sítě** ověřte, zda je stav logické sítě v **pořádku**.

## <a name="get-a-list-of-logical-networks"></a>Získat seznam logických sítí
V clusteru můžete snadno zobrazit všechny logické sítě.

:::image type="content" source="./media/tenant-logical-networks/list-logical-networks.png" alt-text="Snímek obrazovky centra pro správu systému Windows, která ukazuje podokno inventáře logických sítí." lightbox="./media/tenant-logical-networks/list-logical-networks.png":::

1. Na domovské obrazovce centra pro správu systému Windows pod položkou **všechna připojení** vyberte cluster, na kterém chcete zobrazit logické sítě.
1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **logické sítě**.
1. Karta **inventář** obsahuje seznam všech logických sítí dostupných v clusteru a poskytuje příkazy pro správu jednotlivých logických sítí. Další možnosti:
    - Prohlédněte si seznam logických sítí.
    - Zobrazení nastavení logické sítě, stavu každé logické sítě a zda je povolena virtualizace sítě pro každou logickou síť. Pokud je povolena virtualizace sítě, můžete také zobrazit počet virtuálních sítí přidružených ke každé logické síti.
    - Změňte nastavení logické sítě.
    - Odstraňte logickou síť.

## <a name="view-logical-network-details"></a>Zobrazit podrobnosti logické sítě
Můžete zobrazit podrobné informace o konkrétní logické síti ze své vyhrazené stránky.

:::image type="content" source="./media/tenant-logical-networks/view-logical-network-details.png" alt-text="Snímek obrazovky centra pro správu systému Windows znázorňující zobrazení podrobností o logické síti." lightbox="./media/tenant-logical-networks/view-logical-network-details.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **logické sítě**.
1. Vyberte kartu **inventář** a potom vyberte logickou síť, pro kterou chcete zobrazit podrobnosti. Na následující stránce můžete:
    - Zobrazení stavu zřizování logické sítě (úspěšné, neúspěšné).
    - Zobrazení, zda je pro logickou síť povolena virtualizace sítě.
    - Zobrazte podsítě v logické síti.
    - Přidejte nové podsítě, odstraňte existující podsítě a upravte nastavení podsítě logické sítě.
    - Vyberte každou podsíť, kterou chcete přejít na stránku její **podsítě** , kde můžete přidávat, odebírat a upravovat fondy IP adres logické podsítě.
    - Zobrazit virtuální sítě a připojení přidružená k logické síti.

## <a name="change-a-logical-networks-virtualization-setting"></a>Změnit nastavení virtualizace logické sítě
Můžete změnit nastavení virtualizace sítě pro logickou síť.

:::image type="content" source="./media/tenant-logical-networks/change-logical-network-setting.png" alt-text="Snímek obrazovky centra pro správu systému Windows s možností zaškrtávací políčko Povolit virtualizaci sítě v logické síti." lightbox="./media/tenant-logical-networks/change-logical-network-setting.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **logické sítě**.
1. Vyberte kartu **inventář** , vyberte logickou síť a pak vyberte **Nastavení**.
1. Pokud plánujete nasadit virtuální sítě nad logickou sítí, v části název logické sítě zaškrtněte políčko **povolit virtualizaci sítě** a pak vyberte **Odeslat**.

## <a name="delete-a-logical-network"></a>Odstranění logické sítě
Logickou síť můžete odstranit, pokud ji už nepotřebujete.

:::image type="content" source="./media/tenant-logical-networks/delete-logical-network.png" alt-text="Snímek obrazovky centra pro správu systému Windows se zobrazením výzvy k potvrzení odstranění logické sítě" lightbox="./media/tenant-logical-networks/delete-logical-network.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **logické sítě**.
1. Vyberte kartu **inventář** , vyberte logickou síť a pak vyberte **Odstranit**.
1. Na příkazovém řádku pro potvrzení **odstranění** vyberte **Ano**.
1. Vedle pole hledání **logické sítě** vyberte možnost **aktualizovat** a ověřte, zda byla logická síť odstraněna.

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu také:
- [Správa virtuálních sítí tenantů](tenant-virtual-networks.md)
- [Softwarově definované sítě (SDN) v Azure Stack HCI](../concepts/software-defined-networking.md)
