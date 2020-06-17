---
title: Použití portálu pro správu v centru Azure Stack
description: Naučte se používat portál pro správu v centru Azure Stack.
author: justinha
ms.topic: quickstart
ms.date: 06/07/2019
ms.author: justinha
ms.reviewer: efemmano
ms.lastreviewed: 06/07/2019
ms.openlocfilehash: d7951500afe21ec0800328230d6b5f1653934c40
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819202"
---
# <a name="use-the-administrator-portal-in-azure-stack-hub"></a>Použití portálu pro správu v centru Azure Stack

V Azure Stack hub existují dva Portály: portál pro správu a portál User Portal. Jako operátor centra Azure Stack použijete portál pro správu pro každodenní správu a provoz centra Azure Stack.

## <a name="access-the-administrator-portal"></a>Přístup k portálu pro správu

Pokud chcete získat přístup k portálu pro správu, přejděte na adresu URL portálu a přihlaste se pomocí přihlašovacích údajů operátora centra Azure Stack. V případě integrovaného systému se adresa URL portálu liší v závislosti na názvu oblasti a externím plně kvalifikovaném názvu domény (FQDN) vašeho nasazení centra Azure Stack. Adresa URL portálu pro správu je vždycky stejná pro nasazení Azure Stack Development Kit (ASDK).

| Prostředí | Adresa URL portálu pro správu |   
| -- | -- | 
| ASDK| `https://adminportal.local.azurestack.external`  |
| Integrované systémy | https://adminportal.&lt; *oblast* &gt; . &lt; *Plně kvalifikovaný název domény*&gt; | 
| | |

> [!TIP]
> Pro prostředí ASDK je potřeba nejdřív zajistit, abyste se mohli [připojit k hostiteli vývojové sady](../asdk/asdk-connect.md) prostřednictvím připojení ke vzdálené ploše nebo prostřednictvím virtuální privátní sítě (VPN).

 ![Portál pro správu centra Azure Stack](media/azure-stack-manage-portals/admin-portal.png)

Výchozí časové pásmo pro všechna nasazení centra Azure Stack je nastavené na koordinovaný světový čas (UTC).

Na portálu pro správu můžete provádět například tyto akce:

* [Registrace centra Azure Stack s Azure](azure-stack-registration.md)
* [Naplnění Marketplace](azure-stack-download-azure-marketplace-item.md)
* [Vytváření plánů, nabídek a předplatných pro uživatele](service-plan-offer-subscription-overview.md)
* [Monitorování stavu a výstrah](azure-stack-monitor-health.md)
* [Spravovat aktualizace centra Azure Stack](azure-stack-updates.md)

Dlaždice **kurzu rychlý Start** obsahuje odkazy na online dokumentaci pro nejběžnější úlohy.

I když může operátor vytvořit prostředky, jako jsou virtuální počítače (VM), virtuální sítě a účty úložiště, na portálu pro správu, měli byste se [přihlásit k portálu User Portal](../user/azure-stack-use-portal.md) a vytvářet a testovat prostředky.

>[!NOTE]
>Odkaz **vytvořit virtuální počítač** v dlaždici kurzu rychlý Start vytvoří virtuální počítač na portálu pro správu, ale je určený jenom k ověření, že se centrum Azure Stack úspěšně nasadilo.

## <a name="understand-subscription-behavior"></a>Principy chování předplatného

Na portálu pro správu jsou ve výchozím nastavení vytvořeny tři předplatná: spotřeba, výchozí zprostředkovatel a měření. Jako operátor použijete většinou *předplatné výchozího poskytovatele*. Nemůžete přidat žádná další předplatná a používat je na portálu pro správu.

Další předplatná jsou vytvořena uživateli na portálu User Portal na základě plánů a nabídek, které pro ně vytvoříte. Portál User Portal ale neposkytuje přístup k žádným z administrativních a provozních možností portálu pro správu.

Správce a uživatelské portály jsou zajištěny oddělenými instancemi Azure Resource Manager. Z důvodu tohoto oddělení Azure Resource Manager se odběry nevztahují mezi portály. Pokud například jako operátor centra Azure Stack se přihlásíte k portálu User Portal, nebudete mít přístup k *předplatnému výchozímu poskytovatele*. I když nemáte přístup k žádným funkcím pro správu, můžete si vytvořit předplatná z dostupných veřejných nabídek. Pokud jste přihlášeni k portálu User Portal, jste považováni za uživatele klienta.

  >[!NOTE]
  >Pokud uživatel do ASDK prostředí patří do stejného adresáře tenanta jako operátor centra Azure Stack, nebrání se v přihlašování k portálu pro správu. Nemůžou ale mít přístup ke všem funkcím správy ani přidávat předplatná k nabídkám přístupu, které jsou k dispozici na portálu User Portal.

## <a name="administrator-portal-tips"></a>Tipy pro správu portálu

### <a name="customize-the-dashboard"></a>Přizpůsobit řídicí panel

Řídicí panel obsahuje sadu výchozích dlaždic. Můžete vybrat **Upravit řídicí panel** pro úpravu výchozího řídicího panelu, nebo výběrem **nového řídicího** panelu Přidat vlastní řídicí panel. Do řídicího panelu můžete přidat také dlaždice. Vyberte například **+ vytvořit prostředek**, klikněte pravým tlačítkem **nabídky nabídky + plány**a pak vyberte **Připnout na řídicí panel**.

V některých případech se může na portálu zobrazit prázdný řídicí panel. Řídicí panel obnovíte tak, že kliknete na **Upravit řídicí panel**a kliknete pravým tlačítkem a vyberete **obnovit do výchozího stavu**.

### <a name="quick-access-to-online-documentation"></a>Rychlý přístup k online dokumentaci

Pokud chcete získat přístup k dokumentaci k operátorovi centra Azure Stack, použijte v pravém horním rohu portálu pro správu ikonu Nápověda a podpora (otazník). Přesuňte kurzor na ikonu a pak vyberte **help + podpora**.

### <a name="quick-access-to-help-and-support"></a>Rychlý přístup k nápovědě a podpoře

Pokud kliknete na ikonu nápovědy (otazník) v pravém horním rohu portálu pro správu, klikněte na tlačítko **Nápověda a podpora**a potom klikněte na možnost **Nová žádost o podporu** v části **Podpora**, dojde k jednomu z následujících výsledků:

- Pokud používáte integrovaný systém, tato akce otevře web, na kterém můžete přímo otevřít lístek podpory s podpora Microsoftu. Přečtěte si, [kde získat podporu](azure-stack-manage-basics.md#where-to-get-support) pro pochopení, kdy byste měli projít podporu Microsoftu, nebo prostřednictvím podpory dodavatele hardwaru OEM (Original Equipment Manufacturer).
- Pokud používáte ASDK, otevře se tato akce přímo na [webu fóra centra Azure Stack](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) . Tato fóra se pravidelně monitorují. Vzhledem k tomu, že ASDK je zkušební prostředí, není k dispozici žádná oficiální podpora prostřednictvím podpora Microsoftu.

### <a name="quick-access-to-the-azure-roadmap"></a>Rychlý přístup k plánu Azure

Pokud v pravém horním rohu portálu pro správu vyberete **Nápověda a podpora** (otazník) a pak vyberete **plán Azure**, otevře se nová karta prohlížeče a přejdete na plán Azure. Zadáním **Azure Stack hub** do vyhledávacího pole **produkty** můžete zobrazit všechny aktualizace plánu centra Azure Stack.

## <a name="next-steps"></a>Další kroky

[Zaregistrujte Azure Stack hub pomocí Azure](azure-stack-registration.md) a nasaďte [Azure Stack na webu Centrum pro Marketplace](azure-stack-marketplace.md) s položkami, které nabídnou vašim uživatelům.
