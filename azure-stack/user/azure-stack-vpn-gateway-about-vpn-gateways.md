---
title: Vytváření bran sítě VPN pro centrum Azure Stack | Microsoft Docs
description: Vytvořte a nakonfigurujte brány VPN pro centrum Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: f12cf32a3269a850b7dacda5ed97e0a4c886828b
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818600"
---
# <a name="create-vpn-gateways-for-azure-stack-hub"></a>Vytváření bran sítě VPN pro centrum Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Než budete moct poslat síťový provoz mezi virtuální sítí Azure a vaší místní lokalitou, musíte pro svoji virtuální síť vytvořit bránu virtuální sítě (VPN).

Brána VPN je typem brány virtuální sítě, která odesílá šifrovaný síťový provoz přes veřejné spojení. Brány VPN můžete použít k bezpečnému posílání provozu mezi virtuálními sítěmi v Azure Stackovém centru a virtuální sítí v Azure. Mohou také odesílat provoz bezpečně mezi virtuální sítí a jiné síti, která je připojena k zařízení VPN.

Při vytváření brány virtuální sítě musíte určit typ brány, který chcete vytvořit. Centrum Azure Stack podporuje jeden typ brány virtuální sítě: typ **sítě VPN** .

Každá virtuální síť může mít dvě brány virtuální sítě, ale každého typu jenom jednu. V závislosti na nastavení, které zvolíte, můžete k jedné bráně VPN vytvořit několik připojení. Příkladem tohoto typu instalace je konfigurace připojení s více lokalitami.

Než vytvoříte a nakonfigurujete brány VPN pro centrum Azure Stack, přečtěte si téma [požadavky na síť služby Azure Stack hub](azure-stack-network-differences.md) , kde se dozvíte, jak se konfigurace centra Azure Stack liší od Azure.

>[!NOTE]
>V Azure se propustnost šířky pásma pro SKLADOVOU položku brány VPN, kterou zvolíte, musí rozdělit mezi všechna připojení, která jsou připojená k bráně. V Azure Stack hub se ale hodnota šířky pásma pro SKU brány VPN použije u každého prostředku připojení, který je připojený k bráně.
>
> Příklad:
>
> * V Azure může základní propustnost služby VPN Gateway vyhovět přibližně 100 MB/s agregované propustnosti. Pokud vytvoříte dvě připojení k této bráně VPN a jedno připojení používá 50 MB/s šířky pásma, pak je k dispozici 50 MB/s pro druhé připojení.
> * V centru Azure Stack se každé připojení k skladové jednotce základní brány VPN přiděluje 100 MB/s propustnost.

## <a name="configuring-a-vpn-gateway"></a>Konfigurace služby VPN Gateway

Připojení brány VPN se spoléhá na několik prostředků nakonfigurovaných se specifickými nastaveními. Většina těchto prostředků je možné nakonfigurovat jednotlivě, ale v některých případech musí být nakonfigurovány v určitém pořadí.

### <a name="settings"></a>Nastavení

Nastavení, které zvolíte pro každý prostředek, je důležité pro vytvoření úspěšného připojení.

Informace o jednotlivých prostředcích a nastaveních pro bránu VPN najdete v tématu [informace o nastavení služby VPN Gateway pro centrum Azure Stack](azure-stack-vpn-gateway-settings.md). Tento článek vám pomůže pochopit tyto informace:

* Typy bran, typy sítí VPN a typy připojení.
* Podsítě brány, místní síťové brány a další nastavení prostředků, které můžete chtít zvážit.

### <a name="deployment-tools"></a>Nástroje pro nasazení

Můžete vytvořit a nakonfigurovat prostředky pomocí konfiguračního nástroje, jako je například na webu Azure portal. Později můžete přepnout na jiný nástroj, například PowerShell, a nakonfigurovat další prostředky nebo upravit stávající prostředky, pokud je to možné. V současné době nelze konfigurovat všechny prostředky a nastavení prostředků pomocí webu Azure Portal. Pokyny v článcích pro každou topologii připojení určují, kdy je zapotřebí specifický konfigurační nástroj.

## <a name="connection-topology-diagrams"></a>Diagramy topologie připojení

K dispozici jsou různé konfigurace pro připojení brány VPN. Určete, která konfigurace bude nejlépe vyhovovat vašim potřebám. V následujících částech si můžete zobrazit diagramy topologie a informace o následujících připojení brány VPN:

* dostupný model nasazení,
* dostupné konfigurační nástroje,
* odkazy na příslušný článek, pokud existuje.

Diagramy a popisy v následujících částech můžete výběrem topologie připojení tak, aby odpovídaly vašim požadavkům. Diagramy popisují základní topologie, ale je možné vytvořit komplexnější konfigurace s použitím diagramů jako vodítko.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Síť typu Site-to-site a Multi-Site (tunel VPN IPsec/IKE)

### <a name="site-to-site"></a>Site-to-Site

Připojení brány VPN typu *site-to-site* (S2S) je připojení přes tunelové připojení VPN pomocí protokolu IPSec/IKE (IKEv2). Tento typ připojení vyžaduje zařízení VPN, které je umístěné místně a má přiřazenou veřejnou IP adresu. Toto zařízení nemůže být umístěné za překladem adres (NAT). Připojení S2S můžete použít pro konfigurace mezi různými místy a pro hybridní konfigurace.

![Příklad konfigurace připojení Site-to-site VPN](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>více lokalit

Připojení k *více lokalitám* je varianta připojení typu Site-to-site. Z brány virtuální sítě vytvoříte několik připojení VPN, obvykle pro připojení k několika místním lokalitám. Při práci s více připojeními je nutné použít typ sítě VPN založený na trasách (označovaný jako dynamická brána při práci s klasickým virtuální sítě). Vzhledem k tomu, že virtuální síť může mít jenom jednu bránu virtuální sítě, všechna připojení prostřednictvím brány sdílejí dostupnou šířku pásma.

![Příklad propojení Multi-Site pomocí Azure VPN Gateway](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Skladové jednotky (SKU) brány

Když vytváříte bránu virtuální sítě pro centrum Azure Stack, zadáte SKU brány, kterou chcete použít. Podporují se tyto skladové položky brány sítě VPN:

* Úroveň Basic
* Úroveň Standard
* Vysoký výkon

Když vyberete vyšší SKU brány, jako je například Standard over Basic nebo vysoký výkon oproti standardu nebo základní, bude bráně přiděleno více procesorů a šířky pásma sítě. V důsledku toho může brána podporovat vyšší propustnost sítě k virtuální síti.

Centrum Azure Stack nepodporuje SKLADOVOU položku brány pro ultra Performance, která se používá výhradně se službou Express Route.

Při výběru SKU Vezměte v úvahu následující:

* Centrum Azure Stack nepodporuje brány založené na zásadách.
* Protokol BGP (Border Gateway) není podporován v základní SKU.
* ExpressRoute – v centru Azure Stack se nepodporuje souběžné konfigurace brány VPN.

## <a name="gateway-availability"></a>Dostupnost brány

Scénáře s vysokou dostupností je možné nakonfigurovat jenom na SKU pro připojení **brány s vysokým výkonem** . Na rozdíl od Azure, který poskytuje dostupnost prostřednictvím konfigurace aktivních/aktivních i aktivních/pasivních konfigurací, služba Azure Stack hub podporuje jenom konfiguraci typu aktivní/pasivní.

### <a name="failover"></a>Převzetí služeb při selhání

V Azure Stackovém centru existují tři virtuální počítače infrastruktury s více klienty. Dva z těchto virtuálních počítačů jsou v aktivním režimu a třetí je v redundantním režimu. Aktivní virtuální počítače umožňují vytváření připojení k síti VPN a redundantní virtuální počítač akceptuje připojení VPN jenom v případě, že dojde k převzetí služeb při selhání. Pokud virtuální počítač aktivní brány přestane být dostupný, připojení VPN se po krátké době (několik sekund) převezme k redundantnímu virtuálnímu počítači po krátkou dobu (několik sekund) ztráty připojení.

## <a name="estimated-aggregate-throughput-by-sku"></a>Odhadovaná agregovaná propustnost podle typů SKU

Následující tabulka ukazuje typy brány a odhadovanou agregovanou propustnost pomocí SKU brány:

|| Propustnost brány sítě VPN (1) | Maximální počet tunelových propojení IPsec brány sítě VPN (2) |
|-------|-------|-------|
|**Základní SKU** **(3)** | 100 Mb/s | 20 |
|**Standardní SKU** | 100 Mb/s | 20 |
|**SKU s vysokým výkonem** | 200 Mb/s | 10 |

### <a name="table-notes"></a>Poznámky tabulky

**(1)** – propustnost sítě VPN není zaručená propustnost pro připojení mezi různými místy přes Internet. Jedná se o maximální možné měření propustnosti.  
**(2)** – maximální počet tunelů je celkem na nasazení centra Azure Stack pro všechna předplatná.  
**(3)** – pro základní SKU není podporováno směrování protokolu BGP.

>[!NOTE]
>Mezi dvěma nasazeními centra Azure Stack lze vytvořit pouze jedno připojení typu Site-to-Site VPN. Důvodem je omezení platformy, která umožňuje jenom jedno připojení VPN ke stejné IP adrese. Vzhledem k tomu, že centrum Azure Stack využívá víceklientské brány, která používá jednu veřejnou IP adresu pro všechny brány VPN v systému služby Azure Stack hub, může být mezi dvěma Azure Stackmi systémy pouze jedno připojení VPN. Toto omezení platí i pro připojení více než jednoho připojení VPN typu Site-to-site k libovolné bráně VPN, která používá jednu IP adresu. Centrum Azure Stack neumožňuje vytvoření více než jednoho prostředku brány místní sítě pomocí stejné IP adresy.

## <a name="next-steps"></a>Další kroky

* [Nastavení konfigurace brány VPN pro centrum Azure Stack](azure-stack-vpn-gateway-settings.md)
