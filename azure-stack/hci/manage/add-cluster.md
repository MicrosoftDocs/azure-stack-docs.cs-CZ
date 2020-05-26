---
title: Přidání nebo odebrání serverů pro Azure Stack clusteru HCI
description: Naučte se přidávat nebo odebírat uzly serveru z clusteru v Azure Stack HCI.
ms.topic: article
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 05/20/2020
ms.openlocfilehash: 4d8e122c8a0b68740ecf34f8140322ee627d03e7
ms.sourcegitcommit: 91f4baa7a770b7a82f1ddccec4dbac14be530d06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83768984"
---
# <a name="add-or-remove-servers-for-an-azure-stack-hci-cluster"></a>Přidání nebo odebrání serverů pro Azure Stack clusteru HCI

> Platí pro: Windows Server 2019

Můžete snadno přidat nebo odebrat servery z clusteru v Azure Stack HCI. Mějte na paměti, že každý nový fyzický server musí přesně odpovídat zbývajícím serverům v clusteru, když přichází do typu procesoru, paměti, počtu jednotek a typu a velikosti jednotek.

Kdykoli přidáte nebo odeberete server, musíte také provést ověření clusteru, abyste zajistili, že cluster funguje normálně.

## <a name="obtain-server-hardware-from-your-oem"></a>Získání hardwarového serveru od výrobce OEM

Prvním krokem je získání nového hardwaru HCL od původního výrobce OEM. Pokud přidáváte nový serverový hardware pro použití ve vašem clusteru, vždy si přečtěte dokumentaci k poskytované výrobci OEM.

1. Umístěte nový fyzický server do stojanu a zapojte ho správně.
1. Povolte porty fyzických přepínačů a v případě potřeby upravte seznamy řízení přístupu (ACL) a identifikátory sítě VLAN.
1. Nakonfigurujte správnou IP adresu v řadiči pro správu základní desky (BMC) a použijte všechna nastavení systému BIOS podle pokynů výrobce OEM.
1. Použijte aktuální standardní hodnotu firmwaru pro všechny komponenty pomocí nástrojů poskytovaných výrobcem OEM.
1. Spusťte ověřovací testy OEM, abyste zajistili stejnorodost s existujícími servery clusteru.

## <a name="add-the-server-to-the-cluster"></a>Přidání serveru do clusteru

Po správném vystavení serveru použijte centrum pro správu systému Windows k připojení serveru k vašemu clusteru.

:::image type="content" source="media/manage-cluster/add-server.png" alt-text="Obrazovka Přidat server":::

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

:::image type="content" source="media/manage-cluster/remove-server.png" alt-text="Dialogové okno odebrat server":::

1. V **centru pro správu systému Windows**vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **připojení clusteru**vyberte cluster.
1. V části **nástroje**vyberte **servery**.
1. V části **servery**vyberte kartu **inventář** .
1. Na kartě **inventář** vyberte server, který chcete odebrat, a pak vyberte **Odebrat**.
1. Pokud chcete odebrat i jakékoli serverové jednotky z fondu úložiště, povolte toto políčko.
1. Ověřte, že se server úspěšně odebral z clusteru.

## <a name="validate-the-cluster"></a>Ověřit cluster

Kdykoli přidáte nebo odeberete server z clusteru, musíte ověřit cluster. Ověřování clusteru musí být před tím, než je společnost Microsoft podporováno, bez chyb.

:::image type="content" source="media//manage-cluster/validate-cluster.png" alt-text="Dialog ověřit cluster":::

1. V **centru pro správu systému Windows**vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **nástroje**vyberte **servery**.
1. V části **servery**vyberte kartu **inventář** , klikněte na **Další**a pak vyberte **ověřit cluster**.
1. Ověřte, zda byly všechny kroky ověření úspěšně úspěšné.

## <a name="next-steps"></a>Další kroky

- Další informace o ověření clusteru najdete v tématu [Ověření hardwaru pro cluster s podporou převzetí služeb při selhání](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134244(v=ws.11)).