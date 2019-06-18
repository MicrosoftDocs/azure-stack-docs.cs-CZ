---
title: Odstranit kvóty, plány, nabídky a předplatné | Dokumentace Microsoftu
description: Zjistěte, jak odstranit kvót služby Azure Stack, plány, nabídky a předplatné.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 2e0e4ef7abd1885d843832ed7cc9e845003d0ed7
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152538"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Odstranit kvóty, plány, nabídky a předplatné

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek popisuje, jak odstranit kvóty, plány, nabídky a předplatné, které už nepotřebujete. Jako zásadně můžete odstranit pouze co se nepoužívá. Například odstranění nabídky je možné, pouze pokud nejsou žádná předplatná, které patří do této nabídky.

Předplatná se výjimky této obecné zásady: můžete odstraňovat odběry, které obsahují prostředky; a prostředky se odstraní spolu s předplatné.

Proto pokud chcete odstranit kvótu, musíte pracovat zpět prostřednictvím všechny plány a nabídky, které používají tuto kvótu: spouští se s nabídkami, zkontrolujte, jestli nemáte žádná předplatná, odstraňte každá nabídka potom odstranit plány, které používají kvóty a tak dále.

## <a name="delete-a-subscription"></a>Odstranění předplatného

Chcete-li odstranit odběr, vyberte **všechny služby**, pak **předplatná uživatelů**, chcete-li zobrazit seznam všech předplatných v systému. Pokud pracujete na nabídku, můžete také vybrat **předplatná** z něj.

Odběry můžete odstranit z tohoto seznamu, nebo prostředí PowerShell můžete napsat skript, který odstraní všechna předplatná pro vás pomocí příkazů dokumentovány v článku [předplatná – odstranit odkaz](/rest/api/azurestack/subscriptions/delete).

> [!CAUTION]
> Odstraňuje se předplatné se odstraní také všechny data a prostředky, které obsahuje.

## <a name="delete-an-offer"></a>Odstranit nabídku

Odstranit nabídku, na portálu správce, přejděte na **všechny služby**, pak **nabízí**. Vyberte nabídky, kterou chcete odstranit a pak vyberte **odstranit**.

![delsub1](media/azure-stack-delete-offer/delsub1.png)

V rámci nabídky můžete odstranit pouze pokud nejsou žádná předplatná, jeho použití. Pokud existují odběry založené na nabídce, **odstranit** zašedlé možnost. V takovém případě najdete v článku [odstranění předplatného](#delete-a-subscription) oddílu.

## <a name="delete-a-plan"></a>Odstranit plán

Odstranit plán na portálu přejděte správce do **všechny služby**, pak **plány**. Vyberte plán, kterou chcete odstranit a pak vyberte **odstranit**.

![delsub2](media/azure-stack-delete-offer/delsub2.png)

Plán můžete odstranit pouze když neexistují žádné nabídky nebo předplatná jeho použití. Pokud jsou nabídky, které používají plán, odstranit plán, mohla nezdaří a zobrazí se chybová zpráva. Můžete vybrat **nadřazené nabídky** zobrazíte seznam nabídek, které používají tento plán. Další informace o odstranění nabídky najdete v tématu [odstranit v rámci nabídky](#delete-an-offer).

Plány byla pravděpodobně přidána přímo do předplatného jako doplňkové plány, i v případě, že nejsou součástí nabídky. V takovém případě je nutné odebrat z předplatných, které je používají, aby se Dal odstranit tento plán.

Navíc plán nelze odebrat z předplatného, pokud se jedná o jediný zdroj daného prostředku pro dané předplatné. Například pokud plánu A byla přidána k předplatnému 1 a je pouze plán poskytuje kvótu sítě k předplatnému, nelze odebrat z předplatného. Proto jej nelze odstranit.

## <a name="edit-and-delete-a-quota"></a>Úpravy a odstraňování kvótu

Můžete zobrazit a upravit existující kvóty pomocí portálu pro správce: vyberte **Správa oblastí**, pak vyberte poskytovatele příslušných prostředků a klikněte na **kvóty**. Můžete také odstranit kvóty u určitých poskytovatelů prostředků.

![delsub3](media/azure-stack-delete-offer/delsub3.png)

Alternativně můžete odstranit některé kvóty pomocí těchto rozhraní REST API:

- [Compute](/rest/api/azurestack/quotas%20(compute)/delete)
- [Sítě](/rest/api/azurestack/quotas%20(network)/delete)
- [Storage](/rest/api/azurestack/storagequotas/delete)

> [!NOTE]
> Kvóty nejde odstranit, pokud jsou všechny aktuální plány, které ji používají. Nejprve je nutné odstranit plán, který odkazuje na kvótu.

## <a name="next-steps"></a>Další postup

- [Vytvořit odběry](azure-stack-subscribe-plan-provision-vm.md)
- [Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)