---
title: Přehled Azure Stack plánů, nabídek, kvót a předplatných | Microsoft Docs
description: Jako operátor cloudu chci pochopit Azure Stack plány, nabídky, kvóty a odběry.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 08/29/2019
ms.openlocfilehash: 57d2fb7b9fb89858f13b3946be0e36d9529297a8
ms.sourcegitcommit: 1bae55e754d7be75e03af7a4db3ec43fd7ff3e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319113"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Přehled plánů, nabídek, kvót a předplatných

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

[Azure Stack](azure-stack-overview.md) vám umožní doručovat širokou škálu služeb, jako jsou virtuální počítače, databáze SQL Server, SharePoint, Exchange a dokonce i [Azure Marketplace položky](azure-stack-marketplace-azure-items.md). Jako operátor Azure Stack můžete nakonfigurovat a doručovat služby v Azure Stack pomocí plánů, nabídek a kvót.

Nabídky obsahují jeden nebo více plánů a každý plán zahrnuje jednu nebo více služeb. Vytvořením plánů a jejich kombinováním do různých nabídek můžete spravovat:

- Které služby a prostředky, ke kterým mají uživatelé přístup.
- Množství prostředků, které může uživatel spotřebovat.
- Které oblasti mají přístup k prostředkům.

Při doručování služby postupujte podle následujících kroků vysoké úrovně:

1. Přidejte službu, kterou chcete doručovat uživatelům.
2. Vytvořte plán, který má jednu nebo více služeb. Při vytváření plánu vyberte nebo vytvořte kvóty, které definují omezení prostředků u každé služby v plánu.
3. Vytvořte nabídku, která obsahuje jeden nebo více plánů. Nabídka může zahrnovat základní plány a volitelné plány doplňku.

Po vytvoření nabídky se uživatelé můžou přihlásit k odběru služeb a prostředků, které nabídka nabízí. Uživatelé se můžou přihlásit k odběru tolika nabídek, kolik jich chtějí. Následující obrázek ukazuje jednoduchý příklad uživatele, který má přihlášení k odběru dvou nabídek. Každá nabídka má plán nebo dva a každý plán poskytuje přístup ke službám.

![Předplatné tenanta s nabídkami a plány](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Plány

Plány představují seskupení jedné nebo více služeb. Jako operátor Azure Stack [vytvoříte plány](azure-stack-create-plan.md) , které nabídnete vašim uživatelům. Vaši uživatelé se pak můžou přihlásit k odběru nabídek, aby mohli používat plány a služby, které k nim patří. Při vytváření plánů se ujistěte, že jste nastavili své kvóty, definovali základní plány a zvažte zahrnutí volitelných plánů doplňku.

### <a name="quotas"></a>Kvóty

Abyste mohli spravovat kapacitu cloudu, můžete využít předem nakonfigurované *kvóty*nebo vytvořit novou kvótu pro každou službu v plánu. Kvóty definují horní omezení prostředků, které může předplatné uživatele zřizovat nebo využívat. Kvóta může například uživateli dovolit vytvořit až pět virtuálních počítačů (VM).

> [!IMPORTANT]
> Může trvat až dvě hodiny, než budou nové kvóty dostupné na portálu User Portal nebo před tím, než se vynutila změněná kvóta.

Můžete nakonfigurovat kvóty podle oblasti. Například plán, který poskytuje výpočetní služby pro oblast A, může mít kvótu dvou virtuálních počítačů.

>[!NOTE]
>V Azure Stack Development Kit je k dispozici pouze jedna oblast (s názvem *místní*).

Přečtěte si další informace o [typech kvót v Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Základní plán

Při vytváření nabídky může správce služby zahrnovat základní plán. Tyto základní plány jsou standardně zahrnuté, když se uživatel přihlásí k odběru této nabídky. Když se uživatel přihlásí k odběru, má přístup ke všem poskytovatelům prostředků, které jsou uvedené v těchto základních plánech (s odpovídajícími kvótami).

### <a name="add-on-plans"></a>Plány doplňku

Plány doplňku jsou volitelné plány, které do nabídky přidáte. V předplatném nejsou ve výchozím nastavení zahrnuty plány doplňku. Doplňkové plány jsou další plány (s kvótami) dostupné v nabídce, kterou může předplatitel přidat ke svým předplatným. Můžete například nabídnout základní plán s omezenými prostředky pro zkušební verzi a plán doplňku s většími prostředky pro zákazníky, kteří se rozhodnou tuto službu přijmout.

## <a name="offers"></a>Nabídky

Nabídky jsou skupiny jednoho nebo více plánů, které vytvoříte, aby se uživatelé mohli přihlásit k jejich odběru. Například nabídka alfa může obsahovat plán A, který poskytuje sadu výpočetních služeb a plán B, který poskytuje sadu úložišť a síťových služeb.

Když [vytvoříte nabídku](azure-stack-create-offer.md), musíte zahrnout aspoň jeden základní plán, ale můžete také vytvořit doplňky, které mohou uživatelé přidat ke svému předplatnému.

## <a name="subscriptions"></a>Předplatná

Předplatné je způsob, jakým uživatelé přistupují k Vašim nabídkám. Pokud jste operátor Azure Stack pro poskytovatele služeb, vaši uživatelé (klienti) si své služby kupují pomocí přihlášení k odběru vašich nabídek. Pokud jste operátor Azure Stack v organizaci, uživatelé (zaměstnanci) se můžou přihlásit k odběru služeb, které nabízíte bez placení.

Každá kombinace uživatele s nabídkou je jedinečné předplatné. Uživatel může mít odběr více nabídek, ale každé předplatné platí pouze pro jednu nabídku. Plány, nabídky a kvóty platí jenom pro jedinečné předplatné – nedají se sdílet mezi předplatnými. Každý prostředek, který uživatel vytvoří, je přidružený k jednomu předplatnému.

### <a name="default-provider-subscription"></a>Předplatné výchozího poskytovatele

Výchozí předplatné poskytovatele se automaticky vytvoří při nasazení Azure Stack Development Kit. Pomocí tohoto předplatného můžete spravovat Azure Stack, nasazovat další poskytovatele prostředků a vytvářet plány a nabídky pro uživatele. Z důvodů zabezpečení a licencování by se nemělo používat ke spouštění úloh a aplikací zákazníka. Kvótu výchozího předplatného poskytovatele nelze změnit.

## <a name="next-steps"></a>Další kroky

Další informace o plánech a nabídkách najdete v tématu [Vytvoření plánu](azure-stack-create-plan.md).
