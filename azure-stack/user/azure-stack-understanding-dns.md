---
title: Použití IDN v Azure Stack | Microsoft Docs
description: Naučte se používat funkce a možnosti IDN v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 6dda8562e47f17c97da5e0597a2ed88865bc6425
ms.sourcegitcommit: 82d09bbae3e5398d2fce7e2f998dfebff018716c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68497842"
---
# <a name="use-idns-in-azure-stack"></a>Použití IDN v Azure Stack 

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

IDN je funkce Azure Stack sítě, která umožňuje překládat externí názvy DNS (například https:\//www.Bing.com.) Umožňuje taky registraci interních názvů virtuálních sítí. Provedete to tak, že virtuální počítače ve stejné virtuální síti vyřešíte spíše pomocí názvu než IP adresy. Tento přístup odebere nutnost zadat vlastní položky serveru DNS. Další informace o DNS najdete v [přehledu Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Co IDN dělat?

Pomocí IDN v Azure Stack získáte následující možnosti, aniž byste museli zadávat vlastní položky serveru DNS:

- Sdílené služby překladu názvů DNS pro zatížení klientů.
- Autoritativní služba DNS pro překlad názvů a registraci DNS v rámci virtuální sítě tenanta
- Rekurzivní služba DNS pro rozlišení internetových názvů z virtuálních počítačů klientů. Klienti již nemusí určovat vlastní záznamy DNS pro překlad internetových názvů (například www\.Bing.com).

Pořád můžete využívat vlastní DNS a používat vlastní servery DNS. Pomocí IDN ale můžete přeložit internetové názvy DNS a připojit se k ostatním virtuálním počítačům ve stejné virtuální síti, aniž byste museli vytvářet vlastní záznamy DNS.

## <a name="what-doesnt-idns-do"></a>Co to IDN dělat?

IDN neumožňuje vytvořit záznam DNS pro název, který se dá přeložit mimo virtuální síť.

V Azure máte možnost zadat popisek názvu DNS, který je přidružený k veřejné IP adrese. Můžete zvolit popisek (předponu), ale Azure zvolí příponu, která je založená na oblasti, ve které vytvoříte veřejnou IP adresu.

![Příklad popisku názvu DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Jak ukazuje předchozí obrázek, Azure vytvoří záznam "A" v DNS pro popisek názvu DNS zadaný v rámci zóny **westus.cloudapp.Azure.com**. Předpona a přípona jsou kombinovány pro vytvoření [plně kvalifikovaného názvu domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), který lze přeložit odkudkoli na veřejném Internetu.

Azure Stack podporuje jenom IDN pro registraci interních názvů, takže nemůže dělat následující věci:

- Vytvořte záznam DNS v existující hostované zóně DNS (například Local. azurestack. external.).
- Vytvořte zónu DNS (například Contoso.com.)
- Vytvořte záznam v rámci vlastní zóny DNS.
- Podpora nákupu názvů domén.

## <a name="next-steps"></a>Další postup

[Použití DNS ve službě Azure Stack](azure-stack-dns.md)
