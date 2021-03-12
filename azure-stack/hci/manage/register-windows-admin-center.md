---
title: Registrace centra pro správu Windows pomocí Azure
description: Jak zaregistrovat bránu centra pro správu Windows v Azure
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/10/2021
ms.openlocfilehash: 79b90f028c61319535a7f5ddacdbd7cb1cf25081
ms.sourcegitcommit: e7d6f953e7014900b4e7d710340cfa98d253fce9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102637625"
---
# <a name="register-windows-admin-center-with-azure"></a>Registrace centra pro správu Windows pomocí Azure

> Platí pro Azure Stack HCL verze 20H2; Windows Server 2019

Pokud chcete používat služby Azure s centrem pro správu systému Windows, musíte nejdřív [nainstalovat centrum](/windows-server/manage/windows-admin-center/deploy/install) pro správu Windows na počítač pro správu a zaregistrovat bránu centra pro správu Windows. Je to předpoklad pro [registraci clusteru](../deploy/register-with-azure.md) v Azure.

   > [!IMPORTANT]
   > Aby bylo možné použít Centrum pro správu systému Windows k registraci Azure Stack clustery HCI, musí být brána centra pro správu systému Windows zaregistrována do ID aplikace Azure Active Directory (Azure AD), které je schváleno správcem Azure AD vaší organizace. Zaregistrujte centrum pro správu systému Windows na stejném počítači pro správu, který chcete použít k registraci clusterů, a to pomocí stejného Azure Active Directory (tenant) ID.

## <a name="complete-the-registration-process"></a>Dokončení procesu registrace

1. Spusťte Centrum pro správu Windows a v pravém horním rohu vyberte ikonu ozubeného kolečka pro **Nastavení** , která vás převezme na stránku svého účtu. Pak v nabídce **Brána** vlevo vyberte **Azure** a pak klikněte na **zaregistrovat**.

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="Vyberte nastavení > brána > Azure a pak klikněte na zaregistrovat." lightbox="media/register-wac/register-wac.png":::

2. Bude vám poskytnut jedinečný kód. Klikněte na tlačítko **Kopírovat** napravo od kódu. Klikněte na **Zadejte kód**, který otevře jiné okno prohlížeče, ve kterém můžete vložit kód zobrazený ve vaší aplikaci nebo zařízení.

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="Zkopírujte jedinečný kód, klikněte na zadat kód a vložte ho do dialogového okna." lightbox="media/register-wac/enter-code.png":::

3. Po vložení do kódu budete upozorněni, že se chystáte, abyste se přihlásili do centra pro správu Windows na vzdáleném zařízení nebo službě. Zadejte svůj e-mail nebo telefonní číslo. Pokud je vaše zařízení spravované, přejdete na přihlašovací stránku vaší organizace pro ověřování. Postupujte podle pokynů a zadejte příslušné přihlašovací údaje.

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="Přihlášení do centra pro správu Windows pomocí vašeho e-mailu nebo telefonního čísla" lightbox="media/register-wac/sign-in.png":::

   Měla by se zobrazit následující zpráva: "přihlásili jste se k aplikaci centra pro správu Windows na vašem zařízení." Zavřete okno prohlížeče a vraťte se na původní registrační stránku.

4. Připojte se k Azure Active Directory tím, že poskytnete ID vašeho Azure Active Directory (tenant) a ID aplikace. Pokud už máte ID tenanta Azure a následovali jste výše uvedené kroky, pole ID tenanta se může předem vyplněné a může obsahovat víc možností. Vyberte správné ID tenanta. 

   Pokud vám správce Azure AD poskytl ID aplikace, klikněte na **použít existující** a zobrazí se prázdné pole, kde můžete zadat ID poskytnuté vaším správcem. Po zadání svého ID centrum pro správu systému Windows ověří, že se našel účet s tímto ID. Pokud máte existující ID, ale nevíte, co je, postupujte podle [těchto kroků](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) a načtěte ho. Pokud vám vaše organizace neposkytla stávající ID, ponechte **Azure Active Directory aplikaci** nastavenou na **vytvořit novou**.

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="Připojte se k Azure Active Directory tím, že zadáte stávající ID Azure Active Directory (tenant) a ID aplikace nebo vytvoříte nové ID aplikace." lightbox="media/register-wac/connect-to-aad.png":::

5. Klikněte na **Připojit**. Pokud jste správce Azure AD nebo pokud jste použili existující ID aplikace, měli byste vidět potvrzení, že jste teď připojení ke službě Azure AD. Pokud ne, může se zobrazit zpráva, že potřebujete schválení správcem. Pokud se jedná o tento případ, vyberte možnost **vrátit se do aplikace bez udělení souhlasu** a požádejte správce Azure AD, aby udělil souhlas novému ID aplikace, který byl vytvořen po registraci, podle pokynů v kroku 6 níže.

6. Pokud jste správce Azure AD, udělte oprávnění v Azure tak, že přejdete na **Azure Active Directory** a pak **Registrace aplikací**. Vyberte **všechny aplikace** a vyhledejte **WindowsAdminCenter**. Vyberte zobrazovaný název brány, kterou registrujete. Poznamenejte si **ID aplikace (klienta)** zobrazené v horní části stránky, protože ji budete muset poskytnout uživateli. Potom přejděte na **oprávnění rozhraní API**. V části **souhlas udělení souhlasu** vyberte **udělit souhlas správce**. Pak ID aplikace přidělte uživateli. Pokud máte v úmyslu použít stejné ID aplikace pro více uživatelů, přejděte ke kroku 7; v opačném případě přejděte k kroku 8.

7. V zájmu usnadnění správy je možné v organizaci povolit více uživatelů k registraci centra pro správu Windows pomocí stejného ID aplikace Azure. Aby to bylo možné, všichni uživatelé musí zaregistrovat své brány do stejné domény a portu, například *https://localhost:6516* . To také vyžaduje, aby správce Azure AD měl krok navíc a přidal do Azure Portal identifikátory URI pro přesměrování.

   V centru pro správu Windows v pravém horním rohu vyberte ikonu ozubeného kola **Nastavení** . Pak v nabídce **Brána** vlevo vyberte **Azure** a pak klikněte na **Zobrazit v Azure, ve** kterém se zobrazí podrobnosti o Azure AD. V Azure Portal v nabídce na levé straně vyberte **spravovat > ověřování** . V poli **URI pro přesměrování** se zobrazí existující identifikátor URI, který představuje první bránu, která se zaregistruje do ID aplikace. Vyberte **Přidat identifikátor URI** a přidejte dva nové identifikátory URI přesměrování, například *http://localhost:6516* a *https://localhost:6516* .

   :::image type="content" source="media/register-wac/add-redirect-uris.png" alt-text="Pokud chcete povolit více uživatelům v organizaci registraci centra pro správu Windows pomocí stejného ID aplikace Azure, přidejte identifikátory URI pro přesměrování." lightbox="media/register-wac/add-redirect-uris.png":::

   > [!IMPORTANT]
   > Pokud správce Azure AD nepřidá identifikátory URI pro přesměrování a více než jeden uživatel se pokusí zaregistrovat centrum pro správu Windows do stejného ID aplikace, zobrazí se chyba, že se adresa URL odpovědi neshoduje.

7. V tomto okamžiku musí uživatel znovu spustit Průvodce registrací, tentokrát s výběrem možnosti **použít existující** ID aplikace a zadáním ID aplikace, které poskytuje správce Azure AD.

8. Vyberte **Přihlásit** se a přihlaste se k centru pro správu Windows pomocí svého účtu Azure.

## <a name="unregister-windows-admin-center"></a>Zrušit registraci centra pro správu Windows

Pokud chcete zrušit registraci brány centra pro správu Windows, vyberte v pravém horním rohu ikonu **Nastavení** ozubeného kolečka. Pak v nabídce **Brána** vlevo vyberte **Azure** a pak zrušte **registraci** a pak **potvrďte**. 

Tím se odebere přidružení mezi centrem pro správu Windows a ID aplikace Azure AD, které vám poskytl správce Azure AD nebo když jste zaregistrovali bránu. Neodstraní aplikaci Azure AD ani neovlivní žádné služby Azure používané servery nebo clustery spravovanými centrem pro správu systému Windows.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni:

- [Připojení Azure Stack HCl k Azure](../deploy/register-with-azure.md)
- [Použití Azure Stack HCL s centrem pro správu Windows](../get-started.md)
- [Připojení k Azure Hybrid Services](/windows-server/manage/windows-admin-center/azure/)