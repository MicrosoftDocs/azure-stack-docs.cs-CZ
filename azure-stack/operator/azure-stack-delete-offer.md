---
title: Odstranění kvót, plánů, nabídek a předplatných
description: Naučte se odstraňovat kvóty, plány, nabídky a odběry centra Azure Stack.
author: bryanla
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 0e5f8963b2cdca8f116e1fdd889e1e8087265b0f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80806891"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Odstranění kvót, plánů, nabídek a předplatných

Tento článek popisuje, jak odstranit kvóty, plány, nabídky a odběry, které už nepotřebujete. Obecně platí, že můžete odstranit pouze to, co se nepoužívá. Například odstranění nabídky je možné pouze v případě, že nejsou k dispozici žádná předplatná, která patří do této nabídky.

Odběry jsou výjimkou tohoto obecného principu: můžete odstranit odběry, které obsahují prostředky. a prostředky se odstraní spolu s předplatným.

Proto pokud chcete kvótu odstranit, musíte se vrátit zpátky pomocí všech plánů a nabídek, které tuto kvótu používají: počínaje nabídkami, zajistěte, aby nemají žádná předplatná, odstraňte jednotlivé nabídky a pak odstraňte plány, které kvótu používají, a tak dále.

## <a name="delete-a-subscription"></a>Odstranění předplatného

Pokud chcete odběr odstranit, vyberte **všechny služby**a potom **předplatné uživatele**, abyste zobrazili seznam všech předplatných v systému. Pokud pracujete na nabídce, můžete také vybrat **předplatná** .

Odběry můžete z tohoto seznamu odstranit, nebo můžete pomocí prostředí PowerShell napsat skript, který odstraní všechna předplatná, a to pomocí příkazů popsaných v části [předplatná-odstranit odkaz](/rest/api/azurestack/subscriptions/delete).

> [!CAUTION]
> Odstraněním odběru dojde také k odstranění všech dat a prostředků, které obsahuje.

## <a name="delete-an-offer"></a>Odstranění nabídky

Pokud chcete nabídku odstranit, klikněte na portálu pro správu na **všechny služby**a pak na **nabídky**. Vyberte nabídku, kterou chcete odstranit, a pak vyberte **Odstranit**.

![delsub1](media/azure-stack-delete-offer/delsub1.png)

Nabídku můžete odstranit jenom v případě, že ji nepoužívá žádná předplatná. Pokud odběry existují na základě nabídky, možnost **Odstranit** není k dispozici. V tomto případě se podívejte na oddíl [Odstranit předplatné](#delete-a-subscription) .

## <a name="delete-a-plan"></a>Odstranění plánu

Plán odstraníte tak, že na portálu pro správu přejdete na **všechny služby**a pak na **plány**. Vyberte plán, který chcete odstranit, a pak vyberte **Odstranit**.

![delsub2](media/azure-stack-delete-offer/delsub2.png)

Plán můžete odstranit jenom v případě, že ho nepoužívají žádné nabídky nebo předplatné. Pokud jsou k dispozici nabídky, které plán používají, odstraňte plán, umožněte jeho selhání a zobrazí se chybová zpráva. Můžete vybrat **nadřazené nabídky** a zobrazit tak seznam nabídek, které používají tento plán. Další informace o odstraňování nabídek najdete v tématu [odstranění nabídky](#delete-an-offer).

Plány mohou být přidány přímo do předplatného jako plány doplňku, a to i v případě, že nejsou součástí nabídky. V takovém případě je nutné je odebrat z předplatných, která je používají, aby bylo možné plán odstranit.

Plán se taky nedá z předplatného odebrat, pokud se jedná o jediný zdroj daného prostředku pro toto předplatné. Pokud je například plán A přidán k předplatnému 1 a jedná se o jediný plán, který pro předplatné poskytuje kvótu sítě, nelze jej z předplatného odebrat. Proto jej nelze odstranit.

## <a name="edit-and-delete-a-quota"></a>Úprava a odstranění kvóty

Stávající kvóty můžete zobrazit a upravit pomocí portálu pro správu: vyberte **Správa oblastí**, pak vyberte příslušného poskytovatele prostředků a klikněte na **kvóty**. Můžete také odstranit kvóty pro určité poskytovatele prostředků.

![delsub3](media/azure-stack-delete-offer/delsub3.png)

Případně můžete některé kvóty odstranit pomocí těchto rozhraní REST API:

- [Compute](/rest/api/azurestack/quotas%20(compute)/delete)
- [Síť](/rest/api/azurestack/quotas%20(network)/delete)

> [!NOTE]
> Kvótu nemůžete odstranit, pokud existují nějaké aktuální plány, které ji používají. Nejdříve je nutné odstranit plán, který odkazuje na kvótu.

## <a name="next-steps"></a>Další kroky

- [Vytvoření předplatných](azure-stack-subscribe-plan-provision-vm.md)
- [Zřízení virtuálního počítače](../user/azure-stack-create-vm-template.md)