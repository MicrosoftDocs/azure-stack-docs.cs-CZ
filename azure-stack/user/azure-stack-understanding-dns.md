---
title: Použití IDN v centru Azure Stack | Microsoft Docs
description: Naučte se používat funkce a možnosti IDN v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: Justinha
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 8bdadb4e0cd36c6e650b001585aa3519293a9247
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883248"
---
# <a name="use-idns-in-azure-stack-hub"></a>Použití IDN v centru Azure Stack 

IDN je funkce sítě centra pro Azure Stack, která umožňuje překládat externí názvy DNS (například https:\//www.bing.com.) Umožňuje taky registraci interních názvů virtuálních sítí. Provedete to tak, že virtuální počítače ve stejné virtuální síti vyřešíte spíše pomocí názvu než IP adresy. Tento přístup odebere nutnost zadat vlastní položky serveru DNS. Další informace o DNS najdete v [přehledu Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview).

## <a name="what-does-idns-do"></a>Co IDN dělat?

Pomocí IDN v centru Azure Stack získáte následující možnosti, aniž byste museli zadávat vlastní položky serveru DNS:

- Sdílené služby překladu názvů DNS pro zatížení klientů.
- Autoritativní služba DNS pro překlad názvů a registraci DNS v rámci virtuální sítě tenanta
- Rekurzivní služba DNS pro rozlišení internetových názvů z virtuálních počítačů klientů. Klienti již nemusí určovat vlastní záznamy DNS pro překlad internetových názvů (například www\.bing.com.)

Pořád můžete využívat vlastní DNS a používat vlastní servery DNS. Pomocí IDN ale můžete přeložit internetové názvy DNS a připojit se k ostatním virtuálním počítačům ve stejné virtuální síti, aniž byste museli vytvářet vlastní záznamy DNS.

## <a name="what-doesnt-idns-do"></a>Co to IDN dělat?

IDN neumožňuje vytvořit záznam DNS pro název, který se dá přeložit mimo virtuální síť.

V Azure máte možnost zadat popisek názvu DNS, který je přidružený k veřejné IP adrese. Můžete zvolit popisek (předponu), ale Azure zvolí příponu, která je založená na oblasti, ve které vytvoříte veřejnou IP adresu.

![Příklad popisku názvu DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Jak ukazuje předchozí obrázek, Azure vytvoří záznam "A" v DNS pro popisek názvu DNS zadaný v rámci zóny **westus.cloudapp.Azure.com**. Předpona a přípona jsou kombinovány pro vytvoření [plně kvalifikovaného názvu domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN), který lze přeložit odkudkoli na veřejném Internetu.

Rozbočovač Azure Stack podporuje jenom IDN pro registraci interních názvů, takže nemůže dělat tyto věci:

- Vytvořte záznam DNS v existující hostované zóně DNS (například Local. azurestack. external.).
- Vytvořte zónu DNS (například Contoso.com.)
- Vytvořte záznam v rámci vlastní zóny DNS.
- Podpora nákupu názvů domén.

## <a name="demo-of-how-idns-works"></a>Ukázka fungování IDN

Všechny názvy hostitelů virtuálních počítačů ve virtuálních sítích se ukládají jako záznamy o prostředcích DNS ve stejné zóně, ale v jejich vlastním jedinečném oddílu definovaném jako identifikátor GUID, který koreluje s ID virtuální sítě v infrastruktuře SDN, pro kterou byl virtuální počítač nasazený. Plně kvalifikované názvy domén virtuálních počítačů tenanta (FQDN) se skládají z názvu počítače a řetězce přípony DNS pro Virtual Network ve formátu GUID.

<!--- what does compartment mean? Add a screenshot? can we clarify what we mean by host name and computer name. the description doesn't match the example in the table.--->
 
Toto je jednoduché prostředí, které předvádí, jak to funguje. Vytvořili jsme 3 virtuální počítače pro jednu virtuální síť a další virtuální počítač v samostatné virtuální síti:

<!--- Is DNS Label the right term? If so, we should define it. The column lists FQDNs, afaik. Where does the domain suffix come from? --->
 
|Virtuální počítač    |Sítě    |Privátní IP adresa   |Veřejná IP adresa    | Název DNS                                |
|------|--------|-------------|-------------|------------------------------------------|
|VIRTUÁLNÍ POČÍTAČ – A1 |Partnerském   | 10.0.0.5    |172.31.12.68 |VM-a1-Label. lnv1. cloudapp. azscss. external |
|VIRTUÁLNÍ POČÍTAČ – A2 |Partnerském   | 10.0.0.6    |172.31.12.76 |VM-a2-Label. lnv1. cloudapp. azscss. external |
|VIRTUÁLNÍ POČÍTAČ – A3 |Partnerském   | 10.0.0.7    |172.31.12.49 |VM-a3-Label. lnv1. cloudapp. azscss. external |
|VIRTUÁLNÍ POČÍTAČ – B1 |VNetB   | 10.0.0.4    |172.31.12.57 |VM-B1-Label. lnv1. cloudapp. azscss. external |
 
 
|VNet  |GUID                                 |Řetězec přípony DNS                                                  |
|------|-------------------------------------|-------------------------------------------------------------------|
|Partnerském |e71e1db5-0a38-460d-8539-705457a4cf75 |e71e1db5-0a38-460d-8539-705457a4cf75. Internal. lnv1. azurestack. Local|
|VNetB |e8a6e386-bc7a-43e1-a640-61591b5c76dd |e8a6e386-bc7a-43e1-a640-61591b5c76dd. Internal. lnv1. azurestack. Local|
 
 
Pokud chcete lépe pochopit, jak IDN funguje, můžete provést několik testů překladu názvů:

<!--- why Linux?--->

Z virtuálního počítače – a1 (virtuální počítač Linux): vyhledává se virtuální počítač – a2. Můžete vidět, že se přidala přípona DNS pro partnerském a název se přeloží na soukromou IP adresu:
 
```console
carlos@VM-A1:~$ nslookup VM-A2
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2.e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local
Address: 10.0.0.6
```
 
Vyhledává se virtuální počítač-a2-Label bez zadání plně kvalifikovaného názvu domény, podle očekávání:

```console 
carlos@VM-A1:~$ nslookup VM-A2-Label
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-A2-Label: SERVFAIL
```

Pokud zadáte plně kvalifikovaný název domény pro popisek DNS, název se přeloží na veřejnou IP adresu:

```console
carlos@VM-A1:~$ nslookup VM-A2-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.76
```
 
Pokus o vyřešení virtuálního počítače – B1 (který pochází z jiné virtuální sítě) se nezdařil, protože tento záznam v této zóně neexistuje.

```console
carlos@caalcobi-vm4:~$ nslookup VM-B1
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1: SERVFAIL
```

Použití plně kvalifikovaného názvu domény pro virtuální počítač – B1 neumožňuje, aby byl tento záznam z jiné zóny.

```console 
carlos@VM-A1:~$ nslookup VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local: SERVFAIL
```
 
Pokud použijete plně kvalifikovaný název domény pro popisek DNS, úspěšně se vyřeší:

``` 
carlos@VM-A1:~$ nslookup VM-B1-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-B1-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.57
```
 
Z virtuálního počítače – a3 (Windows VM). Všimněte si rozdílu mezi autoritativními a nesměrodatnými odpověďmi.

Interní záznamy:

```console
C:\Users\carlos>nslookup
Default Server:  UnKnown
Address:  168.63.129.16
 
> VM-A2
Server:  UnKnown
Address:  168.63.129.16
 
Name:    VM-A2.e71e1db5-0a38-460d-8539-705457ª4cf75.internal.lnv1.azurestack.local
Address:  10.0.0.6
```

Externí záznamy:

```console
> VM-A2-Label.lnv1.cloudapp.azscss.external
Server:  UnKnown
Address:  168.63.129.16
 
Non-authoritative answer:
Name:    VM-A2-Label.lnv1.cloudapp.azscss.external
Address:  172.31.12.76
``` 
 
V krátkém případě uvidíte z výše uvedeného:
 
*   Každá virtuální síť má svou vlastní zónu obsahující záznamy všech privátních IP adres, které se skládají z názvu virtuálního počítače a přípony DNS virtuální sítě (což je identifikátor GUID).
    *   \<VMName >.\<vnetGUID\>. Internal. >\<oblasti.\<stackinternalFQDN >
    *   To se provádí automaticky.
*   Pokud používáte veřejné IP adresy, můžete pro ně také vytvořit popisky DNS. Jsou vyřešeny jako jakákoli jiná externí adresa.
 
 
- IDN servery jsou autoritativní servery pro své interní zóny DNS a při pokusu o připojení virtuálních počítačů se virtuálními počítači klientů taky fungují jako překladač pro veřejné názvy. Pokud je k dispozici dotaz pro externí prostředek, servery IDN předá požadavek autoritativním serverům DNS, aby se vyřešily.
 
Jak vidíte z výsledků testovacího prostředí, máte kontrolu nad tím, jaká IP adresa se používá. Pokud použijete název virtuálního počítače, získáte privátní IP adresu, a pokud použijete popisek DNS, získáte veřejnou IP adresu.

## <a name="next-steps"></a>Další kroky

[Použití DNS v centru Azure Stack](azure-stack-dns.md)
