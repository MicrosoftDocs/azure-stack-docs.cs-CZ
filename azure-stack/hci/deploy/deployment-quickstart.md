---
title: Rychlý Start k vytvoření Azure Stack clusteru HCI a jeho registraci v Azure
description: Naučte se nasazovat Azure Stack HCI, vytvořit cluster pomocí centra pro správu Windows a zaregistrovat ho v Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: a6e725eee638b0afd2dddbe2f382cade729f9873
ms.sourcegitcommit: d91d44762383790a0bcfc4a85f43050c8528d5d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97069780"
---
# <a name="quickstart-create-an-azure-stack-hci-cluster-and-register-it-with-azure"></a>Rychlý Start: Vytvoření clusteru Azure Stack HCI a jeho registrace do Azure

> Platí pro: Azure Stack HCI, verze 20H2

V tomto rychlém startu se dozvíte, jak nasadit cluster s jednou lokalitou Azure Stack HCI a zaregistrovat ho v Azure. Nasazení ve více lokalitách najdete v tématu [Přehled roztaženého clusteru](../concepts/stretched-clusters.md).

## <a name="before-you-start"></a>Než začnete

Před vytvořením clusteru udělejte toto:

* Zakupte dva servery z [katalogu Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/catalog/) přes preferovaný hardwarový partner Microsoftu s předinstalovaným operačním systémem Azure Stack HCI. Zkontrolujte [požadavky na systém](../concepts/system-requirements.md) , abyste se ujistili, že hardware, který vyberete, bude podporovat úlohy, které plánujete spustit v clusteru. Doporučujeme používat systém s vysokorychlostními síťovými adaptéry, které používají iWARP pro jednoduchou konfiguraci.
* Vytvořte uživatelský účet, který je členem místní skupiny Administrators na každém serveru.
* [Získejte předplatné Azure](https://azure.microsoft.com/), pokud ho ještě nemáte.
* [Nainstalujte si centrum](/windows-server/manage/windows-admin-center/deploy/install) pro správu Windows na počítač pro správu a [Zaregistrujte centrum pro správu Windows pomocí Azure](../manage/register-windows-admin-center.md). Všimněte si, že váš počítač pro správu musí být připojen ke stejné doméně služby Active Directory, ve které vytvoříte cluster, nebo plně důvěryhodnou doménu.
* Poznamenejte si názvy serverů, názvy domén, IP adresy a ID sítě VLAN pro vaše nasazení.

## <a name="create-the-cluster"></a>Vytvoření clusteru

Pomocí těchto kroků můžete vytvořit jednoduchý cluster s jedním uzlem a jediným webem. Další podrobnosti nebo vytvoření roztaženého clusteru najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí centra pro správu systému Windows](create-cluster.md).

1. V centru pro správu Windows v části **všechna připojení** klikněte na **Přidat**.
1. Na panelu **Přidat prostředky** v části **cluster Windows serveru** vyberte **vytvořit novou**.
1. V části **zvolit typ clusteru** vyberte **Azure Stack HCL**.
1. V části **Vybrat umístění serveru** vyberte **všechny servery v jedné lokalitě**.
1. Klikněte na **Vytvořit**. Nyní se zobrazí Průvodce vytvořením clusteru. Pokud se zobrazí automaticky otevírané okno **Zprostředkovatel zabezpečení přihlašovacích údajů (CredSSP)** , vyberte **Ano** a dočasně ho povolte. 

Průvodce vytvořením clusteru má pět oddílů, z nichž každý obsahuje několik kroků.

1. **Začněte.** V této části zkontrolujete požadavky, přidáte servery, připojíte se k doméně, nainstalujete požadované funkce a aktualizace a restartujete servery. 
2. **Sítě.** V této části Průvodce se ověří, že jsou povolené správné síťové adaptéry a že je zakázané, které nepoužíváte. Vyberte adaptéry pro správu, nastavte konfiguraci virtuálního přepínače a definujte síť tím, že zadáte IP adresy.
3. **Clustering.** V této části se ověří, že vaše servery mají konzistentní konfiguraci a jsou vhodné pro clusteringu, a vytvoří vlastní cluster.
4. **Pamì.** V dalším kroku vyčistíte a zkontrolujete jednotky, ověříte úložiště a povolíte Prostory úložiště s přímým přístupem.
5. **Sdn.** Můžete přeskočit oddíl 5, protože pro tento cluster nebudeme používat softwarově definované sítě (SDN).

Pokud jste v průvodci povolili protokol CredSSP, budete ho chtít pro účely zabezpečení zakázat na každém serveru.

1. V centru pro správu Windows v části **všechna připojení** vyberte cluster, který jste právě vytvořili.
1. V části **nástroje** vyberte **servery**.
1. V pravém podokně vyberte první server v clusteru.
1. V části **Přehled** vyberte **Zakázat CredSSP**. Uvidíte, že červená zpráva **CREDSSP povolená** v horní části zmizí.
1. Opakujte kroky 3 a 4 pro druhý server v clusteru.

## <a name="set-up-a-cluster-witness"></a>Nastavení určujícího clusteru

Nastavení prostředku určujícího zdroje je povinné, aby v případě, že bude jeden ze serverů v clusteru přepnut do režimu offline, nezpůsobí, že druhý uzel nebude k dispozici také. V tomto rychlém startu použijeme sdílenou složku SMB, která se nachází na jiném serveru jako určující. Můžete chtít použít cloudové sdílené složky Azure, pokud jsou všechny uzly serveru v clusteru spolehlivé připojení k Internetu. Další informace o možnostech určující sdílené složky najdete v tématu [Nastavení určujícího clusteru](witness.md).

1. V centru pro správu systému Windows vyberte ze šipky nahoru v horním rozevíracím seznamu položku **Správce clusteru** .
1. V části **připojení clusteru** vyberte cluster.
1. V části **nástroje** vyberte **Nastavení**.
1. V pravém podokně vyberte **určující**.
1. Jako **typ určujícího disku** vyberte možnost **určující sdílená složka**.
1. Zadejte cestu ke sdílené složce, například **\\ servername. domain. com\Witness $** , a v případě potřeby zadejte přihlašovací údaje.
1. Klikněte na **Uložit**.

## <a name="register-with-azure"></a>Registrace v Azure

Azure Stack HCI vyžaduje připojení k Azure a budete potřebovat Azure Active Directory oprávnění k dokončení registrace. Pokud je ještě nemáte, požádejte správce služby Azure AD, aby vám udělil oprávnění nebo je delegoval. Další informace najdete v tématu věnovaném [připojení Azure Stack HCl k Azure](register-with-azure.md) . Po registraci se cluster automaticky připojí na pozadí.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili cluster Azure Stack HCI a zaregistrovali ho v Azure. Nyní jste připraveni [vytvořit svazky](../manage/create-volumes.md) a pak [vytvořit virtuální počítače](../manage/vm.md).