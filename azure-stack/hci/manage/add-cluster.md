---
title: Přidání nebo odebrání serverů pro Azure Stack clusteru HCI
description: Naučte se přidávat nebo odebírat uzly serveru z clusteru v Azure Stack HCI.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 07/21/2020
ms.openlocfilehash: 9dfdbcab43694146c4190db8ef29905626a4d597
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866640"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Přidání nebo odebrání serverů pro Azure Stack clusteru HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Můžete snadno přidat nebo odebrat servery z clusteru v Azure Stack HCI. Mějte na paměti, že každý nový fyzický server musí přesně odpovídat zbývajícím serverům v clusteru, když přichází do typu procesoru, paměti, počtu jednotek a typu a velikosti jednotek.

Kdykoli přidáte nebo odeberete server, musíte také provést ověření clusteru, abyste zajistili, že cluster funguje normálně.

## <a name="obtain-oem-hardware"></a>Získat hardware výrobce OEM

Prvním krokem je získání nového hardwaru HCL od původního výrobce OEM. Pokud přidáváte nový serverový hardware pro použití ve vašem clusteru, vždy si přečtěte dokumentaci k poskytované výrobci OEM.

1. Umístěte nový fyzický server do stojanu a zapojte ho správně.
1. Povolte porty fyzických přepínačů a v případě potřeby upravte seznamy řízení přístupu (ACL) a identifikátory sítě VLAN.
1. Nakonfigurujte správnou IP adresu v řadiči pro správu základní desky (BMC) a použijte všechna nastavení systému BIOS podle pokynů výrobce OEM.
1. Použijte aktuální standardní hodnotu firmwaru pro všechny komponenty pomocí nástrojů poskytovaných výrobcem OEM.
1. Spusťte ověřovací testy OEM, abyste zajistili stejnorodost s existujícími servery clusteru.

## <a name="add-a-server-to-the-cluster"></a>Přidání serveru do clusteru

Po správném vystavení serveru použijte centrum pro správu systému Windows k připojení serveru k vašemu clusteru.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Obrazovka Přidat server" lightbox="media/manage-cluster/add-server.png":::

1. V **centru pro správu systému Windows**vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **připojení clusteru**vyberte cluster.
1. V části **nástroje**vyberte **servery**.
1. V části **servery**vyberte kartu **inventář** .
1. Na kartě **inventář** vyberte **Přidat**.
1. Do pole **název serveru**zadejte plně kvalifikovaný název domény serveru, který chcete přidat, klikněte na **Přidat**a potom v dolní části klikněte na **Přidat** znovu.
1. Ověřte, že se server úspěšně přidal do vašeho clusteru.

## <a name="remove-a-server-from-the-cluster"></a>Odebrání serveru z clusteru

Postup odebrání serveru z clusteru je podobný jako při přidávání serveru do clusteru.

Pamatujte, že když odeberete server, odeberete také všechny virtuální počítače, jednotky a úlohy, které jsou přidruženy k serveru.

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Dialogové okno odebrat server" lightbox="media/manage-cluster/remove-server.png":::

1. V **centru pro správu systému Windows**vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **připojení clusteru**vyberte cluster.
1. V části **nástroje**vyberte **servery**.
1. V části **servery**vyberte kartu **inventář** .
1. Na kartě **inventář** vyberte server, který chcete odebrat, a pak vyberte **Odebrat**.
1. Pokud chcete odebrat i jakékoli serverové jednotky z fondu úložiště, povolte toto políčko.
1. Ověřte, že se server úspěšně odebral z clusteru.

Kdykoli přidáte nebo odeberete serverový uzel z clusteru, ujistěte se, že a následně spustíte test ověření clusteru.

## <a name="next-steps"></a>Další kroky

- Po přidání nebo odebrání uzlu byste měli cluster ověřit. Další informace najdete v tématu [ověření clusteru](../deploy/validate.md) .