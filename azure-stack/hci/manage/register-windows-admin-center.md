---
title: Registrace centra pro správu Windows pomocí Azure
description: Jak zaregistrovat bránu centra pro správu Windows v Azure
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/04/2021
ms.openlocfilehash: 93bba2336ab482e1a2bf0fc8e7545f537211382d
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116780"
---
# <a name="register-windows-admin-center-with-azure"></a>Registrace centra pro správu Windows pomocí Azure

> Platí pro Azure Stack HCL v20H2; Windows Server 2019

Pokud chcete používat služby Azure s centrem pro správu systému Windows, musíte nejdřív [nainstalovat centrum](/windows-server/manage/windows-admin-center/deploy/install) pro správu Windows na počítač pro správu a zaregistrovat bránu centra pro správu Windows. Je to předpoklad pro [registraci clusteru](../deploy/register-with-azure.md) v Azure.

   > [!IMPORTANT]
   > Zaregistrujte centrum pro správu Windows na stejném počítači pro správu, který plánujete použít k registraci vašich clusterů, a to pomocí stejného Azure Active Directory (ID tenanta) a ID aplikace.

## <a name="complete-the-registration-process"></a>Dokončení procesu registrace

1. Spusťte Centrum pro správu Windows a v pravém horním rohu klikněte na ikonu ozubeného kolečka pro **Nastavení** . tím přejdete na stránku svého účtu. Pak v nabídce **Brána** vlevo vyberte **Azure** a pak klikněte na **zaregistrovat**.

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="Vyberte nastavení > brána > Azure a pak klikněte na zaregistrovat." lightbox="media/register-wac/register-wac.png":::

2. Bude vám poskytnut jedinečný kód. Klikněte na tlačítko **Kopírovat** napravo od kódu. Klikněte na **Zadejte kód**, který otevře jiné okno prohlížeče, ve kterém můžete vložit kód zobrazený ve vaší aplikaci nebo zařízení.

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="Zkopírujte jedinečný kód, klikněte na zadat kód a vložte ho do dialogového okna." lightbox="media/register-wac/enter-code.png":::

3. Po vložení do kódu budete upozorněni, že se chystáte, abyste se přihlásili do centra pro správu Windows na vzdáleném zařízení nebo službě. Zadejte svůj e-mail nebo telefonní číslo. Pokud je vaše zařízení spravované, přejdete na přihlašovací stránku vaší organizace pro ověřování. Postupujte podle pokynů a zadejte příslušné přihlašovací údaje.

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="Přihlášení do centra pro správu Windows pomocí vašeho e-mailu nebo telefonního čísla" lightbox="media/register-wac/sign-in.png":::

   Měla by se zobrazit následující zpráva: "přihlásili jste se k aplikaci centra pro správu Windows na vašem zařízení." Zavřete okno prohlížeče a vraťte se na původní registrační stránku.

4. Připojte se k Azure Active Directory tím, že poskytnete ID vašeho Azure Active Directory (tenant) a ID aplikace. Pokud už máte ID tenanta Azure a následovali jste výše uvedené kroky, pole ID tenanta se může předem vyplněné a může obsahovat víc možností. Vyberte správné ID tenanta. Pokud vám správce Azure AD poskytl ID aplikace, klikněte na **použít existující** a zobrazí se prázdné pole, kde můžete zadat ID poskytnuté vaším správcem. Po zadání svého ID centrum pro správu systému Windows ověří, že se našel účet s tímto ID. Pokud máte existující ID, ale nevíte, co je, postupujte podle [těchto kroků](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a načtěte ho. Pokud vám vaše organizace neposkytla stávající ID, ponechte **Azure Active Directory aplikaci** nastavenou na **vytvořit novou**.

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="Připojte se k Azure Active Directory tím, že zadáte stávající ID Azure Active Directory (tenant) nebo vytvoříte nový." lightbox="media/register-wac/connect-to-aad.png":::

5. Kliknutím na tlačítko **připojit** se připojte k Azure. Pokud jste správce Azure AD nebo pokud jste použili existující ID aplikace, měli byste vidět potvrzení, že jste teď připojení ke službě Azure AD. Pokud ne, může se zobrazit zpráva, že potřebujete schválení správcem. Pokud se jedná o tento případ, vyberte možnost **vrátit se do aplikace bez udělení souhlasu** a obraťte se na správce služby Azure AD, aby udělil oprávnění k novému ID aplikace, které bylo vytvořeno při registraci, podle pokynů v kroku 6.

6. Pokud jste správce Azure AD, udělte oprávnění v Azure tak, že přejdete na **Azure Active Directory** a pak **Registrace aplikací**. Vyberte identitu aplikace pojmenovanou po registraci brány a přejděte na **oprávnění rozhraní API**. V části **souhlas udělení souhlasu** vyberte **udělit souhlas správce**.

7. Zavřete okno a přihlaste se k centru pro správu Windows pomocí svého účtu Azure.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni:

- [Připojení Azure Stack HCl k Azure](../deploy/register-with-azure.md)
- [Použití Azure Stack HCL s centrem pro správu Windows](../get-started.md)
- [Připojení k Azure Hybrid Services](/windows-server/manage/windows-admin-center/azure/)