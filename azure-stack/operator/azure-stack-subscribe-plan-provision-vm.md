---
title: Přihlášení k odběru nabídky ve Azure Stack
description: Vytvoření předplatných nabídek v Azure Stack
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: c69c8a4be93cc392ca1eaf4edb70fb7fb4fc8676
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283370"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Vytváření předplatných nabídek v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Po [Vytvoření nabídky](azure-stack-create-offer.md)uživatelé k této nabídce potřebují předplatné, aby ji mohli používat. Existují dva způsoby, jak můžou uživatelé získat předplatné nabídky:

- Jako operátor cloudu můžete vytvořit předplatné pro uživatele v portálu pro správu. Předplatná, která vytvoříte, můžou být pro veřejné i soukromé nabídky.
- Jako uživatel klienta se můžete přihlásit k odběru veřejné nabídky při používání portálu User Portal.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Vytvoření předplatného jako operátora cloudu

Operátoři cloudu používají portál pro správu k vytvoření odběru nabídky pro uživatele. Odběry je možné vytvořit pro členy vašeho vlastního tenanta adresáře. Pokud je povolená [víceklientská](azure-stack-enable-multitenancy.md) architektura, můžete také vytvořit odběry pro uživatele v dalších adresářových klientech.

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

1. Na portálu pro správu vyberte **všechny služby** a potom v kategorii **prostředky pro správu** vyberte **odběry uživatelů**. Vyberte předplatné, které chcete změnit.

2. Vyberte **Doplňky** a pak vybrat **+ Přidat**.  

3. V části **Přidat plán**vyberte plán, který chcete přidat.

## <a name="create-a-subscription-as-a-user"></a>Vytvoření předplatného jako uživatel

Jako uživatel se můžete přihlásit k portálu User Portal a vyhledat a přihlásit se k odběru veřejných nabídek a doplňkových plánů pro tenanta adresáře (organizace).

>[!NOTE]
>Pokud vaše prostředí Azure Stack podporuje [více tenantů](azure-stack-enable-multitenancy.md), můžete se také přihlásit k odběru nabídek z tenanta vzdáleného adresáře.

### <a name="to-subscribe-to-an-offer"></a>Přihlášení k odběru nabídky

1. [Přihlaste](../asdk/asdk-connect.md) se k [portálu Azure Stack User Portal](https://portal.local.azurestack.external) a vyberte **získat předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. V části **získat předplatné**zadejte popisný název předplatného ve **zobrazovaném názvu**. Vyberte **nabídku** a v části **Zvolte nabídku**vyberte nabídku. Vyberte **vytvořit** a vytvořte odběr.

   ![Vytvoření nabídky](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Po přihlášení k odběru nabídky aktualizujte portál, abyste viděli, které služby jsou součástí nového předplatného.

4. Pokud se chcete podívat na předplatné, které jste vytvořili, vyberte **všechny služby** a potom v kategorii **Obecné** vyberte **odběry**. Pokud si chcete zobrazit podrobnosti o předplatném, vyberte předplatné.  

### <a name="to-enable-an-add-on-plan-in-your-subscription"></a>Povolení doplňkového plánu v rámci předplatného

Pokud vám nabídka, kterou jste přihlásili k odběru, má plán doplňku, můžete tento plán kdykoli přidat do svého předplatného.  

1. Na portálu User Portal vyberte **všechny služby**. Dále v kategorii **Obecné** vyberte **předplatná**a potom vyberte předplatné, které chcete změnit. Pokud jsou k dispozici nějaké dostupné plány, **+ Přidat plán** je aktivní a zobrazí se dlaždice pro **plány doplňku**.

   Pokud není aktivní **+ Přidat plán** , neexistují žádné doplňkové plány pro nabídku přidruženou k tomuto předplatnému.

1. Vyberte **+ Přidat plán** nebo dlaždici **doplňkových plánů** . V části **plány doplňku**vyberte plán, který chcete přidat.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak uživatel teď může nasadit prostředky do svého předplatného: 
  - [Několik rychlých startů uživatelů](../user/azure-stack-quick-windows-portal.md) ukazuje, jak zřídit virtuální počítače s Windows a Linux pomocí PowerShellu, rozhraní příkazového řádku Azure a portálu User Portal. 
  - [Kurz, který používá šablonu Azure Resource Manager,](../user/azure-stack-create-vm-template.md) ukazuje, jak nasadit virtuální počítač s Ubuntu 16,04, na kterém běží Minikube, ke správě clusteru kubenetes. 
