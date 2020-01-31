---
title: Připojení ohraničení a integrace sítě pro integrované systémy centra Azure Stack
description: Naučte se plánovat připojení k síti datacenter v datových centrech v integrovaných systémech Azure Stack hub.
author: ihenkel
ms.topic: article
ms.date: 11/15/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 15440c32974aebfed3f3faf86885dd6f0af71ddd
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76878509"
---
# <a name="border-connectivity"></a>Připojení k ohraničení 
Plánování integrace sítě je důležitou podmínkou pro úspěšné nasazení, provoz a správu integrovaných systémů integrovaného centra Azure Stack. Plánování připojení k okraji začíná tím, že zvolíte, jestli chcete používat dynamické směrování s protokolem BGP (Border Gateway Protocol). K tomu je potřeba přiřadit 16bitové číslo autonomního systému protokolu BGP (veřejné nebo soukromé) nebo pomocí statického směrování, kde se hraniční zařízení přiřazují statická výchozí trasa.

> [!IMPORTANT]
> Přepínače rozhraní Top of rack vyžadují odchozí připojení vrstvy 3 s IP adresou typu Point-to-Point (/30 sítí) nakonfigurovanými ve fyzických rozhraních. Odchozí připojení vrstvy 2 s přepínači MANDÁTu podporující operace Azure Stackho centra se nepodporují.

## <a name="bgp-routing"></a>Směrování protokolu BGP
Pomocí protokolu dynamického směrování, jako je BGP, zaručuje, že váš systém vždycky ví o změnách v síti a usnadňuje správu. Pro zvýšení zabezpečení je možné nastavit heslo pro vytvoření partnerského vztahu protokolu BGP mezi MANDÁTem a ohraničením.

Jak je znázorněno v následujícím diagramu, inzerování privátního prostoru IP adres v přepínači MANDÁTu je blokováno pomocí seznamu předpon. Seznam předpon odmítne inzerci privátní sítě a použije se jako mapa směrování na připojení mezi MANDÁTem a ohraničením.

Software Load Balancer (SLB) běžící v rámci řešení Azure Stack hub se nachází v partnerských zařízeních k zprovoznění, aby mohl dynamicky inzerovat virtuální IP adresy.

Aby se zajistilo, že se přenos dat uživatelů okamžitě a transparentně obnoví z chyby, VPC nebo MLAG nakonfigurované mezi zařízeními s rozhraním systému oprávnění umožňuje použití agregace s více šasi pro hostitele a HSRP nebo nebo VRRP, která zajišťuje redundanci sítě pro sítě IP.

![Směrování protokolu BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Statické směrování
Statické směrování vyžaduje další konfiguraci hraničních zařízení. Před jakoukoli změnou vyžaduje více ručních zásahů a správu a také důkladnou analýzu. Problémy způsobené chybou konfigurace můžou v závislosti na provedených změnách trvat delší dobu. Tato metoda směrování se nedoporučuje, ale je podporovaná.

Pokud chcete integrovat Azure Stack hub do svého síťového prostředí pomocí statického směrování, musí být připojené všechny čtyři fyzické odkazy mezi hranicí a zařízením. Vysokou dostupnost nelze zaručit kvůli tomu, jak statické směrování funguje.

Hraniční zařízení musí být nakonfigurované se statickými trasami, které odkazují na každou ze čtyř sad IP adres P2P mezi MANDÁTem a hranicí pro provoz směřující do libovolné sítě v rámci centra Azure Stack, ale k provozu se vyžaduje jenom *externí* nebo veřejná síť VIP. Pro počáteční nasazení se vyžadují statické trasy k *řadiči pro správu základní desky* a *externí* sítě. Operátoři se můžou rozhodnout pro ponechání statických tras na hranici pro přístup k prostředkům správy, které se nacházejí na řadiči pro správu *základní desky* a v síti *infrastruktury* . Přidání statických tras pro *Přepnutí infrastruktury* a sítí *pro správu přepínačů* je volitelné.

Zařízení se systémem se konfigurují se statickou výchozí trasou, která odesílá veškerý provoz do hraničních zařízení. Jedinou výjimkou provozu na výchozí pravidlo je pro privátní prostor, který se zablokuje pomocí Access Controlho seznamu, který se používá pro připojení k hraniční síti.

Statické směrování platí jenom pro odchozí připojení mezi přepínači pro mandát a ohraničení. V racku se používá dynamické směrování protokolu BGP, protože jde o nezbytný nástroj pro SLB a další komponenty a nedá se zakázat ani odebrat.

![Statické směrování](media/azure-stack-border-connectivity/static-routing.png)

<sup>\*</sup> Síť řadiče pro správu základní desky je po nasazení volitelná.

<sup>\*\*</sup> Síť infrastruktury přepínače je volitelná, protože celá síť může být součástí sítě pro správu přepínače.

<sup>\*\*\*</sup> Síť pro správu přepínače je povinná a je možné ji přidat odděleně ze sítě infrastruktury přepínače.

## <a name="transparent-proxy"></a>Transparentní proxy server
Pokud vaše datacentrum vyžaduje, aby všechny přenosy používaly proxy server, musíte nakonfigurovat *transparentní proxy server* tak, aby zpracovával veškerý provoz z racku, aby ho mohl zpracovat podle zásad, což rozděluje přenos mezi zónami v síti.

> [!IMPORTANT]
> Řešení centra Azure Stack nepodporuje normální webové proxy servery.  

Transparentní proxy server (označovaný také jako zachycení, vložený nebo vynucený proxy server) zachycuje normální komunikaci v síťové vrstvě bez nutnosti jakékoli speciální konfigurace klienta. Klienti nemusí vědět o existenci proxy serveru.

![Transparentní proxy server](media/azure-stack-border-connectivity/transparent-proxy.png)

Zachycení provozu SSL není [podporované](azure-stack-firewall.md#ssl-interception) a může při přístupu k koncovým bodům způsobit selhání služby. Maximální podporovaný časový limit pro komunikaci s koncovými body vyžadovanými pro identitu je 60 s se 3 opakovanými pokusy.

## <a name="next-steps"></a>Další kroky
[Integrace DNS](azure-stack-integrate-dns.md)
