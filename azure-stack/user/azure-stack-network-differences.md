---
title: Rozdíly v Azure Stack sítě | Microsoft Docs
description: Přečtěte si o rozdílech a ohledech při práci se sítěmi v Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 10/10/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 2ba1fc76b8b0ead1da543abb467d7a1613a5304c
ms.sourcegitcommit: ac7d98a2b58442e82798022d69ebfae6616a225f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239326"
---
# <a name="differences-and-considerations-for-azure-stack-networking"></a>Rozdíly a požadavky pro Azure Stack sítě

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Služba Azure Stack Networking má mnoho funkcí poskytovaných službou Azure Networking. Před nasazením Azure Stack sítě ale byste měli porozumět nějakým hlavním rozdílům.

Tento článek obsahuje přehled jedinečných důležitých informací o Azure Stack sítě a jejích funkcích. Další informace o rozdílech na vysoké úrovni mezi Azure Stack a Azure najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

## <a name="cheat-sheet-networking-differences"></a>Tahák list: rozdíly v sítích

| Služba | Funkce | Azure (Global) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Víceklientské DNS | Podporováno | Zatím nepodporováno |
|  | DNS AAAA records | Podporováno | Nepodporuje se |
|  | Zóny DNS na předplatné | 100 (výchozí)<br>Lze zvýšit na vyžádání. | 100 |
|  | Sady záznamů DNS na zónu | 5000 (výchozí)<br>Lze zvýšit na vyžádání. | 5000 |
|  | Názvové servery pro delegování zóny | Azure poskytuje čtyři názvové servery pro každou vytvořenou zónu uživatele (tenanta). | Azure Stack poskytuje dva názvové servery pro každou vytvořenou zónu uživatele (tenanta). |
| Brána Azure Firewall | Služba zabezpečení sítě | Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. | Zatím se nepodporuje. |
| Virtuální sítě | Partnerské vztahy virtuálních sítí | Propojit dvě virtuální sítě ve stejné oblasti prostřednictvím páteřní sítě Azure. | Zatím nepodporováno |
|  | IPv6 adresy | Adresu IPv6 můžete přiřadit jako součást [Konfigurace síťového rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Podporovaný je jenom protokol IPv4. |
|  | Plán DDoS Protection | Podporováno | Zatím se nepodporuje. |
|  | Konfigurace IP adres sady škálování | Podporováno | Zatím se nepodporuje. |
|  | Služba privátního přístupu (podsíť) | Podporováno | Zatím se nepodporuje. |
|  | Koncové body služeb | Podporováno pro interní připojení (bez připojení k Internetu) ke službám Azure. | Zatím se nepodporuje. |
|  | Zásady koncového bodu služby | Podporováno | Zatím se nepodporuje. |
|  | Tunely služby | Podporováno | Zatím se nepodporuje.  |
| Network Security Groups (Skupiny zabezpečení sítě) | Rozšířená pravidla zabezpečení | Podporováno | Zatím se nepodporuje. |
|  | Platná pravidla zabezpečení | Podporováno | Zatím se nepodporuje. |
|  | Skupiny zabezpečení aplikací | Podporováno | Zatím se nepodporuje. |
| Brány virtuálních sítí | Point-to-Site VPN Gateway | Podporováno | Zatím se nepodporuje. |
|  | Brána VNet-to-VNet | Podporováno | Zatím se nepodporuje. |
|  | Typ Virtual Network brány | Azure podporuje VPN<br> ExpressRoute <br> Síť Hyper. | Azure Stack aktuálně podporuje pouze typ sítě VPN. |
|  | VPN Gateway SKU | Podpora pro Basic, GW1, GW2, GW3, standard High Performance a vysoce vysoký výkon. | Podpora pro základní, standardní a vysoce výkonné skladové jednotky. |
|  | Typ sítě VPN | Azure podporuje jak založené na zásadách, tak i směrování. | Azure Stack podporuje pouze směrování. |
|  | Nastavení protokolu BGP | Azure podporuje konfiguraci partnerských adres protokolu BGP a váhy partnerského vztahu. | V Azure Stack je automaticky nakonfigurována váha partnerského vztahu protokolu BGP a váha partnerského vztahu. Neexistuje žádný způsob, jak by uživatel mohl konfigurovat tato nastavení s vlastními hodnotami. |
|  | Výchozí lokalita brány | Azure podporuje konfiguraci výchozího webu pro vynucené tunelování. | Zatím se nepodporuje. |
|  | Změna velikosti brány | Azure podporuje změnu velikosti brány po nasazení. | Změna velikosti se nepodporuje. |
|  | Konfigurace dostupnosti | Aktivní/aktivní | Aktivní/pasivní |
|  | UsePolicyBasedTrafficSelectors | Azure podporuje selektory přenosu založené na zásadách s připojeními bran založenými na trasách. | Zatím se nepodporuje. |
| Nástroj pro vyrovnávání zatížení | SKU | Podporují se základní a standardní nástroje pro vyrovnávání zatížení. | Podporuje se jenom základní Load Balancer.<br>Vlastnost SKU není podporována.<br>/Path/nástroje pro vyrovnávání zatížení základního SKU nemůže mít více než 5 konfigurací front-endové IP adresy.  |
|  | Zóny | Zóny dostupnosti jsou podporovány. | Zatím nepodporováno |
|  | Podpora pravidel příchozího překladu adres (NAT) pro koncové body služby | Azure podporuje určení koncových bodů služby pro pravidla příchozího překladu adres (NAT). | Azure Stack ještě nepodporuje koncové body služby, takže je nejde zadat. |
|  | Protocol (Protokol) | Azure podporuje určení protokolu GRE nebo ESP. | Třída protokolu není v Azure Stack podporována. |
| Veřejná IP adresa | Verze veřejné IP adresy | Azure podporuje IPv6 i IPv4. | Podporovaný je jenom protokol IPv4. |
| | SKU | Azure podporuje základní a standardní. | Podporovaná je jenom základní. |
| Síťové rozhraní | Získat efektivní směrovací tabulku | Podporováno | Zatím se nepodporuje. |
|  | Získání efektivních seznamů ACL | Podporováno | Zatím se nepodporuje. |
|  | Povolit akcelerované síťové služby | Podporováno | Zatím se nepodporuje. |
|  | Předávání IP | Ve výchozím nastavení zakázáno.  Lze povolit. | Přepnutí tohoto nastavení se nepodporuje.  Ve výchozím nastavení zapnuto. |
|  | Skupiny zabezpečení aplikací | Podporováno | Zatím se nepodporuje. |
|  | Popisek interního názvu DNS | Podporováno | Zatím se nepodporuje. |
|  | Verze privátní IP adresy | Podporují se protokoly IPv6 i IPv4. | Podporovaný je jenom protokol IPv4. |
|  | Statická adresa MAC | Nepodporuje se | Není podporováno. Každý Azure Stack systém používá stejný fond adres MAC. |
| Network Watcher | Network Watcher možnosti monitorování sítě klienta | Podporováno | Zatím se nepodporuje. |
| CDN | Profily Content Delivery Network | Podporováno | Zatím se nepodporuje. |
| Application Gateway | Vyrovnávání zatížení vrstvy 7 | Podporováno | Zatím se nepodporuje. |
| Traffic Manager | Směrování příchozího provozu za účelem optimálního výkonu a spolehlivosti aplikací. | Podporováno | Zatím se nepodporuje. |
| ExpressRoute | Nastavte si rychlé privátní připojení ke cloudovým službám Microsoftu z vaší místní infrastruktury nebo zařízení v rámci umístění. | Podporováno | Podpora připojení Azure Stack k okruhu Express Route. |

## <a name="api-versions"></a>Verze rozhraní API 

Azure Stack sítě podporuje následující verze rozhraní API: 

- 2018-11-01
- 2018-10-01
- 2018-08-01
- 2018-07-01
- 2018-06-01
- 2018-05-01
- 2018-04-01
- 2018-03-01
- 2018-02-01
- 2018-01-01
- 2017-11-01
- 2017-10-01

## <a name="next-steps"></a>Další kroky

[DNS v Azure Stacku](azure-stack-dns.md)
