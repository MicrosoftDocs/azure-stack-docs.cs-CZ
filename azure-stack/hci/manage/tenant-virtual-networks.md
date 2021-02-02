---
title: Správa virtuálních sítí tenantů
description: Toto téma obsahuje podrobné pokyny, jak pomocí centra pro správu systému Windows vytvářet, aktualizovat a odstraňovat virtuální sítě virtualizace sítě Hyper-V (HNV) po nasazení softwarově definované sítě (SDN).
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: 6af08615a25ed93f62f526d92ead7511699c8439
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248069"
---
# <a name="manage-tenant-virtual-networks"></a>Správa virtuálních sítí tenantů

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019; Windows Server 2016

Toto téma obsahuje podrobné pokyny, jak pomocí centra pro správu systému Windows vytvářet, aktualizovat a odstraňovat virtuální sítě virtualizace sítě Hyper-V (HNV) po nasazení softwarově definované sítě (SDN).

HNV pomáhá izolovat klientské sítě tak, aby každá síť tenanta byla samostatnou entitou. Každá entita nemá žádnou možnost vzájemného připojení, pokud nenastavíte buď úlohy veřejného přístupu, nebo partnerský vztah mezi virtuálními sítěmi.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě
K vytvoření virtuální sítě použijte následující postup v centru pro správu systému Windows.

:::image type="content" source="./media/tenant-virtual-networks/create-virtual-network.png" alt-text="Obrazovka domovské obrazovky centra pro správu systému Windows zobrazující podokno, ve kterém se má vytvořit název virtuální sítě a předpona adresy" lightbox="./media/tenant-virtual-networks/create-virtual-network.png":::

1. Na domovské obrazovce centra pro správu Windows pod položkou **všechna připojení** vyberte cluster, na kterém chcete vytvořit virtuální síť.
1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **virtuální sítě**.
1. V části **virtuální sítě** vyberte kartu **inventář** a pak vyberte **Nová**.
1. V podokně **virtuální sítě** zadejte název virtuální sítě.
1. V části **předpony adresy** vyberte **Přidat** a potom zadejte předponu adresy v zápisu CIDR (Classless prosměrování mezi doménami). Volitelně můžete přidat další předpony adres.
1. V části **podsítě** vyberte **Přidat**, zadejte název podsítě a pak zadejte předponu adresy v zápisu CIDR.

   >[!NOTE]
   > Předpona adresy podsítě musí spadat do rozsahu předpon adres, který jste definovali v **předponách adres** virtuální sítě.

1. Vyberte **Odeslat** nebo volitelně přidat další podsítě a pak vyberte **Odeslat**.
1. V seznamu **virtuální sítě** ověřte, zda je stav virtuální sítě v **pořádku**.

## <a name="get-a-list-of-virtual-networks"></a>Získat seznam virtuálních sítí
Ve vašem clusteru můžete snadno zobrazit všechny virtuální sítě.

:::image type="content" source="./media/tenant-virtual-networks/list-virtual-networks.png" alt-text="Snímek obrazovky centra pro správu systému Windows zobrazující seznam virtuálních sítí" lightbox="./media/tenant-virtual-networks/list-virtual-networks.png":::

1. Na domovské obrazovce centra pro správu systému Windows pod položkou **všechna připojení** vyberte cluster, na kterém chcete zobrazit virtuální sítě.
1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **virtuální sítě**.
1. Karta **inventář** uvádí všechny virtuální sítě dostupné v clusteru a poskytuje příkazy pro správu jednotlivých virtuálních sítí. Další možnosti:
    - Zobrazit seznam virtuálních sítí.
    - Zobrazení nastavení virtuální sítě, stavu každé virtuální sítě a počtu virtuálních počítačů připojených k jednotlivým virtuálním sítím.
    - Změnit nastavení virtuální sítě.
    - Odstraní virtuální síť.

## <a name="view-virtual-network-details"></a>Zobrazit podrobnosti virtuální sítě
Můžete zobrazit podrobné informace o konkrétní virtuální síti ze své vyhrazené stránky.

:::image type="content" source="./media/tenant-virtual-networks/view-virtual-network-details.png" alt-text="Snímek obrazovky centra pro správu systému Windows znázorňující zobrazení podrobností o virtuální síti" lightbox="./media/tenant-virtual-networks/view-virtual-network-details.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **virtuální sítě**.
1. Vyberte kartu **inventář** a potom vyberte virtuální síť, pro kterou chcete zobrazit podrobnosti. Na následující stránce můžete:
    - Zobrazení **stavu zřizování** virtuální sítě (úspěšné, neúspěšné)
    - Zobrazit **stav konfigurace** virtuální sítě (v pořádku, chyba, upozornění, neznámá).
    - Zobrazit základní **logickou síť** virtuální sítě.
    - Zobrazení **adresního prostoru** virtuální sítě.
    - Přidejte nové podsítě, odstraňte existující podsítě a upravte nastavení podsítě virtuální sítě.
    - Zobrazit **připojení virtuálního počítače** k virtuální síti.

## <a name="change-virtual-network-settings"></a>Změnit nastavení virtuální sítě
Můžete aktualizovat předpony adres virtuální sítě, spravovat partnerský vztah virtuálních sítí a nakonfigurovat směrovač Border Gateway Protocol (BGP) a partnerské vztahy pro virtuální síť.

:::image type="content" source="./media/tenant-virtual-networks/change-virtual-network-settings.png" alt-text="Snímek obrazovky centra pro správu systému Windows zobrazující zobrazení nastavení virtuální sítě." lightbox="./media/tenant-virtual-networks/change-virtual-network-settings.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **virtuální sítě**.
1. Vyberte kartu **inventář** , vyberte virtuální síť a pak vyberte **Nastavení**.
1. Na kartě **Obecné** můžete:
    - Odeberte existující předpony adres nebo přidejte nové.
    - Nakonfigurujte partnerský vztah s jinou virtuální sítí.
    - Přidejte do virtuální sítě směrovač protokolu BGP. K tomu je potřeba zadat název směrovače BGP a číslo ASN (autonomního systému).
    - Přidejte jeden nebo více partnerských uzlů protokolu BGP pro směrovač protokolu BGP. K tomu je potřeba zadat každý partnerský název protokolu BGP a číslo ASN pro každého partnerského uzlu protokolu BGP.

## <a name="delete-a-virtual-network"></a>Odstranění virtuální sítě
Virtuální síť můžete odstranit, pokud ji už nepotřebujete.

:::image type="content" source="./media/tenant-virtual-networks/delete-virtual-network.png" alt-text="Snímek obrazovky centra pro správu systému Windows se zobrazením výzvy k potvrzení odstranění virtuální sítě" lightbox="./media/tenant-virtual-networks/delete-virtual-network.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **virtuální sítě**.
1. Vyberte kartu **inventář** , vyberte virtuální síť a pak vyberte **Odstranit**.
1. Na příkazovém řádku pro potvrzení **odstranění virtuální sítě** vyberte **Odstranit**.
1. Vedle vyhledávacího pole virtuální sítě vyberte **aktualizovat** , aby se zajistilo odstranění virtuální sítě.

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu také:
- [Softwarově definované sítě (SDN) v Azure Stack HCI](../concepts/software-defined-networking.md)
