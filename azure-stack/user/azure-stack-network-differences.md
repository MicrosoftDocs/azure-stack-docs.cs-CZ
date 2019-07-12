---
title: Azure Stack sítě rozdíly | Dokumentace Microsoftu
description: Další informace o rozdíly a aspekty při práci se sítěmi v Azure stacku.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 06/17/2019
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 06b61bf80b2c123413425fc3abdcda12961d096c
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816221"
---
# <a name="differences-and-considerations-for-azure-stack-networking"></a>Rozdíly a aspekty sítí služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Sítě Azure Stack má mnoho z funkcí poskytovaných službou sítě Azure. Existují však některé hlavní rozdíly, které byste měli porozumět před nasazením služby Azure Stack network.

Tento článek poskytuje přehled o jedinečných důležité informace o sítích Azure stacku a jeho funkcí. Další informace o základní rozdíly mezi Azure Stack a Azure, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

## <a name="cheat-sheet-networking-differences"></a>Tahák: Rozdíly sítě

| Služba | Funkce | Azure (globální) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | Multi-tenant DNS | Podporováno | Není dosud podporován. |
|  | DNS AAAA records | Podporováno | Nepodporuje se |
|  | Zóny DNS na předplatné | 100 (výchozí)<br>Je možné zvýšit na vyžádání. | 100 |
|  | DNS record sets per zone | 5000 (výchozí)<br>Je možné zvýšit na vyžádání. | 5000 |
|  | Name servers for zone delegation | Azure poskytuje čtyři názvové servery pro každou zónu uživatele (tenant), který je vytvořen. | Azure Stack nabízí dvě názvové servery pro každou zónu uživatele (tenant), který je vytvořen. |
| Brána Azure Firewall | Služba zabezpečení sítě | Azure Firewall je spravovaná cloudová služba síťového zabezpečení, která chrání vaše prostředky ve virtuálních sítích Azure. | Ještě není podporované. |
| Virtuální sítě | Partnerské vztahy virtuálních sítí | Propojení dvou virtuálních sítí ve stejné oblasti prostřednictvím páteřní sítě Azure. | Není dosud podporován. |
|  | IPv6 adresy | Můžete přiřadit adresu protokolu IPv6 v rámci [konfiguraci síťového rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Podporovaný je jenom protokol IPv4. |
|  | Plán DDoS Protection | Podporováno | Ještě není podporované. |
|  | Konfigurace IP adresy škálovací sady | Podporováno | Ještě není podporované. |
|  | Privátní přístup ke službám (podsítě) | Podporováno | Ještě není podporované. |
|  | Koncové body služeb | Podporované pro interní (bez Internet) připojení ke službám Azure. | Ještě není podporované. |
|  | Zásady koncového bodu služby | Podporováno | Ještě není podporované. |
|  | Tunelová propojení služby | Podporováno | Ještě není podporované.  |
| Network Security Groups (Skupiny zabezpečení sítě) | Rozšířená pravidla zabezpečení | Podporováno | Ještě není podporované. |
|  | Platná pravidla zabezpečení | Podporováno | Ještě není podporované. |
|  | Skupiny zabezpečení aplikací | Podporováno | Ještě není podporované. |
| Brány virtuálních sítí | Point-to-Site VPN Gateway | Podporováno | Ještě není podporované. |
|  | Brána připojení typu Vnet-to-Vnet | Podporováno | Ještě není podporované. |
|  | Typ brány virtuální sítě | Azure podporuje sítě VPN<br> ExpressRoute <br> Hyper Net. | Azure Stack v současné době podporuje pouze typ sítě VPN. |
|  | SKU služby VPN Gateway | Podporu pro Basic, GW1, GW2, GW3, standardní vysoký výkon, mimořádně vysoký výkon. | Podpora pro Basic, Standard a skladové položky High Performance. |
|  | Typ sítě VPN | Azure podporuje technologie QoS i založené na směrování. | Azure Stack podporuje založené na trasách pouze. |
|  | Nastavení protokolu BGP | Azure podporuje konfiguraci adresy partnerského vztahu protokolu BGP a váha partnerského uzlu. | Adresa partnerského vztahu protokolu BGP a váha partnerského uzlu jsou automaticky nakonfigurované ve službě Azure Stack. Neexistuje žádný způsob pro uživatele k nakonfigurování těchto nastavení vlastní hodnoty. |
|  | Výchozí server brány | Azure podporuje konfiguraci výchozí web pro vynucené tunelování. | Ještě není podporované. |
|  | Změna velikosti brány | Azure podporuje změnu velikosti brány po nasazení. | Změna velikosti není podporované. |
|  | Konfigurace dostupnosti | Aktivní/aktivní | Aktivní/pasivní vysoká dostupnost |
|  | UsePolicyBasedTrafficSelectors | Azure podporuje používání selektorů přenosu na základě zásad s připojeními trasové brány. | Ještě není podporované. |
| Nástroj pro vyrovnávání zatížení | SKU | Základní a podporovaných nástrojů pro vyrovnávání zatížení. | Je podporován pouze Load balanceru úrovně Basic.  Vlastnost SKU se nepodporuje. |
|  | Zóny | Zóny dostupnosti jsou podporovány. | Není dosud podporován. |
|  | Pravidla příchozího překladu adres podporu koncových bodů služby | Azure podporuje zadání koncových bodů služby pro pravidla příchozího překladu adres. | Azure Stack zatím nepodporuje koncové body služby, takže tyto nelze zadat. |
|  | Protocol | Azure podporuje zadávání GRE nebo ESP. | Třída protokolu není podporované ve službě Azure Stack. |
| Veřejná IP adresa | Verze veřejné IP adresy | Azure podporuje protokol IPv6 a IPv4. | Podporovaný je jenom protokol IPv4. |
| Síťové rozhraní | Získat efektivní směrovací tabulky | Podporováno | Ještě není podporované. |
|  | Získat efektivní seznamy ACL | Podporováno | Ještě není podporované. |
|  | Povolit akcelerované síťové služby | Podporováno | Ještě není podporované. |
|  | Předávání IP | Ve výchozím nastavení zakázané.  Je možné povolit. | Při přepnutí tohoto nastavení se nepodporuje.  Na ve výchozím nastavení. |
|  | Skupiny zabezpečení aplikací | Podporováno | Ještě není podporované. |
|  | Popisek názvu interního serveru DNS | Podporováno | Ještě není podporované. |
|  | Verze privátní IP adresa | Jsou podporovány IPv6 a IPv4. | Podporovaný je jenom protokol IPv4. |
|  | Adresa MAC statická | Nepodporuje se | Nepodporuje se. Každý systém Azure Stack používá stejný fond adres MAC. |
| Network Watcher | Možnosti monitorování sítě tenanta sledovací proces sítě | Podporováno | Ještě není podporované. |
| CDN | Profily síť pro doručování obsahu | Podporováno | Ještě není podporované. |
| Application Gateway | Vyrovnávání zatížení vrstvy 7 | Podporováno | Ještě není podporované. |
| Traffic Manager | Směrování příchozího provozu pro zajištění optimálního výkonu aplikací a spolehlivost. | Podporováno | Ještě není podporované. |
| ExpressRoute | Nastavení rychlého privátního připojení ke cloudovým službám Microsoftu z vaší místní infrastruktury nebo společně umístěného zařízení. | Podporováno | Podpora pro připojení služby Azure Stack k okruhu Express Route. |

## <a name="next-steps"></a>Další postup

[DNS v Azure Stacku](azure-stack-dns.md)
