---
title: Store pověření instančního objektu ve službě Azure Stack Key Vault | Dokumentace Microsoftu
description: Zjistěte, jak Key Vault ukládá přihlašovací údaje instančního objektu služby ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: efa8dda8061ce81d751e9cce47c5e81a3917f2bf
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138845"
---
# <a name="store-service-principal-credentials-in-azure-stack-key-vault"></a>Store pověření instančního objektu ve službě Azure Stack Key Vault

Vývoj aplikací v Azure stacku obvykle vyžaduje vytváření instančního objektu a pomocí těchto přihlašovacích údajů k ověření před nasazením. V některých případech však ztratíte uložené přihlašovací údaje pro instanční objekt. Tento článek popisuje, jak vytvořit instanční objekt a uložit pro pozdější načtení hodnoty ve službě Azure Key Vault.

Další informace o službě Key Vault najdete v tématu [v tomto článku](azure-stack-key-vault-intro.md).

## <a name="prerequisites"></a>Požadavky

- Předplatné nabídky, která zahrnuje službu Azure Key Vault.
- PowerShell je nakonfigurován pro použití se službou Azure Stack.

## <a name="key-vault-in-azure-stack"></a>Trezor klíčů ve službě Azure Stack

Key Vault ve službě Azure Stack pomáhá chránit kryptografické klíče a tajné kódy, které cloudové aplikace a služby používat. Pomocí služby Key Vault můžete šifrovat klíče a tajné kódy.

Chcete-li vytvořit trezor klíčů, postupujte takto:

1. Přihlaste se k portálu Azure Stack.

2. Z řídicího panelu, vyberte **+ vytvořit prostředek**, pak **zabezpečení + Identita**a pak vyberte **služby Key Vault.**

   ![Vytvoření trezoru klíčů](media/azure-stack-key-vault-store-credentials/create-key-vault.png)

3. V **vytvořit trezor klíčů** podokno, přiřadit **název** pro svůj trezor. Trezor názvy můžou obsahovat jenom alfanumerické znaky a znak spojovníku (-). Nesmí začínat číslicí.

4. V seznamu dostupných předplatných si vyberte požadované předplatné.

5. Vyberte existující skupinu prostředků nebo vytvořte novou.

6. Vyberte cenovou úroveň.

7. Vyberte jednu z existujících zásad přístupu nebo vytvořte novou. Zásady přístupu vám umožní udělit oprávnění pro uživatele, aplikace nebo skupinu zabezpečení k provedení operací k tomuto trezoru.

8. Volitelně můžete zvolte zásadu přístupu advanced. pro povolení přístupu k funkcím.

9. Jakmile konfigurujete nastavení, vyberte **OK**a pak vyberte **vytvořit**. Spustí se nasazení služby key vault.

## <a name="create-a-service-principal"></a>Vytvoření instančního objektu

1. Přihlaste se ke svému účtu Azure na webu Azure portal.

2. Vyberte **Azure Active Directory**, pak **registrace aplikací**, pak **přidat**.

3. Zadejte název a adresu URL pro aplikaci. Vyberte buď **webovou aplikaci nebo API** nebo **nativní** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnot, vyberte **vytvořit**.

4. Vyberte **služby Active Directory**, pak **registrace aplikací**a vyberte svou aplikaci.

5. Kopírovat **ID aplikace** a uloží je v kódu vaší aplikace. Použití ukázkové aplikace **ID klienta** k odkazování na **ID aplikace**.

6. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.

7. Zadejte popis a dobu trvání pro klíč.

8. Vyberte **Uložit**.

9. Kopírovat **klíč** , která bude k dispozici po klepnutí na **Uložit**.

## <a name="store-the-service-principal-inside-key-vault"></a>Store instanční objekt služby ve službě Key Vault

1. Přihlaste se k portálu user portal pro Azure Stack, pak vyberte trezor klíčů, který jste vytvořili dříve a pak vyberte **tajný kód** dlaždici.

2. V **tajný kód** vyberte **vygenerovat/importovat**.

3. V **vytvoření tajného klíče** vyberte **ruční** ze seznamu možností. Pokud jste vytvořili službu objektu zabezpečení pomocí certifikátů, certifikáty vyberte z rozevíracího seznamu a pak nahrajte soubor.

4. Zadejte **ID aplikace** zkopírován z instančního objektu jako název pro váš klíč. Název klíče, který může obsahovat jenom alfanumerické znaky a znak spojovníku (-).

5. Vložte hodnotu váš klíč z objektu do služby **hodnotu** kartu.

6. Vyberte **instanční objekt služby** pro **typ obsahu**.

7. Nastavte **datum aktivace** a **datum vypršení platnosti** hodnoty klíče.

8. Vyberte **vytvořit** ke spuštění nasazení.

Po úspěšném vytvoření tajného kódu je uložených informací o instančním objektu služby. Můžete zvolit kdykoli pod **tajných kódů** a zobrazit nebo upravit její vlastnosti. Oddíl properties obsahuje identifikátor tajného kódu, který je identifikátor URI (Uniform Resource), externí aplikace použít pro přístup k tento tajný kód.

## <a name="next-steps"></a>Další postup

- [Použít instanční objekty](azure-stack-create-service-principals.md)
- [Správa služby Key Vault ve službě Azure Stack pomocí portálu](azure-stack-key-vault-manage-portal.md)  
- [Správa služby Key Vault ve službě Azure Stack pomocí prostředí PowerShell](azure-stack-key-vault-manage-powershell.md)