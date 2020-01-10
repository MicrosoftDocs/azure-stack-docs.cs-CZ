---
title: Rozdíly v sítích centra Azure Stack | Microsoft Docs
description: Přečtěte si o rozdílech a ohledech při práci se sítěmi v centru Azure Stack.
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
ms.openlocfilehash: 854c249ff6ef5d44f6e8b3451f50b90b296ae997
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816084"
---
# <a name="differences-and-considerations-for-azure-stack-hub-networking"></a>Rozdíly a požadavky pro sítě Azure Stack hub

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Síť Azure Stack hub má mnoho funkcí poskytovaných službou Azure Networking. Před nasazením sítě Azure Stack hub byste ale měli porozumět nějakým hlavním rozdílům.

Tento článek obsahuje přehled jedinečných důležitých informací o sítích centra Azure Stack a jeho funkcích. Další informace o hlavních rozdílech mezi Azure Stack hub a Azure najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

## <a name="cheat-sheet-networking-differences"></a>Tahák list: rozdíly v sítích

| Služba | Funkce | Azure (Global) | Azure Stack Hub |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Víceklientské DNS | Podporováno | Zatím nepodporováno |
|  | DNS AAAA records | Podporováno | Nepodporováno |
|  | Zóny DNS na předplatné | 100 (výchozí)<br>Lze zvýšit na vyžádání. | 100 |
|  | Sady záznamů DNS na zónu | 5000 (výchozí)<br>Lze zvýšit na vyžádání. | 5000 |
|  | Názvové servery pro delegování zóny | Azure poskytuje čtyři názvové servery pro každou vytvořenou zónu uživatele (tenanta). | Centrum Azure Stack poskytuje dva názvové servery pro každou vytvořenou zónu uživatele (tenanta). |
| Azure Firewall | Služba zabezpečení sítě | Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. | Zatím se nepodporuje. |
| Virtual Network | Partnerský vztah virtuální sítě | Propojit dvě virtuální sítě ve stejné oblasti prostřednictvím páteřní sítě Azure. | Zatím nepodporováno |
|  | Adresy IPv6 | Adresu IPv6 můžete přiřadit jako součást [Konfigurace síťového rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Podporovaný je jenom protokol IPv4. |
|  | Plán DDoS Protection | Podporováno | Zatím se nepodporuje. |
|  | Konfigurace IP adres sady škálování | Podporováno | Zatím se nepodporuje. |
|  | Služba privátního přístupu (podsíť) | Podporováno | Zatím se nepodporuje. |
|  | Koncové body služeb | Podporováno pro interní připojení (bez připojení k Internetu) ke službám Azure. | Zatím se nepodporuje. |
|  | Zásady koncového bodu služby | Podporováno | Zatím se nepodporuje. |
|  | Tunely služby | Podporováno | Zatím se nepodporuje.  |
| Skupiny zabezpečení sítě | Rozšířená pravidla zabezpečení | Podporováno | Zatím se nepodporuje. |
|  | Platná pravidla zabezpečení | Podporováno | Zatím se nepodporuje. |
|  | Skupiny zabezpečení aplikací | Podporováno | Zatím se nepodporuje. |
| Brány virtuálních sítí | Point-to-Site VPN Gateway | Podporováno | Zatím se nepodporuje. |
|  | Brána VNet-to-VNet | Podporováno | Zatím se nepodporuje. |
|  | Typ Virtual Network brány | Azure podporuje VPN<br> ExpressRoute <br> Síť Hyper. | Rozbočovač Azure Stack aktuálně podporuje pouze typ sítě VPN. |
|  | Skladové položky VPN Gateway | Podpora pro Basic, GW1, GW2, GW3, standard High Performance a vysoce vysoký výkon. | Podpora pro základní, standardní a vysoce výkonné skladové jednotky. |
|  | Typ sítě VPN | Azure podporuje jak založené na zásadách, tak i směrování. | Centrum Azure Stack podporuje pouze směrování. |
|  | Nastavení protokolu BGP | Azure podporuje konfiguraci partnerských adres protokolu BGP a váhy partnerského vztahu. | Adresa partnerského vztahu protokolu BGP a váha partnerského vztahu se automaticky konfigurují v Azure Stack hub. Neexistuje žádný způsob, jak by uživatel mohl konfigurovat tato nastavení s vlastními hodnotami. |
|  | Výchozí lokalita brány | Azure podporuje konfiguraci výchozího webu pro vynucené tunelování. | Zatím se nepodporuje. |
|  | Změna velikosti brány | Azure podporuje změnu velikosti brány po nasazení. | Změna velikosti se nepodporuje. |
|  | Konfigurace dostupnosti | Aktivní/aktivní | Aktivní/pasivní |
|  | UsePolicyBasedTrafficSelectors | Azure podporuje selektory přenosu založené na zásadách s připojeními bran založenými na trasách. | Zatím se nepodporuje. |
| Nástroj pro vyrovnávání zatížení | Skladová položka | Podporují se základní a standardní nástroje pro vyrovnávání zatížení. | Podporuje se jenom základní Load Balancer.<br>Vlastnost SKU není podporována.<br>/Path/nástroje pro vyrovnávání zatížení základního SKU nemůže mít více než 5 konfigurací front-endové IP adresy.  |
|  | Zóny | Zóny dostupnosti jsou podporovány. | Zatím nepodporováno |
|  | Podpora pravidel příchozího překladu adres (NAT) pro koncové body služby | Azure podporuje určení koncových bodů služby pro pravidla příchozího překladu adres (NAT). | Centrum Azure Stack ještě nepodporuje koncové body služby, takže je nejde zadat. |
|  | Protocol (Protokol) | Azure podporuje určení protokolu GRE nebo ESP. | Třída protokolu není v centru Azure Stack podporována. |
| Veřejná IP adresa | Verze veřejné IP adresy | Azure podporuje IPv6 i IPv4. | Podporovaný je jenom protokol IPv4. |
| | Skladová položka | Azure podporuje základní a standardní. | Podporovaná je jenom základní. |
| Síťové rozhraní | Získat efektivní směrovací tabulku | Podporováno | Zatím se nepodporuje. |
|  | Získání efektivních seznamů ACL | Podporováno | Zatím se nepodporuje. |
|  | Povolit akcelerované síťové služby | Podporováno | Zatím se nepodporuje. |
|  | Předávání IP | Ve výchozím nastavení zakázáno.  Lze povolit. | Přepnutí tohoto nastavení se nepodporuje.  Ve výchozím nastavení zapnuto. |
|  | Skupiny zabezpečení aplikací | Podporováno | Zatím se nepodporuje. |
|  | Popisek interního názvu DNS | Podporováno | Zatím se nepodporuje. |
|  | Verze privátní IP adresy | Podporují se protokoly IPv6 i IPv4. | Podporovaný je jenom protokol IPv4. |
|  | Statická adresa MAC | Nepodporováno | Není podporováno. Každý systém Azure Stack hub používá stejný fond adres MAC. |
| Network Watcher | Network Watcher možnosti monitorování sítě klienta | Podporováno | Zatím se nepodporuje. |
| CDN | Profily Content Delivery Network | Podporováno | Zatím se nepodporuje. |
| Application Gateway | Vyrovnávání zatížení vrstvy 7 | Podporováno | Zatím se nepodporuje. |
| Traffic Manager | Směrování příchozího provozu za účelem optimálního výkonu a spolehlivosti aplikací. | Podporováno | Zatím se nepodporuje. |
| ExpressRoute | Nastavte si rychlé privátní připojení ke cloudovým službám Microsoftu z vaší místní infrastruktury nebo zařízení v rámci umístění. | Podporováno | Podpora připojení centra Azure Stack k okruhu Express Route. |

## <a name="api-versions"></a>Verze rozhraní API 

Sítě centra Azure Stack podporují následující verze rozhraní API: 

- 2018-11-01
- 2018-10-01
- 1\. 8. 2018
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
