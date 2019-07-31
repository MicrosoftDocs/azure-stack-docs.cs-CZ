---
title: DNS v Azure Stack | Microsoft Docs
description: Přečtěte si o DNS v Azure Stack a o tom, jak vytvářet a spravovat zóny DNS.
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
ms.date: 06/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 8bfe15ad19e4aaec45492aa98cfb2ef02294742a
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658472"
---
# <a name="use-dns-in-azure-stack"></a>Použít DNS v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack podporuje následující funkce Azure DNS:

* Překlad názvů hostitelů DNS.
* Vytvářejte a spravujte záznamy a zóny DNS pomocí rozhraní API.

## <a name="support-for-dns-hostname-resolution"></a>Podpora překladu názvů hostitelů DNS

Pro prostředky veřejné IP adresy můžete zadat popisek názvu domény DNS. Azure Stack používá **domainnamelabel. Location. cloudapp. azurestack. external** pro název popisku a mapuje ho na veřejnou IP adresu v serverech DNS spravovaných Azure Stack.

Pokud například vytvoříte prostředek veřejné IP adresy se společností **Contoso** jako popisek názvu domény v umístění místní Azure Stack, [plně kvalifikovaný název domény (FQDN)](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) **contoso. Local. cloudapp. azurestack. external** se přeloží na veřejnou IP adresu. Adresa prostředku. Tento plně kvalifikovaný název domény můžete použít k vytvoření vlastního záznamu CNAME domény, který odkazuje na veřejnou IP adresu v Azure Stack.

Další informace o překladu názvů najdete v článku věnovaném [překladu DNS](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

> [!IMPORTANT]
> Jednotlivé štítky s názvem domény, které vytvoříte, musí být jedinečné v rámci svého Azure Stackho umístění.

Následující snímek obrazovky ukazuje dialog **vytvořit veřejnou IP adresu** pro vytvoření veřejné IP adresy pomocí portálu:

![Vytvoření veřejné IP adresy](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>Příklad scénáře

Máte k dispozici nástroj pro vyrovnávání zatížení, který zpracovává požadavky z webové aplikace. Za nástrojem pro vyrovnávání zatížení je web, který běží na jednom nebo několika virtuálních počítačích. K webu s vyrovnáváním zatížení můžete přistupovat pomocí názvu DNS místo IP adresy.

## <a name="create-and-manage-dns-zones-and-records-using-the-api"></a>Vytváření a Správa záznamů a zón DNS pomocí rozhraní API

V Azure Stack můžete vytvářet a spravovat zóny a záznamy DNS.

Azure Stack poskytuje službu DNS podobnou Azure a používá rozhraní API, která jsou konzistentní s rozhraními API Azure DNS.  Díky hostování domén ve službě Azure Stack DNS můžete spravovat záznamy DNS pomocí stejných přihlašovacích údajů, rozhraní API a nástrojů. Můžete také použít stejnou fakturaci a podporu jako vaše další služby Azure.

Azure Stack infrastruktura DNS je kompaktnější než Azure. Velikost a umístění nasazení Azure Stack ovlivňují rozsah DNS, škálování a výkon. To také znamená, že výkon, dostupnost, globální distribuce a vysoká dostupnost se mohou lišit od nasazení po nasazení.

## <a name="comparison-with-azure-dns"></a>Porovnání s Azure DNS

Služba DNS v Azure Stack je podobná DNS v Azure, ale existuje několik důležitých výjimek:

* **Nepodporuje záznamy AAAA**: Azure Stack nepodporuje záznamy AAAA, protože Azure Stack nepodporuje adresy IPv6. Jedná se o klíčový rozdíl mezi DNS v Azure a Azure Stack.

* **Není více tenantů**: Služba DNS v Azure Stack není víceklientské. Klienti nemůžou vytvořit stejnou zónu DNS. Pouze první předplatné, které se pokouší vytvořit zónu úspěšně, a pozdější požadavky selžou. Jedná se o další klíčový rozdíl mezi Azure a Azure Stack DNS.

* **Značky, metadata a značky ETag**: Existují drobné rozdíly ve způsobu, jakým Azure Stack zpracovává značky, metadata, značky ETag a omezení.

Další informace o Azure DNS najdete v tématu [zóny a záznamy DNS](/azure/dns/dns-zones-records).

### <a name="tags"></a>Tags

Azure Stack DNS podporuje použití značek Azure Resource Manager v prostředcích zóny DNS. Nepodporuje značky pro sady záznamů DNS. Jako alternativu jsou **metadata** pro sady záznamů DNS podporována, jak je vysvětleno v další části.

### <a name="metadata"></a>Metadata

Jako alternativu k značkám sady záznamů Azure Stack DNS podporuje přidávání poznámek do sad záznamů pomocí *metadat*. Podobně jako u značek vám metadata umožňují přidružit páry název-hodnota ke každé sadě záznamů. Metadata jsou například užitečná pro záznam účelu každé sady záznamů. Na rozdíl od značek nemůžete použít metadata k poskytnutí filtrovaného zobrazení vaší faktury Azure a metadata nejde zadat v zásadách Azure Resource Manager.

### <a name="etags"></a>Značek entit

Předpokládejme, že se dva lidé nebo dva procesy pokusí změnit záznam DNS současně. Kterou jednu službu WINS? A ví, že přepsané změny vytvořil někdo jiný?

Azure Stack DNS používá *značky ETag* k bezpečnému zpracování souběžných změn stejného prostředku. Značky ETag se liší od Azure Resource Manager *značek*. K každému prostředku DNS (zóně nebo sadě záznamů) je přidružená značka ETag. Při načtení prostředku je načtena také značka ETag. Když aktualizujete prostředek, můžete se rozhodnout, že se má převrátit značka ETag, aby Azure Stack DNS mohlo ověřit, jestli se shoduje se značkou ETag na serveru. Vzhledem k tomu, že každá aktualizace prostředku má za následek opětovné vygenerování značky ETag, neshoda značek ETag indikuje, že došlo k souběžné změně. Značky ETag se dají použít taky při vytváření nového prostředku, abyste se ujistili, že prostředek ještě neexistuje.

Ve výchozím nastavení Azure Stack rutiny prostředí PowerShell služby DNS pomocí značek ETag zablokují souběžné změny zón a sad záznamů. Můžete použít volitelný `-Overwrite` přepínač pro potlačení kontrol značek ETag. Bez kontrol ETag nejsou všechny souběžné změny, ke kterým došlo, přepsány.

Na úrovni Azure Stack DNS REST API jsou značky ETag zadány pomocí hlaviček protokolu HTTP. Jejich chování je popsané v následující tabulce:

| Záhlaví | Chování|
|--------|---------|
| Žádný   | VLOŽENÍ vždy proběhne úspěšně (žádné kontroly ETag).|
| If-Match| VLOŽENÍ se zdaří pouze v případě, že prostředek existuje a odpovídá ETag.|
| If-Match *| Pokud prostředek existuje, operace PUT se podaří.|
| If-None-Match *| Pokud prostředek neexistuje, operace PUT se podaří.|

### <a name="limits"></a>Omezení

Při použití Azure Stack DNS platí následující výchozí omezení:

| Resource| Výchozí omezení|
|---------|--------------|
| Zóny na předplatné| 100|
| Sady záznamů na zónu| 5000|
| Počet záznamů na sadu záznamů| 20|

## <a name="next-steps"></a>Další postup

* [Představujeme iDNS pro Azure Stack](azure-stack-understanding-dns.md)
