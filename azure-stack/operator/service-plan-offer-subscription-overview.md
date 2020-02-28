---
title: Přehled služeb Azure Stack hub, plánů, nabídek a předplatných
description: Přehled služeb Azure Stack hub, plánů, nabídek a předplatných.
author: BryanLa
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: 0e5d71335fb5b7086341c7c06ec503aae1a8e728
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704773"
---
# <a name="azure-stack-hub-services-plans-offers-subscriptions-overview"></a>Přehled služeb Azure Stack hub, plánů, nabídek a předplatných

[Centrum Microsoft Azure Stack](azure-stack-overview.md) je hybridní cloudová platforma, která umožňuje doručovat služby z vašeho datového centra. Služby zahrnují virtuální počítače, SQL Server databáze, SharePoint, Exchange a dokonce i [Azure Marketplace položky](azure-stack-marketplace-azure-items.md). Jako poskytovatel služeb můžete nabízet služby vašim klientům. V rámci státní nebo státní instituce můžete svým zaměstnancům nabídnout místní služby.

## <a name="overview"></a>Přehled

Jako operátor centra Azure Stack můžete nakonfigurovat a doručovat služby pomocí nabídek, plánů a předplatných. Nabídky obsahují jeden nebo více plánů a každý plán zahrnuje jednu nebo více služeb, které jsou nakonfigurovány s kvótami. Díky vytvoření plánů a jejich kombinování do různých nabídek se uživatelé můžou přihlásit k odběru vašich nabídek a nasazovat prostředky. Tato struktura vám umožní spravovat tyto možnosti:

- Které služby a prostředky, ke kterým mají uživatelé přístup.
- Množství prostředků, které může uživatel spotřebovat.
- Které oblasti mají přístup k prostředkům.

Chcete-li doručovat službu, postupujte podle následujících kroků vysoké úrovně:

1. Plánování nabídky služeb pomocí:

   - Základní služby, jako jsou výpočetní prostředky, úložiště, sítě nebo Key Vault.
   - Value – přidejte služby, jako je App Service, SQL Server nebo MySQL server.

2. Vytvořte plán, který se skládá z jedné nebo více služeb. Při vytváření plánu vyberte nebo vytvořte kvóty, které definují omezení prostředků u každé služby v plánu.
3. Vytvořte nabídku, která má jeden nebo více plánů. Nabídka může zahrnovat základní plány a volitelné plány doplňku.

Po vytvoření nabídky se uživatelé můžou přihlásit k odběru služeb a nasazovat prostředky. Uživatelé se můžou přihlásit k odběru tolika nabídek, kolik jich chtějí. Následující obrázek ukazuje jednoduchý příklad uživatele, který má přihlášení k odběru dvou nabídek. Každá nabídka má plán nebo dva a každý plán poskytuje přístup ke konkrétním službám.

![Předplatné tenanta s nabídkami a plány](media/azure-stack-key-features/image4.png)

## <a name="services"></a>Služby

Můžete nabízet služby [infrastruktury jako služby](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS), které uživatelům umožňují vytvářet výpočetní infrastrukturu na vyžádání, zřídit a spravovat z portálu Azure Stack User Portal.

Můžete také nasazovat služby PaaS ( [Platform as a Service](https://azure.microsoft.com/overview/what-is-paas/) ) pro Azure Stack hub od Microsoftu a jiných poskytovatelů třetích stran. Mezi služby PaaS, které můžete poskytovat, patří mimo jiné:

- [App Service](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Server MySQL](azure-stack-mysql-resource-provider-deploy.md)

Můžete také kombinovat služby pro integraci a vytváření složitých řešení pro různé uživatele.

### <a name="quotas"></a>kvóty,

Pro lepší správu kapacity cloudu můžete použít předem nakonfigurované *kvóty*nebo vytvořit novou kvótu pro každou službu v plánu. Kvóty definují horní omezení prostředků, které může předplatné uživatele zřizovat nebo využívat. Kvóta například může uživateli umožnit vytvoření až pěti virtuálních počítačů.

> [!IMPORTANT]
> Může trvat až dvě hodiny, než budou nové kvóty dostupné na portálu User Portal nebo před tím, než se vynutila změněná kvóta.

Můžete nastavit kvóty podle oblasti. Například plán, který poskytuje výpočetní služby pro oblast A, může mít kvótu dvou virtuálních počítačů.

>[!NOTE]
>V Azure Stack Development Kit (ASDK) je k dispozici pouze jedna oblast (s názvem *místní*).

Přečtěte si další informace o [typech kvót v centru Azure Stack](azure-stack-quota-types.md).

## <a name="plans"></a>Plány

Plány představují seskupení jedné nebo více služeb. Jako operátor centra Azure Stack [vytvoříte plány](azure-stack-create-plan.md) , které nabízíte uživatelům. Vaši uživatelé se pak můžou přihlásit k odběru nabídek, aby mohli používat plány a služby, které k nim patří. Při vytváření plánů se ujistěte, že jste nastavili své kvóty, definovali základní plány a zvažte zahrnutí volitelných plánů doplňku.

### <a name="base-plan"></a>Základní plán

Při vytváření nabídky může správce služby zahrnovat základní plán. Tyto základní plány jsou standardně zahrnuté, když se uživatel přihlásí k odběru této nabídky. Když se uživatel přihlásí k odběru, má přístup ke všem poskytovatelům prostředků, které jsou uvedené v těchto základních plánech (s odpovídajícími kvótami).

### <a name="add-on-plans"></a>Plány doplňku

Plány doplňku jsou volitelné plány, které do nabídky přidáte. V předplatném nejsou ve výchozím nastavení zahrnuty plány doplňku. Doplňkové plány jsou další plány (s kvótami) dostupné v nabídce, kterou může předplatitel přidat ke svým předplatným. Můžete například nabídnout základní plán s omezenými prostředky pro zkušební verzi a plán doplňku s většími prostředky pro zákazníky, kteří se rozhodnou tuto službu přijmout.

## <a name="offers"></a>Rozsah

Nabídky jsou skupiny jednoho nebo více plánů, které vytvoříte, aby se uživatelé mohli přihlásit k jejich odběru. Například: nabídka alfa může obsahovat plán A, který poskytuje sadu výpočetních služeb a plán B, který poskytuje sadu úložišť a síťových služeb.

Když [vytvoříte nabídku](azure-stack-create-offer.md), musíte zahrnout aspoň jeden základní plán, ale můžete také vytvořit doplňky, které mohou uživatelé přidat ke svému předplatnému.

Při plánování nabídek mějte na paměti následující body:

**Nabídky zkušební verze**: pomocí nabídek zkušební verze přilákat nové uživatele, kteří pak můžou upgradovat na další služby. Pokud chcete vytvořit nabídku zkušební verze, vytvořte si malý [základní plán](service-plan-offer-subscription-overview.md#base-plan) s volitelným větším doplňkovým plánem. Alternativně můžete vytvořit zkušební nabídku skládající se z malého základního plánu a samostatnou nabídku s větším plánem průběžných plateb.

**Plánování kapacity**: může se stát, že budete mít obavy o uživatele, kteří přijímají velké objemy prostředků, a CLOG systém pro všechny uživatele. Abyste mohli zvýšit výkon, můžete [nakonfigurovat plány s](service-plan-offer-subscription-overview.md#plans) využitím kvót na Cap.

**Delegovaní zprostředkovatelé**: můžete udělit ostatním možnost vytvářet nabídky ve vašem prostředí. Pokud jste například poskytovatelem služeb, můžete tuto možnost [delegovat](azure-stack-delegated-provider.md) pro prodejce. Nebo, pokud jste organizace, můžete delegovat na jiné divize/pobočky.

## <a name="subscriptions"></a>Předplatná

Předplatná umožňují uživatelům přístup k Vašim nabídkám. Pokud jste operátorem Azure Stackového centra pro poskytovatele služeb, vaši uživatelé (klienti) si své služby kupují pomocí přihlášení k odběru vašich nabídek. Pokud jste operátor centra Azure Stack v organizaci, uživatelé (zaměstnanci) se můžou přihlásit k odběru služeb, které nabízíte bez placení.

Uživatelé vytvoří nové odběry a získají přístup k existujícím předplatným, když se přihlásí k centru Azure Stack. Každé předplatné představuje přidružení s jednou nabídkou. Nabídku (a její plány a kvóty) přiřazené k jednomu předplatnému nejde sdílet s ostatními předplatnými. Každý prostředek, který uživatel vytvoří, je přidružený k jednomu předplatnému.

### <a name="default-provider-subscription"></a>Předplatné výchozího poskytovatele

Výchozí předplatné poskytovatele se automaticky vytvoří při nasazení ASDK. Pomocí tohoto předplatného můžete spravovat centrum Azure Stack, nasazovat další poskytovatele prostředků a vytvářet plány a nabídky pro uživatele. Z důvodů zabezpečení a licencování by se nemělo používat k provádění úloh a aplikací pro zákazníky. Kvótu výchozího předplatného poskytovatele nelze změnit.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o vytváření plánů, nabídek a předplatných, začněte s [vytvořením plánu](azure-stack-create-plan.md).
