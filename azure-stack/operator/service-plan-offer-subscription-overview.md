---
title: Přehled služby Azure Stack, plán, nabídka, předplatné
description: Jako operátor cloudu chci pochopit Azure Stack služeb, plánů, nabídek a předplatných.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 10/01/2019
ms.openlocfilehash: 3559dd6bb684ef3a1ab1c90efcc3dfc814c811f3
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72287111"
---
# <a name="service-plan-offer-subscription-overview"></a>Přehled služeb, plánů, nabídek, předplatných

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

[Microsoft Azure Stack](azure-stack-overview.md) je hybridní cloudová platforma, která umožňuje doručovat služby z vašeho datového centra. Služby, jako jsou virtuální počítače, SQL Server databáze, SharePoint, Exchange a dokonce i [Azure Marketplace položky](azure-stack-marketplace-azure-items.md). Jako poskytovatel služeb můžete nabízet služby vašim klientům. V rámci státní nebo státní instituce můžete svým zaměstnancům nabídnout místní služby.

## <a name="overview"></a>Přehled

Jako operátor Azure Stack konfigurujete a doručujete služby pomocí nabídek, plánů a předplatných. Nabídky obsahují jeden nebo více plánů a každý plán zahrnuje jednu nebo více služeb, které jsou nakonfigurovány s kvótami. Díky vytvoření plánů a jejich kombinování do různých nabídek se uživatelé můžou přihlásit k odběru vašich nabídek a nasazovat prostředky. To vám umožní spravovat tyto možnosti:

- Které služby a prostředky, ke kterým mají uživatelé přístup.
- Množství prostředků, které může uživatel spotřebovat.
- Které oblasti mají přístup k prostředkům.

Chcete-li doručovat službu, postupujte podle těchto kroků vysoké úrovně:

1. Plánování nabídky služeb pomocí:
   - Základní služby, jako jsou výpočetní prostředky, úložiště, sítě nebo Key Vault.
   - Služby doplňku, jako je App Service, SQL Server nebo MySQL server.
2. Vytvořte plán, který se skládá z jedné nebo více služeb. Při vytváření plánu vyberte nebo vytvořte kvóty, které definují omezení prostředků u každé služby v plánu.
3. Vytvořte nabídku, která obsahuje jeden nebo více plánů. Nabídka může zahrnovat základní plány a volitelné plány doplňku.

Po vytvoření nabídky se uživatelé můžou přihlásit k odběru služeb, které nabízí, a nasadit prostředky. Uživatelé se můžou přihlásit k odběru tolika nabídek, kolik jich chtějí. Následující obrázek ukazuje jednoduchý příklad uživatele, který má přihlášení k odběru dvou nabídek. Každá nabídka má plán nebo dva a každý plán poskytuje přístup ke konkrétním službám.

![Předplatné tenanta s nabídkami a plány](media/azure-stack-key-features/image4.png)

## <a name="services"></a>Služby

Můžete nabízet služby [infrastruktury jako služby](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS), které uživatelům umožňují vytvářet výpočetní infrastrukturu na vyžádání, zřídit a spravovat z portálu Azure Stack User Portal.

Můžete také nasadit služby [Platform as a Service](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) pro Azure Stack od Microsoftu a jiných poskytovatelů třetích stran. Mezi služby PaaS, které můžete poskytovat, patří mimo jiné:

- [App Service](azure-stack-app-service-overview.md)
- [SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Server MySQL](azure-stack-mysql-resource-provider-deploy.md)

Můžete také kombinovat služby pro integraci a vytváření složitých řešení pro různé uživatele.

### <a name="quotas"></a>Rovnoměrně

Abyste mohli spravovat kapacitu cloudu, můžete využít předem nakonfigurované *kvóty*nebo vytvořit novou kvótu pro každou službu v plánu. Kvóty definují horní omezení prostředků, které může předplatné uživatele zřizovat nebo využívat. Kvóta může například uživateli dovolit vytvořit až pět virtuálních počítačů (VM).

> [!IMPORTANT]
> Může trvat až dvě hodiny, než budou nové kvóty dostupné na portálu User Portal nebo před tím, než se vynutila změněná kvóta.

Můžete nakonfigurovat kvóty podle oblasti. Například plán, který poskytuje výpočetní služby pro oblast A, může mít kvótu dvou virtuálních počítačů.

>[!NOTE]
>V Azure Stack Development Kit je k dispozici pouze jedna oblast (s názvem *místní*).

Přečtěte si další informace o [typech kvót v Azure Stack](azure-stack-quota-types.md).

## <a name="plans"></a>Plánují

Plány jsou seskupení jedné nebo více služeb. Jako operátor Azure Stack [vytvoříte plány](azure-stack-create-plan.md) , které nabídnete vašim uživatelům. Uživatelé si pak můžou předplatit vaše nabídky, aby mohli používat plány a služby, které obsahují. Při vytváření plánů se ujistěte, že jste nastavili své kvóty, definovali základní plány a zvažte zahrnutí volitelných plánů doplňku.

### <a name="base-plan"></a>Základní plán

Při vytváření nabídky může správce služby zahrnovat základní plán. Tyto základní plány jsou standardně zahrnuté, když se uživatel přihlásí k odběru této nabídky. Když se uživatel přihlásí k odběru, má přístup ke všem poskytovatelům prostředků, které jsou uvedené v těchto základních plánech (s odpovídajícími kvótami).

### <a name="add-on-plans"></a>Plány doplňku

Plány doplňku jsou volitelné plány, které do nabídky přidáte. V předplatném nejsou ve výchozím nastavení zahrnuty plány doplňku. Doplňkové plány jsou další plány (s kvótami) dostupné v nabídce, kterou může předplatitel přidat ke svým předplatným. Můžete například nabídnout základní plán s omezenými prostředky pro zkušební verzi a plán doplňku s většími prostředky pro zákazníky, kteří se rozhodnou tuto službu přijmout.

## <a name="offers"></a>Rozsah

Nabídky jsou skupiny jednoho nebo více plánů, které vytvoříte, aby se uživatelé mohli přihlásit k jejich odběru. Například nabídka alfa může obsahovat plán A, který poskytuje sadu výpočetních služeb a plán B, který poskytuje sadu úložišť a síťových služeb.

Když [vytvoříte nabídku](azure-stack-create-offer.md), musíte zahrnout aspoň jeden základní plán, ale můžete také vytvořit doplňky, které mohou uživatelé přidat ke svému předplatnému.

Při plánování nabídek mějte na paměti následující body:

**Nabídky zkušební verze**: pomocí nabídek zkušební verze přilákat nové uživatele, kteří pak můžou upgradovat na další služby. Pokud chcete vytvořit nabídku zkušební verze, vytvořte si malý [základní plán](service-plan-offer-subscription-overview.md#base-plan) s volitelným větším doplňkovým plánem. Alternativně můžete vytvořit zkušební nabídku skládající se z malého základního plánu a samostatnou nabídku s větším plánem průběžných plateb.

**Plánování kapacity**: můžete se rozhodnout o uživatelích, kteří přijímají velké objemy prostředků, a zajímat systém pro všechny uživatele. Abyste mohli zvýšit výkon, můžete [nakonfigurovat plány s](service-plan-offer-subscription-overview.md#plans) využitím kvót na Cap.

**Delegovaní zprostředkovatelé**: můžete udělit ostatním možnost vytvářet nabídky ve vašem prostředí. Pokud jste například poskytovatelem služeb, můžete tuto možnost [delegovat](azure-stack-delegated-provider.md) pro prodejce. Nebo, pokud jste organizace, můžete delegovat na jiné divize/pobočky.

## <a name="subscriptions"></a>Předplatná

Předplatná umožňují uživatelům přístup k Vašim nabídkám. Pokud jste operátor Azure Stack pro poskytovatele služeb, vaši uživatelé (klienti) si své služby kupují pomocí přihlášení k odběru vašich nabídek. Pokud jste operátor Azure Stack v organizaci, uživatelé (zaměstnanci) se můžou přihlásit k odběru služeb, které nabízíte bez placení. 

Uživatelé si můžou vytvořit nová předplatná a získat přístup k existujícím předplatným, a to přihlášením k Azure Stack. Každé předplatné představuje přidružení s jednou nabídkou. Nabídku (a její plány a kvóty) přiřazené k jednomu předplatnému nelze sdílet s ostatními předplatnými. Každý prostředek, který uživatel vytvoří, je přidružený k jednomu předplatnému.

### <a name="default-provider-subscription"></a>Předplatné výchozího poskytovatele

Výchozí předplatné poskytovatele se automaticky vytvoří při nasazení Azure Stack Development Kit. Pomocí tohoto předplatného můžete spravovat Azure Stack, nasazovat další poskytovatele prostředků a vytvářet plány a nabídky pro uživatele. Z důvodů zabezpečení a licencování by se nemělo používat ke spouštění úloh a aplikací zákazníka. Kvótu výchozího předplatného poskytovatele nelze změnit.

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o vytváření plánů, nabídek a předplatných, začněte s [vytvořením plánu](azure-stack-create-plan.md).
