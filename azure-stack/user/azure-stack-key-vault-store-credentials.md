---
title: Ukládat přihlašovací údaje instančního objektu ve službě Azure Stack hub Key Vault
description: Informace o tom, jak Key Vault ukládá přihlašovací údaje instančního objektu do centra Azure Stack
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.lastreviewed: 01/16/2020
ms.openlocfilehash: c17ba69fedfd51681c5038efde591115ce54bddf
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86567514"
---
# <a name="store-service-principal-credentials-in-azure-stack-hub-key-vault"></a>Ukládat přihlašovací údaje instančního objektu ve službě Azure Stack hub Key Vault

Vývoj aplikací v centru Azure Stack obvykle vyžaduje vytvoření instančního objektu a používání těchto pověření k ověření před nasazením. Někdy ale ztratíte uložené přihlašovací údaje k instančnímu objektu. Tento článek popisuje, jak vytvořit instanční objekt a uložit hodnoty v Azure Key Vault pro pozdější načtení.

Další informace o Key Vault najdete v tématu [Úvod do Key Vault v centru Azure Stack](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Požadavky

- Předplatné nabídky, která zahrnuje službu Azure Key Vault.
- Prostředí PowerShell je nainstalované a nakonfigurované pro použití s rozbočovačem Azure Stack.

## <a name="key-vault-in-azure-stack-hub"></a>Key Vault v centru Azure Stack

Key Vault v centru Azure Stack pomáhají chránit kryptografické klíče a tajné kódy, které využívají cloudové aplikace a služby. Pomocí Key Vault můžete šifrovat klíče a tajné kódy.

K vytvoření trezoru klíčů použijte tento postup:

1. Přihlaste se k portálu centra Azure Stack.

2. Z řídicího panelu vyberte **+ vytvořit prostředek**a pak **zabezpečení a identita**a pak vyberte **Key Vault.**

   ![Vytvoření trezoru klíčů](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. V podokně **vytvořit Key Vault** přiřaďte k trezoru **název** . Názvy trezorů mohou obsahovat pouze alfanumerické znaky a znak spojovníku (-). Nesmí začínat číslicí.

4. V seznamu dostupných předplatných si vyberte požadované předplatné.

5. Vyberte existující skupinu prostředků nebo vytvořte novou.

6. Vyberte cenovou úroveň.

7. Vyberte jednu z existujících zásad přístupu nebo vytvořte novou. Zásady přístupu umožňují udělit uživatelům, aplikacím nebo skupině zabezpečení oprávnění k provádění operací s tímto trezorem.

8. Volitelně můžete pro povolení přístupu k funkcím zvolit zásadu pokročilého přístupu.

9. Po nakonfigurování nastavení vyberte **OK**a pak vyberte **vytvořit**. Začíná nasazení trezoru klíčů.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

1. Přihlaste se ke svému účtu Azure prostřednictvím Azure Portal.

2. Vyberte **Azure Active Directory**a pak **Registrace aplikací**a pak **Přidat**.

3. Zadejte název a adresu URL pro aplikaci. Pro typ aplikace, kterou chcete vytvořit, vyberte možnost **Webová aplikace/rozhraní API** nebo **nativní** . Po nastavení hodnot vyberte **vytvořit**.

4. Vyberte **Active Directory**, pak **Registrace aplikací**a vyberte svou aplikaci.

5. Zkopírujte **ID aplikace** a uložte ho do kódu aplikace. Ukázkové aplikace používají **ID klienta** při odkazování na **ID aplikace**.

6. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.

7. Zadejte popis a dobu trvání klíče.

8. Vyberte **Uložit**.

9. Zkopírujte **klíč** , který bude k dispozici po kliknutí na **Uložit**.

## <a name="store-the-service-principal-inside-key-vault"></a>Uložení instančního objektu v rámci služby Key Vault

1. Přihlaste se k portálu User Portal pro centrum Azure Stack, vyberte Trezor klíčů, který jste vytvořili dříve, a pak vyberte dlaždici **tajné** .

2. V podokně **tajný kód** vyberte **Generovat/importovat**.

3. V podokně **vytvořit tajný kód** vyberte v seznamu možností možnost **ručně** . Pokud jste objekt služby vytvořili pomocí certifikátů, v rozevíracím seznamu vyberte certifikáty a pak tento soubor nahrajte.

4. Jako název klíče zadejte **ID aplikace** zkopírované z instančního objektu. Název klíče může obsahovat jenom alfanumerické znaky a znak spojovníku (-).

5. Vložte hodnotu klíče z objektu služby na kartu **hodnota** .

6. Vyberte **instanční objekt** pro **typ obsahu**.

7. Nastavte hodnoty data **Aktivace** a **data vypršení platnosti** klíče.

8. Vyberte **vytvořit** a spusťte nasazení.

Po úspěšném vytvoření tajného klíče se tam uloží hlavní informace o instančním objektu. Můžete ho kdykoli vybrat v **tajných klíčích**a zobrazit nebo upravit jeho vlastnosti. Oddíl **Properties** obsahuje identifikátor tajného kódu, který je identifikátor URI (Uniform Resource Identifier), který používají externí aplikace pro přístup k tomuto tajnému kódu.

## <a name="next-steps"></a>Další kroky

- [Použití instančních objektů](../operator/azure-stack-create-service-principals.md?view=azs-2002)
- [Správa Key Vault v centru Azure Stack na portálu](azure-stack-key-vault-manage-portal.md)  
- [Správa Key Vault v centru Azure Stack pomocí prostředí PowerShell](azure-stack-key-vault-manage-powershell.md)
