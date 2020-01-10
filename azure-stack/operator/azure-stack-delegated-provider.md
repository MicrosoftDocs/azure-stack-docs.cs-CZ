---
title: Delegování nabídek v centru Azure Stack | Microsoft Docs
description: Naučte se delegovat úlohy, jako je vytváření nabídek a registrace uživatelů.
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
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 7850c24a281a91f6797074778e72efdc281f6958
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811834"
---
# <a name="delegate-offers-in-azure-stack-hub"></a>Delegování nabídek v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Jako operátor centra Azure Stack můžete chtít, aby se k registraci uživatelů a vytváření předplatných museli zajímat další lidé. Pokud jste například poskytovatelem služeb, můžete chtít, aby prodejci mohli registrovat zákazníky a spravovat je vaším jménem. Nebo, pokud jste součástí centrální skupiny IT v podniku, možná budete chtít delegovat registraci uživatelů na jiné pracovníky IT.

Delegování usnadňuje dosažení a správu více uživatelů, než můžete sami sebe, jak je znázorněno na následujícím obrázku:

![Úrovně delegování v centru Azure Stack](media/azure-stack-delegated-provider/image1.png)

Delegovaný zprostředkovatel pomocí delegování spravuje nabídku (nazývanou *delegovanou nabídku*) a koncovým zákazníkům získává předplatná v rámci této nabídky bez zásahu správce systému.

## <a name="delegation-roles"></a>Role delegování

Následující role jsou součástí delegování:

* *Operátor centra Azure Stack* spravuje infrastrukturu centra Azure Stack a vytvoří šablonu nabídky. Operátor poslouží ostatním uživatelům k poskytování nabídek do svého tenanta.

* Delegované operátory centra Azure Stack jsou uživatelé s právy *vlastníka* nebo *přispěvatele* v předplatných s názvem *delegovaní zprostředkovatelé*. Můžou patřit do jiných organizací, jako jsou například jiní klienti Azure Active Directory (Azure AD).

* *Uživatelé* si můžou zaregistrovat nabídky a používat je ke správě svých úloh, vytváření virtuálních počítačů, ukládání dat a tak dále.

## <a name="delegation-steps"></a>Postup delegování

Existují dva základní kroky nastavení delegování:

1. **Vytvoření předplatného delegovaného poskytovatele**: Přihlaste se k odběru nabídky, která obsahuje jenom službu předplatných. Uživatelé, kteří se přihlásí k odběru této nabídky, můžou rozšíření delegovaných nabídek přenést na jiné uživatele tak, že je přihlásíte pro tyto nabídky.

2. **Delegování nabídky delegovanému zprostředkovateli**: Tato nabídka umožňuje delegovanému poskytovateli vytvářet předplatná nebo rozšiřuje nabídku na své uživatele. Delegovaný zprostředkovatel teď může nabídku převzít a nabídnout ji ostatním uživatelům.

Následující obrázek ukazuje postup nastavení delegování:

![Postup vytvoření delegovaného zprostředkovatele a jeho povolení k registraci uživatelů v centru Azure Stack](media/azure-stack-delegated-provider/image2.png)

### <a name="delegated-provider-requirements"></a>Požadavky na delegovaného zprostředkovatele

Aby mohl uživatel fungovat jako delegovaný poskytovatel, vytvoří v rámci předplatného vztah s hlavním poskytovatelem. Toto předplatné identifikuje delegovaného zprostředkovatele, který má právo prezentovat delegované nabídky jménem hlavního poskytovatele.

Po navázání tohoto vztahu může operátor centra Azure Stack delegovat nabídku na delegovaného zprostředkovatele. Delegovaný zprostředkovatel může nabídku převzít, přejmenovat ji (ale nezměnit její látku) a nabídnout ji svým zákazníkům.

## <a name="delegation-walkthrough"></a>Návod k delegování

Následující části obsahují návod pro nastavení delegovaného zprostředkovatele, delegování nabídky a ověření, že se uživatelé mohou zaregistrovat k delegované nabídce.

### <a name="set-up-roles"></a>Nastavení rolí

Chcete-li použít tento návod, potřebujete kromě svého účtu operátora centra Azure Stack dva účty Azure AD. Pokud tyto dva účty nemáte, musíte je vytvořit. Účty můžou patřit do libovolného uživatele Azure AD a označují se jako delegovaný zprostředkovatel a uživatel.

| **Role** | **Práva organizace** |
| --- | --- |
| Delegovaný zprostředkovatel |Uživatel |
| Uživatel |Uživatel |

 > [!NOTE]
 > V případě prodejců CSP by vytváření tohoto delegovaného zprostředkovatele vyžadovalo, aby tito uživatelé byli v adresáři tenanta (uživatel Azure AD). Operátor centra Azure Stack se musí [nejdřív](azure-stack-enable-multitenancy.md) připojit ke službě Azure AD a potom pomocí následujících [kroků](azure-stack-csp-howto-register-tenants.md)nastavit účtování a využití a fakturace.

### <a name="identify-the-delegated-provider"></a>Identifikace delegovaného zprostředkovatele

1. Přihlaste se k portálu pro správu jako operátor centra Azure Stack.

1. Vytvoření nabídky, která umožní uživateli, aby se stal delegovaným zprostředkovatelem:

   a.  [Vytvořte plán](azure-stack-create-plan.md).
       Tento plán by měl zahrnovat jenom službu předplatného. V tomto článku se jako příklad používá plán s názvem **PlanForDelegation** .

   b.  [Vytvořte nabídku](azure-stack-create-offer.md) na základě tohoto plánu. V tomto článku se jako příklad používá nabídka s názvem **OfferToDP** .

   c.  Přidejte delegovaného zprostředkovatele jako předplatitele této nabídky, a to tak, že vyberete **odběry**, pak **přidáte**a pak **nové předplatné tenanta**.

   ![Přidat delegovaného zprostředkovatele jako předplatitele na portálu pro správu centra Azure Stack](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Stejně jako u všech nabídek centra Azure Stack máte možnost nastavit si nabídku jako veřejnou a umožnit uživatelům, aby si ji zaregistrovali nebo si ji zachovali a pomohli tak, aby si zaregistrovali registraci v rámci služby Azure Stack hub. Delegovaní zprostředkovatelé jsou obvykle malé skupiny. Chcete určit, kdo k němu je přihlášený, takže tato nabídka bude ve většině případů vhodná.

### <a name="azure-stack-hub-operator-creates-the-delegated-offer"></a>Operátor centra Azure Stack vytvoří delegovanou nabídku.

Dalším krokem je vytvoření plánu a nabídky, která bude delegována a kterou budou uživatelé používat. Tuto nabídku je vhodné definovat tak, jak chcete, aby ji uživatelé viděli, protože delegovaný zprostředkovatel nemůže měnit plány a kvóty, které obsahuje.

1. Jako operátor centra Azure Stack [vytvořte plán](azure-stack-create-plan.md) a [nabídku](azure-stack-create-offer.md) na základě plánu. V tomto článku se jako příklad používá nabídka s názvem **DelegatedOffer** .

   > [!NOTE]
   > Tato nabídka nemusí být veřejná, ale můžete ji zveřejnit. Ve většině případů ale chcete, aby k této nabídce měli přístup jenom delegovaní zprostředkovatelé. Po delegování soukromé nabídky, jak je popsáno v následujícím postupu, má delegovaný zprostředkovatel přístup k němu.

2. Delegovat nabídku Přejít na **DelegatedOffer**. V části **Nastavení**vyberte **delegovaní zprostředkovatelé**a pak vyberte **Přidat**.

3. V rozevíracím seznamu vyberte předplatné delegovaného zprostředkovatele a pak vyberte **delegovat**.

   ![Přidání delegovaného zprostředkovatele na portálu správce Azure Stackového centra](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Delegovaný poskytovatel přizpůsobuje nabídku.

Přihlaste se k portálu User Portal jako delegovaný zprostředkovatel a pak vytvořte novou nabídku pomocí delegované nabídky jako šablony.

1. Vyberte **+ vytvořit prostředek**, pak **nabídky tenanta + plány**a pak vyberte **nabídky**.

    ![Vytvoření nové nabídky na portále User Portal centra Azure Stack](media/azure-stack-delegated-provider/image5.png)

2. Přiřaďte k nabídce název. V tomto příkladu se používá **ResellerOffer**. Vyberte delegovanou nabídku, na které se má založit, a pak vyberte **vytvořit**.

   ![Přiřazení názvu v Azure Stack portálu User Portal](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Je důležité pochopit, že delegovaní zprostředkovatelé můžou zvolit jenom nabídky, které jsou na ně delegované. Tyto nabídky nemůžou provádět změny. Tyto nabídky může změnit pouze operátor centra Azure Stack. Například pouze operátor může změnit své plány a kvóty. Delegovaný zprostředkovatel nevytváří nabídku ze základních plánů a plánů doplňku.

3. Delegovaný zprostředkovatel může tyto nabídky zveřejnit prostřednictvím vlastní adresy URL portálu. Pokud chcete nabídku zveřejnit, vyberte **Procházet**a pak **nabídky**. Vyberte nabídku a pak vyberte **změnit stav**.

4. Veřejné delegované nabídky se teď zobrazují jenom prostřednictvím delegovaného portálu. Vyhledání a změna této adresy URL:

    a.  Vyberte **Procházet**, pak **všechny služby**a potom v kategorii **Obecné** vyberte **předplatná**. Vyberte předplatné delegovaného poskytovatele (například **DPSubscription**) a pak **vlastnosti**.

    b.  Zkopírujte adresu URL portálu do samostatného umístění, například do poznámkového bloku.

    ![Výběr předplatného delegovaného poskytovatele na portálu Azure Stack User Portal](media/azure-stack-delegated-provider/dpportaluri.png)  

   Dokončili jste vytváření delegované nabídky jako delegovaného poskytovatele. Odhlaste se jako delegovaný zprostředkovatel a zavřete okno prohlížeče.

### <a name="sign-up-for-the-offer"></a>Zaregistrujte si nabídku

1. V novém okně prohlížeče přejít na adresu URL delegovaného portálu, kterou jste uložili v předchozím kroku. Přihlaste se k portálu jako uživatel.

   >[!NOTE]
   >Delegované nabídky nejsou viditelné, pokud nepoužíváte delegovaný portál.

1. Na řídicím panelu vyberte **získat předplatné**. Uvidíte, že uživateli se zobrazí jenom delegované nabídky, které vytvořil delegovaný zprostředkovatel.

   ![Zobrazení a výběr nabídek v portálu Azure Stack User Portal](media/azure-stack-delegated-provider/image8.png)

Proces delegování nabídky je dokončený. Nyní se uživatel může zaregistrovat k této nabídce a získat tak jejich předplatné.

## <a name="move-subscriptions-between-delegated-providers"></a>Přesun předplatných mezi delegovanými zprostředkovateli

V případě potřeby se předplatné dá přesunout mezi novými nebo existujícími delegovanými zprostředkovateli, kteří patří do stejného tenanta adresáře. Můžete je přesunout pomocí rutiny prostředí PowerShell [Move-AzsSubscription](/powershell/module/azs.subscriptions.admin).

Přesunutí předplatných je užitečné v těchto případech:

* Připojíte nového člena týmu, který bude přebírat role delegovaného zprostředkovatele, a chcete přiřadit k předplatným členů týmu, které byly dříve vytvořeny v rámci výchozího předplatného poskytovatele.
* Máte několik delegovaných poskytovatelů předplatných v rámci stejného tenanta adresáře (Azure AD) a potřebujete mezi nimi přesunout odběry uživatelů. K tomuto scénáři může dojít, když se člen týmu přesouvá mezi týmy a jejich předplatné musí být přiděleno novému týmu.

## <a name="next-steps"></a>Další kroky

* [Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)
