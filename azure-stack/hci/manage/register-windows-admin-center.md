---
title: Registrace centra pro správu Windows pomocí Azure
description: Postup registrace centra pro správu Windows pomocí Azure
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: ee6794c0798bc388bc3d9313665eb0b7917cf8eb
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867202"
---
# <a name="register-windows-admin-center-with-azure"></a>Registrace centra pro správu Windows pomocí Azure

> Platí pro Azure Stack HCL v20H2; Windows Server 2019

Pokud chcete používat služby Azure s centrem pro správu systému Windows, musíte nejdřív nainstalovat centrum pro správu Windows na počítač pro správu a dokončit jednorázovou registraci.

## <a name="complete-the-registration-process-in-windows-admin-center"></a>Dokončení procesu registrace v centru pro správu Windows

1. Spusťte Centrum pro správu Windows a v pravém horním rohu klikněte na ikonu ozubeného kolečka pro **Nastavení** . tím přejdete na stránku svého účtu. Pak v nabídce **Brána** vlevo vyberte **Azure**a pak klikněte na **zaregistrovat**.
1. Bude vám poskytnut jedinečný kód. Klikněte na tlačítko **Kopírovat** napravo od kódu.
1. Klikněte na **Zadejte kód**, který otevře jiné okno prohlížeče, ve kterém můžete vložit kód zobrazený ve vaší aplikaci nebo zařízení.
1. Po vložení do kódu budete upozorněni, že se chystáte, abyste se přihlásili do centra pro správu Windows na vzdáleném zařízení nebo službě. 
1. Zadejte svůj e-mail nebo telefonní číslo. Pokud je vaše zařízení spravované, přejdete na přihlašovací stránku vaší organizace pro ověřování. Postupujte podle pokynů a zadejte příslušné přihlašovací údaje.
1. Měla by se zobrazit následující zpráva: "přihlásili jste se k aplikaci centra pro správu Windows na vašem zařízení." Zavřete okno prohlížeče a vraťte se na původní registrační stránku.
1. Připojte se k Azure Active Directory tím, že poskytnete ID vašeho Azure Active Directory (tenant). Pokud jste postupovali podle předchozích kroků, pole ID bude předem vyplněné. Pokud vám vaše organizace neposkytla existující ID, ponechte **Azure Active Directory aplikaci** nastavenou na **vytvořit novou** . Pokud už ID máte, klikněte na **použít existující**a zobrazí se prázdné pole, kde můžete zadat ID poskytnuté vaším správcem. Po zadání svého ID centrum pro správu systému Windows ověří, že se našel účet s tímto ID. Pokud máte existující ID, ale nevíte, co je, postupujte podle [těchto kroků](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a načtěte ho.
1. Kliknutím na tlačítko **připojit** se připojte k Azure. Mělo by se zobrazit potvrzení, že jste teď připojení ke službě Azure AD.
1. V Azure udělte oprávnění **aplikace** v Azure Portal. V části **souhlas udělení souhlasu**vyberte **udělit souhlas správce**.
1. Zavřete okno a přihlaste se k centru pro správu Windows pomocí svého účtu Azure.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni:

- [Použití Azure Stack HCL s centrem pro správu Windows](../get-started.md)
- [Připojení k Azure Hybrid Services](/windows-server/manage/windows-admin-center/azure/)