---
title: Přihlášení k odběru nabídky v Azure Stack | Microsoft Docs
description: Vytvoření předplatných nabídek v Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 51b61eae1f2c00a959eded275c13c5c4b8d06e4c
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991792"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Vytváření předplatných nabídek v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Když [vytvoříte nabídku](azure-stack-create-offer.md), uživatelé potřebují předplatné pro tuto nabídku, aby ji mohli využít. Uživatelé můžou získat předplatné nabídky dvěma způsoby:

- Jako operátor cloudu můžete pro uživatele vytvořit předplatné na portálu pro správu. Můžete vytvářet předplatná veřejných i privátních nabídek.
- Jako uživatel tenanta si můžete vytvořit předplatné veřejné nabídky na portálu User Portal.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Vytvoření předplatného jako operátora cloudu

Operátoři cloudu můžou pomocí portálu pro správu vytvořit předplatné nabídky pro uživatele. Můžete vytvářet předplatná pro členy vašeho vlastního tenanta adresáře. Pokud je povolená [víceklientská](azure-stack-enable-multitenancy.md) architektura, můžete také vytvořit odběry pro uživatele v dalších adresářových klientech.

Pokud nechcete, aby klienti vytvářeli vlastní odběry, zajistěte, aby vaše nabídky byly soukromé a pak vytvořili předplatná pro klienty. Tento přístup je společný při integraci Azure Stack s externími systémy fakturace nebo katalogu služeb.

Po vytvoření předplatného pro uživatele se mohou přihlásit k portálu User Portal a zobrazit, že se přihlásí k odběru této nabídky.  

### <a name="to-create-a-subscription-for-a-user"></a>Vytvoření odběru pro uživatele

1. Na portálu pro správu přejdete na **předplatná uživatelů.**
2. Vyberte **Přidat**. V části **Nový odběr uživatele**zadejte následující informace:  

   - **Zobrazovaný název** – popisný název pro identifikaci předplatného, které se zobrazí jako *název předplatného uživatele*.
   - **Uživatel** – zadejte uživatele z dostupného tenanta adresáře pro toto předplatné. Uživatelské jméno se zobrazí jako *vlastník*.  Formát uživatelského jména závisí na vašem řešení identity. Příklad:

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Tenant adresáře** – vyberte tenanta adresáře, do kterého patří uživatelský účet. Pokud jste nepovolili víceklientské prostředí, je k dispozici pouze váš tenant místního adresáře.

3. Vyberte **nabídku**. V nabídce **nabídky**vyberte **nabídku** pro toto předplatné. Vzhledem k tomu, že vytváříte předplatné pro uživatele, vyberte jako stav přístupnosti možnost **Private** .

4. Vyberte **vytvořit** a vytvořte odběr. Nové předplatné se zobrazí v části **předplatné uživatele**. Když se uživatel přihlásí k portálu User Portal, uvidí podrobnosti předplatného.

### <a name="to-make-an-add-on-plan-available"></a>Zpřístupnění plánu doplňku

Operátor cloudu může kdykoli přidat plán do dříve vytvořeného předplatného:

1. Na portálu pro správu vyberte **všechny služby** a potom v kategorii **prostředky pro správu** vyberte odběry **uživatelů**. Vyberte předplatné, které chcete změnit.

2. Vyberte **Doplňky** a pak vybrat **+ Přidat**.  

3. V části **Přidat plán**vyberte plán, který chcete přidat.

## <a name="create-a-subscription-as-a-user"></a>Vytvoření předplatného jako uživatel

Jako uživatel se můžete přihlásit k portálu User Portal a vyhledat a přihlásit se k odběru veřejných nabídek a doplňkových plánů pro tenanta adresáře (organizace).

>[!NOTE]
>Pokud vaše prostředí Azure Stack podporuje [více tenantů](azure-stack-enable-multitenancy.md), můžete se také přihlásit k odběru nabídek z tenanta vzdáleného adresáře.

### <a name="to-subscribe-to-an-offer"></a>Přihlášení k odběru nabídky

1. [](../asdk/asdk-connect.md) Přihlaste se k [portálu Azure Stack User Portal](https://portal.local.azurestack.external) a vyberte **získat předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. V části **získat předplatné**zadejte popisný název předplatného ve zobrazovaném **názvu**. Vyberte **nabídku** a v části **Zvolte nabídku**vyberte nabídku. Vyberte **vytvořit** a vytvořte odběr.

   ![Vytvoření nabídky](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Po přihlášení k odběru nabídky aktualizujte portál, abyste viděli, které služby jsou součástí nového předplatného.

4. Pokud se chcete podívat na předplatné, které jste vytvořili, vyberte **všechny služby** a potom v kategorii **Obecné** vyberte **odběry**. Pokud si chcete zobrazit podrobnosti o předplatném, vyberte předplatné.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Přihlášení k odběru plánu doplňku

Pokud má nabídka plán doplňku, můžete tento plán kdykoli přidat do svého předplatného.  

1. Na portálu User Portal vyberte **všechny služby**. Dále v kategorii **Obecné** vyberte předplatná apotom vyberte předplatné, které chcete změnit. Pokud jsou k dispozici nějaké dostupné plány, **+ Přidat plán** je aktivní a je k dispozici dlaždice pro **plány doplňku**.

   Pokud není aktivní **+ Přidat plán** , neexistují žádné doplňkové plány pro nabídku přidruženou k tomuto předplatnému.

1. Vyberte **+ Přidat plán** nebo dlaždici **doplňkových plánů** . V části **plány doplňku**vyberte plán, který chcete přidat.

## <a name="next-steps"></a>Další kroky

- [Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)
