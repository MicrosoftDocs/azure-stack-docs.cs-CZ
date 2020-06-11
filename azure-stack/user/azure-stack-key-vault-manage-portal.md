---
title: Správa Key Vault v centru Azure Stack pomocí portálu
description: Naučte se spravovat Key Vault v centru Azure Stack pomocí portálu Azure Stack hub.
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 5413c37b0574e022716a1a0d333c18e78a818937
ms.sourcegitcommit: d91e47a51a02042f700c6a420f526f511a6db9a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84666378"
---
# <a name="manage-key-vault-in-azure-stack-hub-using-the-portal"></a>Správa Key Vault v centru Azure Stack pomocí portálu

Tento článek popisuje, jak vytvořit a spravovat Trezor klíčů v centru Azure Stack pomocí portálu Azure Stack hub.

## <a name="prerequisites"></a>Požadavky

Musíte se přihlásit k odběru nabídky, která zahrnuje službu Azure Key Vault.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

1. Přihlaste se k portálu User Portal `https://portal.local.azurestack.external` .

2. Z řídicího panelu vyberte **+ vytvořit prostředek**a pak **Zabezpečení a identita**a pak **Key Vault**.

    ![Key Vault obrazovce](media/azure-stack-key-vault-manage-portal/image1.png)

3. V podokně **vytvořit Key Vault** přiřaďte k trezoru **název** . Názvy trezorů mohou obsahovat pouze alfanumerické znaky a znak spojovníku (-). Nesmí začínat číslicí.

4. Vyberte **předplatné** ze seznamu dostupných předplatných. V rozevíracím seznamu se zobrazí všechna předplatná, která nabízejí službu Key Vault.

5. Vyberte existující **skupinu prostředků**nebo vytvořte novou.

6. Vyberte **cenovou úroveň**. V Azure Stack Development Kit (ASDK) trezory klíčů podporují pouze **standardní** SKU.

7. Vyberte jednu z existujících **zásad přístupu** nebo vytvořte novou. Zásady přístupu vám umožňují udělit oprávnění pro uživatele, aplikaci nebo skupinu zabezpečení k provádění operací s tímto trezorem.

8. Volitelně můžete pro povolení přístupu k funkcím zvolit **zásadu pokročilého přístupu** . Například: virtuální počítače pro nasazení, Správce prostředků pro nasazení šablony a přístup k Azure Disk Encryption pro šifrování svazku.

9. Po nakonfigurování nastavení vyberte **OK**a pak vyberte **vytvořit**. Tento krok spustí nasazení trezoru klíčů.

## <a name="manage-keys-and-secrets"></a>Správa klíčů a tajných kódů

Po vytvoření trezoru klíčů použijte následující postup k vytvoření a správě klíčů a tajných klíčů v trezoru:

### <a name="create-a-key"></a>Vytvoření klíče

1. Přihlaste se k portálu pro uživatele centra Azure Stack `https://portal.local.azurestack.external` .

2. Z řídicího panelu vyberte **všechny prostředky**, vyberte Trezor klíčů, který jste vytvořili dříve, a pak vyberte dlaždici **klíče** .

3. V podokně **klíče** vyberte **Generovat/importovat**.

4. V podokně **vytvořit klíč** klikněte v seznamu **možností**na metodu, kterou chcete použít k vytvoření klíče. Můžete **vygenerovat** nový klíč, **nahrát** existující klíč nebo použít **obnovení zálohy** k výběru zálohy klíče.

5. Zadejte **název** pro svůj klíč. Název klíče může obsahovat jenom alfanumerické znaky a znak spojovníku (-).

6. Volitelně nakonfigurujte pro svůj klíč hodnotu **nastavit datum aktivace** a **nastavte datum vypršení platnosti** .

7. Vyberte **vytvořit** a spusťte nasazení.

Po úspěšném vytvoření klíče ho můžete vybrat v části **klíče** a zobrazit nebo upravit jeho vlastnosti. Oddíl Properties (vlastnosti) obsahuje **identifikátor klíče**, což je identifikátor URI (Uniform Resource Identifier), který používají externí aplikace pro přístup k tomuto klíči. Pro omezení operací s tímto klíčem nakonfigurujte nastavení v části **povolené operace**.

![Klíč identifikátoru URI](media/azure-stack-key-vault-manage-portal/image4.png)

### <a name="create-a-secret"></a>Vytvoření tajného klíče

1. Přihlaste se k portálu User Portal `https://portal.local.azurestack.external` .

2. Z řídicího panelu vyberte **všechny prostředky**, vyberte Trezor klíčů, který jste vytvořili dříve, a pak vyberte dlaždici **tajných** kódů.

3. V části **tajné klíče**vyberte **Přidat**.

4. V části **vytvořit tajný kód**vyberte v seznamu **možností nahrávání**možnost, se kterou chcete vytvořit tajný kód. Tajný klíč můžete vytvořit **ručně** , pokud zadáte hodnotu pro tajný klíč nebo nahrajete **certifikát** z místního počítače.

5. Zadejte **název** tajného kódu. Název tajného kódu může obsahovat jenom alfanumerické znaky a znak spojovníku (-).

6. Volitelně můžete zadat **typ obsahu**a nakonfigurovat hodnoty pro **nastavení datum aktivace** a **nastavit datum vypršení platnosti** tajného klíče.

7. Vyberte **vytvořit** a spusťte nasazení.

Po úspěšném vytvoření tajného klíče ho můžete vybrat pod **tajnými klíči** a zobrazit nebo upravit jeho vlastnosti. **Identifikátor tajného** kódu je identifikátor URI, který můžou externí aplikace použít pro přístup k tomuto tajnému kódu.

![Tajný kód identifikátoru URI](media/azure-stack-key-vault-manage-portal/image5.png)

## <a name="next-steps"></a>Další kroky

* [Nasazení virtuálního počítače načtením hesla uloženého v Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Nasazení virtuálního počítače s certifikátem uloženým v Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
