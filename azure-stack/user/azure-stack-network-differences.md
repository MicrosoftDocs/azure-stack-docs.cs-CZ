---
title: Rozdíly v sítích centra Azure Stack
description: Přečtěte si o rozdílech a ohledech při práci se sítěmi v centru Azure Stack.
author: mattbriggs
ms.date: 5/27/2020
ms.topic: article
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 459d6177015cbca40d2f8f830eba85dc87be18c7
ms.sourcegitcommit: e79aafb05c5fc512a83f0ebc6d76503213ccbc70
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2020
ms.locfileid: "84730675"
---
# <a name="differences-and-considerations-for-azure-stack-hub-networking"></a>Rozdíly a požadavky pro sítě Azure Stack hub

Síť Azure Stack hub má mnoho funkcí poskytovaných službou Azure Networking. Před nasazením sítě Azure Stack hub byste ale měli porozumět nějakým hlavním rozdílům.

Tento článek obsahuje přehled jedinečných důležitých informací o sítích centra Azure Stack a jeho funkcích. Další informace o hlavních rozdílech mezi Azure Stack hub a Azure najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

## <a name="cheat-sheet-networking-differences"></a>Tahák list: rozdíly v sítích

| Služba | Příznak | Azure (Global) | Centrum Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Víceklientské DNS | Podporuje se | Zatím nepodporováno |
|  | Záznamy DNS AAAA | Podporuje se | Nepodporuje se |
|  | Zóny DNS na předplatné | 100 (výchozí)<br>Lze zvýšit na vyžádání. | 100 |
|  | Sady záznamů DNS na zónu | 5000 (výchozí)<br>Lze zvýšit na vyžádání. | 5000 |
|  | Názvové servery pro delegování zóny | Azure poskytuje čtyři názvové servery pro každou vytvořenou zónu uživatele (tenanta). | Centrum Azure Stack poskytuje dva názvové servery pro každou vytvořenou zónu uživatele (tenanta). |
| Brána Azure Firewall | Služba zabezpečení sítě | Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. | Zatím se nepodporuje. |
| Virtual Network | Partnerské vztahy virtuálních sítí | Propojit dvě virtuální sítě ve stejné oblasti prostřednictvím páteřní sítě Azure. | Zatím nepodporováno |
|  | IPv6 adresy | Adresu IPv6 můžete přiřadit jako součást [Konfigurace síťového rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Podporovaný je jenom protokol IPv4. |
|  | Plán DDoS Protection | Podporuje se | Zatím se nepodporuje. |
|  | Konfigurace IP adres sady škálování | Podporuje se | Zatím se nepodporuje. |
|  | Služba privátního přístupu (podsíť) | Podporuje se | Zatím se nepodporuje. |
|  | Koncové body služby | Podporováno pro interní připojení (bez připojení k Internetu) ke službám Azure. | Zatím se nepodporuje. |
|  | Zásady koncového bodu služby | Podporuje se | Zatím se nepodporuje. |
|  | Tunely služby | Podporuje se | Zatím se nepodporuje.  |
| Network Security Groups (Skupiny zabezpečení sítě) | Rozšířená pravidla zabezpečení | Podporuje se | Podporuje se. |
|  | Platná pravidla zabezpečení | Podporuje se | Zatím se nepodporuje. |
|  | Skupiny zabezpečení aplikací | Podporuje se | Zatím se nepodporuje. |
|  | Protokoly pravidel | TCP, UDP, ICMP, jakýkoli | Jenom TCP, UDP nebo Any |
| Brány virtuálních sítí | Point-to-Site VPN Gateway | Podporuje se | Zatím se nepodporuje. |
|  | Brána VNet-to-VNet | Podporuje se | Zatím se nepodporuje. |
|  | Typ Virtual Network brány | Azure podporuje VPN<br> Express Route <br> Síť Hyper. | Rozbočovač Azure Stack aktuálně podporuje pouze typ sítě VPN. |
|  | Skladové položky VPN Gateway | Podpora pro Basic, GW1, GW2, GW3, standard High Performance a vysoce vysoký výkon. | Podpora pro základní, standardní a vysoce výkonné skladové jednotky. |
|  | Typ sítě VPN | Azure podporuje jak založené na zásadách, tak i směrování. | Centrum Azure Stack podporuje pouze směrování. |
|  | Nastavení protokolu BGP | Azure podporuje konfiguraci partnerských adres protokolu BGP a váhy partnerského vztahu. | Adresa partnerského vztahu protokolu BGP a váha partnerského vztahu se automaticky konfigurují v Azure Stack hub. Neexistuje žádný způsob, jak by uživatel mohl konfigurovat tato nastavení s vlastními hodnotami. |
|  | Výchozí lokalita brány | Azure podporuje konfiguraci výchozího webu pro vynucené tunelování. | Zatím se nepodporuje. |
|  | Změna velikosti brány | Azure podporuje změnu velikosti brány po nasazení. | Změna velikosti se nepodporuje. |
|  | Konfigurace dostupnosti | Aktivní/aktivní | Aktivní/pasivní |
|  | UsePolicyBasedTrafficSelectors | Azure podporuje selektory přenosu založené na zásadách s připojeními bran založenými na trasách. | Zatím se nepodporuje. |
|  | Monitorování a výstrahy | Azure používá Azure Monitor k tomu, aby poskytovala možnost nastavit výstrahy pro prostředky sítě VPN. | Zatím se nepodporuje.|
| Nástroj pro vyrovnávání zatížení | SKU | Podporují se základní a standardní nástroje pro vyrovnávání zatížení. | Podporuje se jenom základní Load Balancer.<br>Vlastnost SKU není podporována.<br>/Path/nástroje pro vyrovnávání zatížení základního SKU nemůže mít více než 5 konfigurací front-endové IP adresy.  |
|  | Zóny | Zóny dostupnosti jsou podporovány. | Zatím nepodporováno |
|  | Podpora pravidel příchozího překladu adres (NAT) pro koncové body služby | Azure podporuje určení koncových bodů služby pro pravidla příchozího překladu adres (NAT). | Centrum Azure Stack ještě nepodporuje koncové body služby, takže je nejde zadat. |
|  | Protocol (Protokol) | Azure podporuje určení protokolu GRE nebo ESP. | Třída protokolu není v centru Azure Stack podporována. |
| Veřejná IP adresa | Verze veřejné IP adresy | Azure podporuje IPv6 i IPv4. | Podporovaný je jenom protokol IPv4. |
| | SKU | Azure podporuje základní a standardní. | Podporovaná je jenom základní. |
| Síťové rozhraní | Získat efektivní směrovací tabulku | Podporuje se | Zatím se nepodporuje. |
|  | Získání efektivních seznamů ACL | Podporuje se | Zatím se nepodporuje. |
|  | Povolit akcelerované síťové služby | Podporuje se | Zatím se nepodporuje. |
|  | Předávání IP | Ve výchozím nastavení zakázáno.  Lze povolit. | Přepnutí tohoto nastavení se nepodporuje.  Ve výchozím nastavení zapnuto. |
|  | Skupiny zabezpečení aplikací | Podporuje se | Zatím se nepodporuje. |
|  | Popisek interního názvu DNS | Podporuje se | Zatím se nepodporuje. |
|  | Verze privátní IP adresy | Podporují se protokoly IPv6 i IPv4. | Podporovaný je jenom protokol IPv4. |
|  | Statická adresa MAC | Nepodporuje se | Není podporováno. Každý systém Azure Stack hub používá stejný fond adres MAC. |
| Network Watcher | Network Watcher možnosti monitorování sítě klienta | Podporuje se | Zatím se nepodporuje. |
| CDN | Profily Content Delivery Network | Podporuje se | Zatím se nepodporuje. |
| Application Gateway | Vyrovnávání zatížení vrstvy 7 | Podporuje se | Zatím se nepodporuje. |
| Traffic Manager | Směrování příchozího provozu za účelem optimálního výkonu a spolehlivosti aplikací. | Podporuje se | Zatím se nepodporuje. |
| Express Route | Nastavte si rychlé privátní připojení ke cloudovým službám Microsoftu z vaší místní infrastruktury nebo zařízení v rámci umístění. | Podporuje se | Podpora připojení centra Azure Stack k okruhu Express Route. |

## <a name="api-versions"></a>Verze rozhraní API 

Sítě centra Azure Stack podporují následující verze rozhraní API: 

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

[DNS v centru Azure Stack](azure-stack-dns.md)
