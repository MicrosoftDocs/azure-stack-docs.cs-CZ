---
title: DNS v centru Azure Stack | Microsoft Docs
description: Přečtěte si o DNS v centru Azure Stack a vytváření a správě zón DNS.
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
ms.date: 10/03/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 239c65ca430140fda3d53be543e2f976ee57f588
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820436"
---
# <a name="use-dns-in-azure-stack-hub"></a>Použití DNS v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Centrum Azure Stack podporuje následující funkce Azure DNS:

* Překlad názvů hostitelů DNS.
* Vytvářejte a spravujte záznamy a zóny DNS pomocí rozhraní API.

## <a name="support-for-dns-hostname-resolution"></a>Podpora překladu názvů hostitelů DNS

Pro prostředky veřejné IP adresy můžete zadat popisek názvu domény DNS. Azure Stack hub používá **domainnamelabel. Location. cloudapp. azurestack. external** pro název popisku a mapuje ho na veřejnou IP adresu v serverech DNS spravovaných rozbočovačem Azure Stack.

Pokud například vytvoříte prostředek veřejné IP adresy se společností **Contoso** jako popisek názvu domény v umístění místního centra Azure Stack, [plně kvalifikovaný název domény (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) **contoso. Local. cloudapp. azurestack. external** se přeloží na veřejnou IP adresu prostředku. Tento plně kvalifikovaný název domény můžete použít k vytvoření vlastního záznamu CNAME domény, který odkazuje na veřejnou IP adresu v centru Azure Stack.

Další informace o překladu názvů najdete v článku věnovaném [překladu DNS](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

> [!IMPORTANT]
> Jednotlivé štítky s názvem domény, které vytvoříte, musí být jedinečné v rámci svého umístění centra Azure Stack.

Následující snímek obrazovky ukazuje dialog **vytvořit veřejnou IP adresu** pro vytvoření veřejné IP adresy pomocí portálu:

![Vytvořit veřejnou IP adresu](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Příklad scénáře

Máte k dispozici nástroj pro vyrovnávání zatížení, který zpracovává požadavky z webové aplikace. Za nástrojem pro vyrovnávání zatížení je web, který běží na jednom nebo několika virtuálních počítačích. K webu s vyrovnáváním zatížení můžete přistupovat pomocí názvu DNS místo IP adresy.

## <a name="create-and-manage-dns-zones-and-records-using-the-apis"></a>Vytváření a Správa zón DNS a záznamů pomocí rozhraní API

Můžete vytvářet a spravovat zóny a záznamy DNS v centru Azure Stack.

Služba Azure Stack hub poskytuje službu DNS podobnou Azure a používá rozhraní API, která jsou konzistentní s rozhraními API Azure DNS.  Hostováním domén v rámci služby DNS centra Azure Stack můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů. Můžete také použít stejnou fakturaci a podporu jako vaše další služby Azure.

Infrastruktura služby DNS centra Azure Stack je kompaktnější než Azure. Velikost a umístění nasazení centra Azure Stack ovlivňují rozsah DNS, škálování a výkon. To také znamená, že výkon, dostupnost, globální distribuce a vysoká dostupnost se mohou lišit od nasazení po nasazení.

## <a name="comparison-with-azure-dns"></a>Porovnání s Azure DNS

Služba DNS v centru Azure Stack se podobá DNS v Azure, ale existuje několik důležitých výjimek:

* Nepodporuje **záznamy AAAA**: Azure Stack centrum nepodporuje záznamy AAAA, protože centrum Azure Stack nepodporuje adresy IPv6. Jedná se o klíčový rozdíl mezi DNS v Azure a centra Azure Stack.

* Není **Vícenásobný tenant**: Služba DNS v centru Azure Stack není víceklientské. Klienti nemůžou vytvořit stejnou zónu DNS. Pouze první předplatné, které se pokouší vytvořit zónu úspěšně, a pozdější požadavky selžou. Jedná se o další klíčový rozdíl mezi Azure a službou DNS centra Azure Stack.

* **Značky, metadata a značky ETag**: existují drobné rozdíly ve způsobu, jakým Azure Stack centrum zpracovává značky, metadata, značky ETag a omezení.

Další informace o Azure DNS najdete v tématu [zóny a záznamy DNS](/azure/dns/dns-zones-records).

### <a name="tags"></a>Značky

Služba DNS centra Azure Stack podporuje použití značek Azure Resource Manager v prostředcích zóny DNS. Nepodporuje značky pro sady záznamů DNS. Jako alternativu jsou **metadata** pro sady záznamů DNS podporována, jak je vysvětleno v další části.

### <a name="metadata"></a>Metadata

Jako alternativu k značkám sady záznamů Azure Stack centrum DNS podporuje přidávání záznamů do sad záznamů pomocí *metadat*. Podobně jako u značek vám metadata umožňují přidružit páry název-hodnota ke každé sadě záznamů. Metadata jsou například užitečná pro záznam účelu každé sady záznamů. Na rozdíl od značek nemůžete použít metadata k poskytnutí filtrovaného zobrazení vaší faktury Azure a metadata nejde zadat v zásadách Azure Resource Manager.

### <a name="etags"></a>Značek entit

Předpokládejme, že se dva lidé nebo dva procesy pokusí změnit záznam DNS současně. Kterou jednu službu WINS? A ví, že přepsané změny vytvořil někdo jiný?

Služba DNS centra Azure Stack používá *značky ETag* k bezpečnému zpracování souběžných změn stejného prostředku. Značky ETag se liší od Azure Resource Manager *značek*. K každému prostředku DNS (zóně nebo sadě záznamů) je přidružená značka ETag. Při načtení prostředku je načtena také značka ETag. Když provedete aktualizaci prostředku, můžete se rozhodnout, že se má převrátit značka ETag, aby služba DNS centra Azure Stack mohla ověřit, jestli se na serveru shodují. Vzhledem k tomu, že každá aktualizace prostředku má za následek opětovné vygenerování značky ETag, neshoda značek ETag indikuje, že došlo k souběžné změně. Značky ETag lze také použít při vytváření nového prostředku, abyste zajistili, že prostředek ještě neexistuje.

Ve výchozím nastavení používají rutiny prostředí PowerShell služby DNS centra Azure Stack k blokování souběžných změn zón a sad záznamů pomocí značek ETag. Pro potlačení kontrol značek ETag můžete použít volitelný `-Overwrite` přepínač. Bez kontrol ETag nejsou všechny souběžné změny, ke kterým došlo, přepsány.

Na úrovni REST API DNS centra Azure Stack jsou značky ETag zadány pomocí hlaviček protokolu HTTP. Jejich chování je popsané v následující tabulce:

| Hlavička | Chování|
|--------|---------|
| Žádné   | VLOŽENÍ vždy proběhne úspěšně (žádné kontroly ETag).|
| If-Match| VLOŽENÍ se zdaří pouze v případě, že prostředek existuje a odpovídá ETag.|
| If-Match *| Pokud prostředek existuje, operace PUT se podaří.|
| If-None-Match *| Pokud prostředek neexistuje, operace PUT se podaří.|

### <a name="limits"></a>Omezení

Při použití služby DNS centra Azure Stack se použijí následující výchozí omezení:

| Prostředek| Výchozí omezení|
|---------|--------------|
| Zóny na předplatné| 100|
| Sady záznamů na zónu| 5000|
| Počet záznamů na sadu záznamů| 20|

## <a name="next-steps"></a>Další kroky

* [Představujeme IDN pro centrum Azure Stack](azure-stack-understanding-dns.md)
