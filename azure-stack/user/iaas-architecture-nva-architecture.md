---
title: Nasazení vysoce dostupných síťových virtuálních zařízení na Azure Stack | Microsoft Docs
description: Naučte se nasadit virtuální síťová zařízení s vysokou dostupností na Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: f3a590519fcfb6b6214b202920b2756484f51dfc
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595340"
---
# <a name="deploy-highly-available-network-virtual-appliances-on-azure-stack"></a>Nasazení vysoce dostupných síťových virtuálních zařízení na Azure Stack

V tomto článku se dozvíte, jak nasadit sadu síťových virtuálních zařízení (síťová virtuální zařízení) pro zajištění vysoké dostupnosti v Azure Stack. Síťové virtuální zařízení (NVA) se obvykle používá pro řízení toku síťového provozu z hraniční sítě (označované také jako DMZ) do ostatních sítí nebo podsítí. Tento článek obsahuje příklady architektur pro pouze příchozí přenos dat, pro pouze výchozí přenos dat a pro příchozí i výchozí přenos dat.

K dispozici jsou síťová virtuální zařízení od různých dodavatelů, kteří jsou k dispozici na [Azure Stack Marketplace](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items), pro zajištění optimálního výkonu použijte jeden z nich.

Tato architektura se skládá z následujících součástí.

## <a name="networking-and-load-balancing"></a>Sítě a vyrovnávání zatížení

-   **Virtuální síť a podsítě**. Každý virtuální počítač Azure je nasazený do virtuální sítě, která se dá rozdělit do podsítí. Vytvořte pro každou vrstvu samostatnou podsíť.

-   **Load Balancer vrstvy 7.** Protože Application Gateway ještě není v Azure Stack k dispozici, jsou k dispozici alternativy na [Azure Stack trhu](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items) , jako je: [kemp LoadMaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [F5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) nebo [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Nástroje pro vyrovnávání zatížení.** Použijte [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)k distribuci síťového provozu z webové vrstvy do obchodní vrstvy a z obchodní vrstvy na SQL Server.

-   **Skupiny zabezpečení sítě** (skupin zabezpečení sítě). Použijte skupin zabezpečení sítě k omezení síťového provozu v rámci virtuální sítě. Například v níže uvedené architektuře architektury nepřijímá databázová vrstva přenos z webového front-endu, a to pouze z obchodní vrstvy a z podsítě pro správu.

-   **Udr.** Použijte [*trasy definované uživatelem*](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview/) (udr) ke směrování provozu do konkrétního nástroje pro vyrovnávání zatížení.

Tento článek předpokládá základní znalost Azure Stack sítě.

## <a name="architecture-diagrams"></a>Diagramy architektury

SÍŤOVÉ virtuální zařízení se dá nasadit do hraniční sítě v mnoha různých architekturách. Následující obrázek například znázorňuje použití jediného síťové virtuální zařízení pro příchozí přenos dat.

![Snímek obrazovky s popisem příspěvku na sociálních médiích, který se automaticky vygeneroval](./media/iaas-architecture-nva-architecture/image1.png)

V této architektuře síťové virtuální zařízení poskytuje bezpečnou hranicí sítě tím, že kontroluje veškerý příchozí a výchozí provoz v síti a předává pouze provoz, který splňuje pravidla zabezpečení sítě. Skutečnost, že veškerý síťový provoz musí projít přes síťové virtuální zařízení, znamená, že síťové virtuální zařízení je jediným bodem selhání v síti. Pokud však síťové virtuální zařízení selže, jiná cesta pro provoz neexistuje a všechny back-endové podsítě budou nedostupné.

Pokud chcete vytvořit vysoce dostupné síťové virtuální zařízení, nasaďte do skupiny dostupnosti více než jedno síťové virtuální zařízení.

Následující architektury popisují nezbytné prostředky a konfiguraci pro vysoce dostupná síťová virtuální zařízení:

| Řešení | Výhody | Požadavky |
| --- | --- | --- |
| Příchozí přenos dat se síťovými virtuálními zařízeními vrstvy 7 | Všechny uzly síťové virtuální zařízení jsou aktivní. | Vyžaduje síťové virtuální zařízení, který může ukončit připojení a použít SNAT.<br>Vyžaduje samostatnou sadu síťová virtuální zařízení pro provoz pocházející z podnikové sítě nebo z Internetu a z Azure Stack.<br>Dá se použít jenom pro přenosy, které pocházejí mimo Azure Stack.  |
| Výchozí přenos dat se síťovými virtuálními zařízeními vrstvy 7 | Všechny uzly síťové virtuální zařízení jsou aktivní. | Vyžaduje síťové virtuální zařízení, který může ukončit připojení a implementuje překlad adresy zdrojového v síti (SNAT). |
| Příchozí přenos dat s vrstvou 7 Síťová virtuální zařízení | Všechny uzly jsou aktivní.<br>Schopnost zpracovat provoz pocházející z Azure Stack. | Vyžaduje síťové virtuální zařízení, který může ukončit připojení a použít SNAT.<br>Vyžaduje samostatnou sadu síťová virtuální zařízení pro provoz pocházející z podnikové sítě nebo z Internetu a z Azure Stack. |

## <a name="ingress-with-layer-7-nvas"></a>Příchozí přenos dat se síťovými virtuálními zařízeními vrstvy 7

Následující obrázek ukazuje architekturu s vysokou dostupností, která implementuje hraniční síť příchozího přenosu dat za internetovým nástrojem pro vyrovnávání zatížení. Tato architektura je navržená tak, aby poskytovala připojení k Azure Stack úlohám pro přenos vrstvy 7, jako je HTTP nebo HTTPS:

![Automaticky vygeneroval se snímek obrazovky s popisem mapy.](./media/iaas-architecture-nva-architecture/image2.png)

Výhodou této architektury je, že všechna síťová virtuální zařízení jsou aktivní, a pokud jedno z nich selže, nástroj pro vyrovnávání zatížení bude směrovat provoz na jiné síťové virtuální zařízení. Obě síťová virtuální zařízení směrují provoz na interní nástroj pro vyrovnávání zatížení, takže dokud je jedno síťové virtuální zařízení aktivní, tok provozu může pokračovat. Síťová virtuální zařízení mají povinnost ukončit provoz protokolu SSL určeného pro virtuální počítače webové vrstvy. Tyto síťová virtuální zařízení se nedají rozšířit tak, aby zpracovávala síťový provoz v podniku, protože provoz podnikové sítě vyžaduje jinou vyhrazenou sadu síťová virtuální zařízení s vlastními síťovými trasami.

## <a name="egress-with-layer-7-nvas"></a>Výchozí přenos dat se síťovými virtuálními zařízeními vrstvy 7

Možnost příchozího přenosu s architekturou síťová virtuální zařízení vrstvy 7 se dá rozšířit tak, aby poskytovala výstupní hraniční síť pro požadavky, které pocházejí z Azure Stack úlohy. Následující architektura je navržena tak, aby poskytovala vysokou dostupnost síťová virtuální zařízení v hraniční síti pro přenos vrstvy 7, jako je například HTTP nebo HTTPS:

![Automaticky se vygeneroval snímek obrazovky s popisem mobilního telefonu.](./media/iaas-architecture-nva-architecture/image3.png)

V této architektuře je veškerý provoz pocházející z Azure Stack směrován do interního nástroje pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení distribuuje odchozí žádosti mezi sadu síťových virtuálních zařízení. Tato síťová virtuální zařízení směrují pomocí jednotlivých veřejných IP adres provoz na internet.

## <a name="ingress-egress-with-layer-7--nvas"></a>Příchozí přenos dat s vrstvou 7 Síťová virtuální zařízení

V obou architekturách příchozího a odchozího přenosu dat existovala samostatná hraniční síť pro příchozí a odchozí přenosy. Následující architektura ukazuje, jak vytvořit hraniční síť, která se dá použít pro příchozí i výstupní přenos dat vrstvy 7, jako je HTTP nebo HTTPS:

![Snímek obrazovky s popisem příspěvku na sociálních médiích, který se automaticky vygeneroval](./media/iaas-architecture-nva-architecture/image4.png)

V příchozím přenosu s architekturou vrstvy 7 Síťová virtuální zařízení proces síťová virtuální zařízení zpracovává příchozí požadavky z vrstvy 7 Load Balancer. Síťová virtuální zařízení v back-endovém fondu nástroje pro vyrovnávání zatížení zpracovávají také odchozí požadavky z úloh virtuálních počítačů. Vzhledem k tomu, že příchozí provoz je směrován pomocí nástroje pro vyrovnávání zatížení vrstvy 7 a odchozí přenosy jsou směrovány pomocí služby SLB (Azure Stack Basic Load Balancer), jsou za udržování spřažení relací zodpovědné síťová virtuální zařízení. To znamená, že nástroj pro vyrovnávání zatížení vrstvy 7 udržuje mapování příchozích a odchozích požadavků, aby mohl přesměrovat správnou odpověď původnímu žadateli. Interní nástroj pro vyrovnávání zatížení však nemá přístup k mapování nástroje pro vyrovnávání zatížení vrstvy 7 a pomocí vlastní logiky odesílá odpovědi do síťová virtuální zařízení. Je možné, že nástroj pro vyrovnávání zatížení pošle odpověď na síťové virtuální zařízení, která původně neobdržela žádost z nástroje pro vyrovnávání zatížení vrstvy 7. V takovém případě musí síťová virtuální zařízení komunikovat a přenášet odpověď mezi nimi, aby správně síťové virtuální zařízení mohla předat odpověď do nástroje pro vyrovnávání zatížení vrstvy 7.

> [!Note]  
> Problém s asymetrickým směrováním můžete také vyřešit tím, že zajistíte, aby síťová virtuální zařízení prováděla příchozí překlad adres na základě stroje (SNAT). Tak by se původní zdrojová IP adresa žadatele nahradila jednou z IP adres, kterou síťové virtuální zařízení použilo u příchozího toku. To zajistí, že budete moct použít více síťových virtuálních zařízení najednou a zároveň zachováte symetrické směrování.

## <a name="next-steps"></a>Další kroky

- Další informace o Azure Stack virtuálních počítačů najdete v tématu [Azure Stack funkce virtuálních počítačů](azure-stack-vm-considerations.md).  
- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](https://docs.microsoft.com/azure/architecture/patterns).
