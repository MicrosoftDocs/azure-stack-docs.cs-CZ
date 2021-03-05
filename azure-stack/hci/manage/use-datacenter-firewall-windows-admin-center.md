---
title: Použití brány firewall Datacenter ke konfiguraci seznamů ACL pomocí centra pro správu Windows
description: Toto téma obsahuje pokyny k vytvoření a konfiguraci seznamů řízení přístupu (ACL) pro správu toku dat pomocí brány firewall Datacenter a seznamů ACL v logických a virtuálních sítích softwarově definované sítě (SDN).
author: AnirbanPaul
ms.author: anpaul
ms.topic: how-to
ms.date: 03/04/2021
ms.openlocfilehash: 342f2771d74a2347cd53fa9364089a934885c671
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102194231"
---
# <a name="use-datacenter-firewall-to-configure-acls-with-windows-admin-center"></a>Použití brány firewall Datacenter ke konfiguraci seznamů ACL pomocí centra pro správu Windows

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019, Windows Server 2016

V tomto tématu najdete podrobné pokyny k vytvoření a konfiguraci seznamů řízení přístupu (ACL) pro správu toku přenosů dat pomocí brány firewall datového centra. Poskytuje taky pokyny ke správě seznamů ACL v logických a virtuálních sítích softwarově definované sítě (SDN). Povolíte a nakonfigurujete bránu firewall Datacenter vytvořením seznamů ACL a jejich použitím v podsíti nebo síťovém rozhraní. Další informace najdete v tématu [co je datacentrum firewall?](../concepts/datacenter-firewall-overview.md) Pokud chcete použít skripty PowerShellu, přečtěte si téma [použití brány firewall Datacenter ke konfiguraci seznamů ACL pomocí PowerShellu](use-datacenter-firewall-powershell.md).

Před konfigurací seznamů řízení přístupu (ACL) musíte nasadit síťový adaptér. Další informace o síťovém adaptéru najdete v tématu [co je to síťový adaptér?](../concepts/network-controller-overview.md) . Informace o nasazení síťového adaptéru pomocí skriptů PowerShellu najdete v tématu [nasazení infrastruktury SDN](sdn-express.md).

Pokud navíc chcete použít seznamy ACL pro logickou síť SDN, je nutné nejprve vytvořit logickou síť. Podobně pokud chcete použít seznamy ACL pro virtuální síť SDN, musíte nejdřív vytvořit virtuální síť. Další informace najdete v následujících tématech:
- [Správa logických sítí tenanta](tenant-logical-networks.md)
- [Správa virtuálních sítí tenantů](tenant-virtual-networks.md)

## <a name="create-an-acl"></a>Vytvoření seznamu ACL
Seznam ACL můžete snadno vytvořit v centru pro správu systému Windows.

:::image type="content" source="./media/access-control-lists/create-acl.png" alt-text="Snímek obrazovky centra pro správu systému Windows, která zobrazuje pole název seznamu Access Control." lightbox="./media/access-control-lists/create-acl.png":::

1. Na domovské obrazovce centra pro správu Windows pod položkou **všechna připojení** vyberte cluster, na kterém chcete vytvořit seznam řízení přístupu (ACL).
1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **seznamy řízení přístupu**.
1. V části **seznamy řízení přístupu** vyberte kartu **inventář** a pak vyberte **Nová**.
1. V podokně se **seznamem Access Control** zadejte název seznamu ACL a pak vyberte **Odeslat**.
1. V části **seznamy řízení přístupu** ověřte, že **stav zřizování** nového seznamu ACL se **úspěšně** zobrazuje.

## <a name="create-acl-rules"></a>Vytvoření pravidel seznamu ACL
Až vytvoříte seznam řízení přístupu, budete připraveni vytvořit pravidla seznamů ACL. Pokud chcete použít pravidla seznamu ACL u příchozího i odchozího provozu, musíte vytvořit dvě pravidla.

:::image type="content" source="./media/access-control-lists/create-acl-rules.png" alt-text="Snímek obrazovky centra pro správu systému Windows zobrazující podokno pravidla Access Control." lightbox="./media/access-control-lists/create-acl-rules.png":::

1. Na domovské obrazovce centra pro správu Windows pod položkou **všechna připojení** vyberte cluster, na kterém chcete vytvořit seznam řízení přístupu (ACL).
1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **seznamy řízení přístupu**.
1. V části **seznamy řízení přístupu** vyberte kartu **inventář** a potom vyberte seznam ACL, který jste právě vytvořili.
1. V části **Access Control pravidlo** vyberte **Nový**.
1. V podokně **Access Control pravidlo** zadejte následující informace:
    1. **Název** pravidla
    1. **Priorita** pravidla – přijatelné hodnoty jsou **101** až **65000**. Nižší hodnota znamená vyšší prioritu.
    1. **Typy** – může to být příchozí nebo odchozí.
    1. **Protokol** – zadejte protokol, který bude odpovídat příchozímu nebo odchozímu paketu. Přijatelné hodnoty jsou **všechny**, **TCP** a **UDP**.
    1. **Předpona zdrojové adresy** – zadejte předponu zdrojové adresy, která bude odpovídat příchozímu nebo odchozímu paketu. Pokud zadáte *, znamená to, že označuje všechny zdrojové adresy.
    1. **Rozsah zdrojového portu** – zadejte rozsah zdrojových portů, který bude odpovídat příchozímu nebo odchozímu paketu. Pokud zadáte *, znamená to, že označuje všechny zdrojové porty.
    1. **Předpona cílové adresy** – zadejte předponu cílové adresy, která bude odpovídat příchozímu nebo odchozímu paketu. Pokud zadáte *, znamená to, že označuje všechny cílové adresy.
    1. **Rozsah cílových portů** – určete rozsah cílových portů, který bude odpovídat příchozímu nebo odchozímu paketu. Pokud zadáte *, znamená to, že označuje všechny cílové porty.
    1. **Akce** – Pokud se shodují výše uvedené podmínky, určete, jestli se má paket použít, nebo zablokovat. Přijatelné hodnoty jsou **Allow** a **Deny**.
    1. **Protokolování** – zadejte, pokud chcete povolit nebo zakázat protokolování pro pravidlo. Pokud je povolené protokolování, veškerý provoz odpovídající tomuto pravidlu se zaznamená do protokolu na hostitelských počítačích.
1. Vyberte **Odeslat**.

## <a name="apply-an-acl-to-a-virtual-network"></a>Použití seznamu ACL pro virtuální síť
Až vytvoříte seznam ACL a pravidla, budete je muset použít v podsíti virtuální sítě, podsíti logické sítě nebo síťovém rozhraní.

:::image type="content" source="./media/access-control-lists/apply-acl-virtual-network.png" alt-text="Snímek obrazovky centra pro správu systému Windows zobrazující podokno virtuální podsíť" lightbox="./media/access-control-lists/apply-acl-virtual-network.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **virtuální sítě**.
1. Vyberte kartu **inventář** a potom vyberte virtuální síť. Na další stránce vyberte podsíť virtuální sítě a pak vyberte **Nastavení**.
1. V rozevíracím seznamu vyberte seznam ACL a pak vyberte **Odeslat**.

    Po dokončení posledního kroku přidružíte seznam ACL k podsíti virtuální sítě a použijete ho pro všechny počítače připojené k podsíti virtuální sítě.

## <a name="apply-an-acl-to-a-network-interface"></a>Použití seznamu ACL pro síťové rozhraní
Seznam ACL můžete použít pro síťové rozhraní, a to buď při vytváření virtuálního počítače, nebo později.

:::image type="content" source="./media/access-control-lists/apply-acl-network-interface.png" alt-text="Snímek obrazovky centra pro správu systému Windows s možností nastavení sítě, která přidruží seznam řízení přístupu k síťovému rozhraní." lightbox="./media/access-control-lists/apply-acl-network-interface.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **virtuální počítače**.
1. Vyberte kartu **inventář** , vyberte virtuální počítač a pak vyberte **Nastavení**.
1. Na stránce **Nastavení** vyberte **sítě**.
1. Přejděte dolů na **seznam řízení přístupu**, rozbalte rozevírací seznam, vyberte seznam ACL a vyberte **Uložit nastavení sítě**.

    Po dokončení posledního kroku přidružíte seznam řízení přístupu k síťovému rozhraní a použijete ho pro všechny příchozí a odchozí přenosy pro síťové rozhraní.

## <a name="get-a-list-of-acls"></a>Získat seznam seznamů ACL
V seznamu můžete snadno zobrazit všechny seznamy ACL v clusteru.

:::image type="content" source="./media/access-control-lists/get-acl-list.png" alt-text="Snímek obrazovky centra pro správu systému Windows zobrazující seznam seznamů ACL na kartě inventáře." lightbox="./media/access-control-lists/get-acl-list.png":::

1. Na domovské obrazovce centra pro správu systému Windows pod položkou **všechna připojení** vyberte cluster, na kterém chcete zobrazit seznam seznamů ACL.
1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **seznamy řízení přístupu**.
1. Karta **inventář** zobrazuje seznam seznamů ACL dostupných v clusteru a poskytuje příkazy, které můžete použít ke správě jednotlivých seznamů ACL v seznamu. Další možnosti:
    - Zobrazte seznam ACL.
    - Zobrazit počet pravidel pro každý seznam ACL a počet použitých podsítí a síťových adaptérů použitých u každého seznamu ACL.
    - Zobrazení **stavu zřizování** jednotlivých seznamů ACL (**úspěšných**, **neúspěšných**)
    - Odstraní seznam ACL.
    - Pokud vyberete seznam řízení přístupu (ACL) v seznamu, můžete zobrazit jeho pravidla. Pak můžete přidat, odstranit nebo upravit nastavení pravidla seznamu ACL.

## <a name="delete-an-acl"></a>Odstranit seznam řízení přístupu
Seznam ACL můžete odstranit, pokud ho už nepotřebujete.

>[!NOTE]
> Po odstranění seznamu ACL ze seznamu seznamů ACL se ujistěte, že není přidružený k podsíti nebo síťovému rozhraní.

:::image type="content" source="./media/access-control-lists/delete-acl.png" alt-text="Snímek obrazovky centra pro správu systému Windows se zobrazením výzvy k potvrzení odstranění seznamu ACL" lightbox="./media/access-control-lists/delete-acl.png":::

1. V části **nástroje** přejděte dolů k oblasti **síť** a vyberte **seznamy řízení přístupu**.
1. Vyberte kartu **inventář** , v seznamu vyberte seznam ACL a pak vyberte **Odstranit**.
1. Na výzvu k **odstranění potvrzení** vyberte **Ano**.
1. Vedle vyhledávacího pole vyberte **aktualizovat** a ujistěte se, že seznam ACL byl odstraněn.

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu také:
- [Softwarově definované sítě (SDN) v Azure Stack HCI a Windows Server](../concepts/software-defined-networking.md)
