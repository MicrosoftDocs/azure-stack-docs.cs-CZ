---
title: Připojení ohraničení a integrace sítě pro Azure Stack integrovaných systémů | Microsoft Docs
description: Naučte se plánovat připojení k síti datacenter v datacentrech v Azure Stack integrovaných systémech.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: 142ea9b53d64e89157ce5c5556241b41275d430d
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094345"
---
# <a name="border-connectivity"></a>Připojení k ohraničení 
Plánování integrace sítě je důležitou podmínkou pro úspěšné Azure Stack nasazení integrovaných systémů, operace a správu. Plánování připojení k okraji začíná tím, že zvolíte, jestli chcete používat dynamické směrování s protokolem BGP (Border Gateway Protocol). K tomu je potřeba přiřadit 16bitové číslo autonomního systému protokolu BGP (veřejné nebo soukromé) nebo pomocí statického směrování, kde se hraniční zařízení přiřazují statická výchozí trasa.

> [!IMPORTANT]
> Přepínače rozhraní Top of rack vyžadují odchozí připojení vrstvy 3 s IP adresou typu Point-to-Point (/30 sítí) nakonfigurovanými ve fyzických rozhraních. Odchozí připojení vrstvy 2 s přepínači operačních systémem podporující operace Azure Stack se nepodporují.

## <a name="bgp-routing"></a>Směrování protokolu BGP
Pomocí protokolu dynamického směrování, jako je BGP, zaručuje, že váš systém vždycky ví o změnách v síti a usnadňuje správu. Pro zvýšení zabezpečení je možné nastavit heslo pro vytvoření partnerského vztahu protokolu BGP mezi MANDÁTem a ohraničením.

Jak je znázorněno v následujícím diagramu, inzerování privátního prostoru IP adres v přepínači MANDÁTu je blokováno pomocí seznamu předpon. Seznam předpon odmítne inzerci privátní sítě a použije se jako mapa směrování na připojení mezi MANDÁTem a ohraničením.

Software Load Balancer (SLB) běžící v rámci řešení Azure Stack partnerských uzlů pro zařízení se systémem zprovoznění, aby mohl dynamicky inzerovat virtuální IP adresy.

Aby se zajistilo, že se přenos dat uživatelů okamžitě a transparentně obnoví z chyby, VPC nebo MLAG nakonfigurované mezi zařízeními s rozhraním systému oprávnění umožňuje použití agregace s více šasi pro hostitele a HSRP nebo nebo VRRP, která zajišťuje redundanci sítě pro sítě IP.

![Směrování protokolu BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Statické směrování
Statické směrování vyžaduje další konfiguraci hraničních zařízení. Před jakoukoli změnou vyžaduje více ručních zásahů a správu a také důkladnou analýzu. Problémy způsobené chybou konfigurace můžou v závislosti na provedených změnách trvat delší dobu. Tato metoda směrování se nedoporučuje, ale je podporovaná.

Aby bylo možné integrovat Azure Stack do svého síťového prostředí pomocí statického směrování, musí být připojeni všechny čtyři fyzické odkazy mezi ohraničením a zařízením. Vysokou dostupnost nelze zaručit kvůli tomu, jak statické směrování funguje.

Hraniční zařízení musí být nakonfigurované se statickými trasami, které odkazují na zařízení se systémem P2Ps pro provoz směřující do *externí* sítě nebo veřejné VIP a sítě *infrastruktury* . Bude vyžadovat statické trasy k řadiči pro *správu základní desky* a k *externím* sítím pro nasazení. Operátoři se můžou rozhodnout pro ponechání statických tras na hranici pro přístup k prostředkům správy, které se nacházejí v síti řadiče pro správu *základní desky* . Přidání statických tras pro *Přepnutí infrastruktury* a sítí *pro správu přepínačů* je volitelné.

Zařízení s modulem pro sestavilo konfiguraci se statickou výchozí trasou, která odesílá veškerý provoz do hraničních zařízení. Jedinou výjimkou provozu na výchozí pravidlo je pro privátní prostor, který se zablokuje pomocí Access Controlho seznamu, který se používá pro připojení k hraniční síti.

Statické směrování platí jenom pro odchozí připojení mezi přepínači pro mandát a ohraničení. V racku se používá dynamické směrování protokolu BGP, protože jde o nezbytný nástroj pro SLB a další komponenty a nedá se zakázat ani odebrat.

![Statické směrování](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup>Síť řadiče pro správu základní desky je po nasazení volitelná.

<sup>\*\*</sup>Síť infrastruktury přepínače je volitelná, protože celá síť může být součástí sítě pro správu přepínače.

<sup>\*\*\*</sup>Síť pro správu přepínače je povinná a je možné ji přidat odděleně ze sítě infrastruktury přepínače.

## <a name="transparent-proxy"></a>Transparentní proxy server
Pokud vaše datacentrum vyžaduje, aby všechny přenosy používaly proxy server, musíte nakonfigurovat *transparentní proxy server* tak, aby zpracovával veškerý provoz z racku, aby ho mohl zpracovat podle zásad, což rozděluje přenos mezi zónami v síti.

> [!IMPORTANT]
> Řešení Azure Stack nepodporuje normální webové proxy servery.  

Transparentní proxy server (označovaný také jako zachycení, vložený nebo vynucený proxy server) zachycuje normální komunikaci v síťové vrstvě bez nutnosti jakékoli speciální konfigurace klienta. Klienti nemusí vědět o existenci proxy serveru.

![Transparentní proxy server](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Další kroky
[Integrace DNS](azure-stack-integrate-dns.md)
