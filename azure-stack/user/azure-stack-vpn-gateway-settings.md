---
title: Konfigurace nastavení služby VPN Gateway pro Azure Stack | Microsoft Docs
description: Přečtěte si o nastaveních služby VPN Gateway a nakonfigurujte je Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 53a423ebc8e9f503934bfd3df2f4962a7b584059
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658585"
---
# <a name="configure-vpn-gateway-settings-for-azure-stack"></a>Konfigurace nastavení služby VPN Gateway pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Brána sítě VPN je typem brány virtuální sítě, která odesílá šifrovaný provoz mezi vaší virtuální sítí v Azure Stack a vzdálenou bránou VPN. Vzdálená brána VPN se může nacházet v Azure, v zařízení v datovém centru nebo v zařízení na jiné lokalitě. Pokud je mezi dvěma koncovými body síťové připojení, můžete mezi těmito dvěma sítěmi vytvořit zabezpečené připojení VPN typu Site-to-Site (S2S).

Připojení brány VPN se spoléhá na konfiguraci více prostředků, z nichž každá obsahuje konfigurovatelné nastavení. Tento článek popisuje prostředky a nastavení vztahující se k bráně VPN pro virtuální síť, kterou vytvoříte v modelu nasazení Správce prostředků. Popisy a diagramy topologie pro každé řešení připojení najdete v [o VPN Gateway pro Azure Stack](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Nastavení služby VPN gateway

### <a name="gateway-types"></a>Typy bran

Každá Azure Stack virtuální síť podporuje jednu bránu virtuální sítě, která musí být typu **VPN**.  Tato podpora se liší od Azure, která podporuje další typy.

Když vytvoříte bránu virtuální sítě, musíte se ujistit, že je typ brány správný pro vaši konfiguraci. Brána sítě VPN vyžaduje `-GatewayType Vpn` příznak, například:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Skladové jednotky (SKU) brány

Při vytváření brány virtuální sítě je nutné zadat SKU brány, které chcete použít. Vyberte SKU, které splňují vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA.

Azure Stack nabízí SKU brány VPN, které jsou uvedené v následující tabulce.

| | Propustnost brány VPN Gateway |Maximální počet tunelových propojení IPsec brány VPN Gateway |
|-------|-------|-------|
|**Základní SKU**  | 100 Mb/s  | 20    |
|**Standardní SKU**   | 100 Mb/s  | 20 |
|**SKU s vysokým výkonem** | 200 Mb/s | 10 |

### <a name="resizing-gateway-skus"></a>Změna velikosti SKU brány

Azure Stack nepodporuje změnu velikosti SKU mezi podporovanými staršími SKU.

Podobně Azure Stack nepodporuje změnu velikosti z podporované starší verze SKU (**Basic**, **Standard**a **HighPerformance**) na novější SKU podporovanou Azure (**VpnGw1**, **VpnGw2**a **VpnGw3**).

### <a name="configure-the-gateway-sku"></a>Konfigurace SKU brány

#### <a name="azure-stack-portal"></a>Portál Azure Stack

Pokud k vytvoření Správce prostředků brány virtuální sítě použijete portál Azure Stack, můžete vybrat SKU brány pomocí rozevíracího seznamu. Možnosti odpovídají typu brány a typu sítě VPN, který vyberete.

#### <a name="powershell"></a>PowerShell

Následující příklad prostředí PowerShell Určuje `-GatewaySku` jako **VpnGw1**:

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```

### <a name="connection-types"></a>Typy připojení

V modelu nasazení Správce prostředků Každá konfigurace vyžaduje konkrétní typ připojení brány virtuální sítě. Dostupné hodnoty prostředí PowerShell v Resource Manageru pro `-ConnectionType` jsou:

* **Podporují**

   V následujícím příkladu PowerShellu se vytvoří připojení S2S, které vyžaduje typ připojení IPsec:

   ```powershell
   New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
   -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
   -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

### <a name="vpn-types"></a>Typy sítě VPN

Když vytvoříte bránu virtuální sítě pro konfiguraci brány sítě VPN, musíte zadat typ sítě VPN. Typ sítě VPN, který zvolíte, závisí na topologii připojení, kterou chcete vytvořit. Typ sítě VPN může také záviset na hardwaru, který používáte. Konfigurace S2S vyžadují zařízení VPN. Některá zařízení VPN podporují jenom určitý typ sítě VPN.

> [!IMPORTANT]  
> V současné době Azure Stack podporuje jenom typ sítě VPN založený na trasách. Pokud vaše zařízení podporuje jenom sítě VPN založené na zásadách, pak se připojení k těmto zařízením z Azure Stack nepodporují.  
>
> Kromě toho Azure Stack nepodporuje v současné době používání selektorů přenosu na základě zásad pro brány založené na trasách, protože vlastní konfigurace zásad IPSec/IKE se nepodporují.

* **PolicyBased**: Sítě VPN založené na zásadách šifrují a směrují pakety prostřednictvím tunelů IPsec na základě zásad IPsec nakonfigurovaných s kombinacemi předpon adres mezi vaší místní sítí a Azure Stack VNet. Zásada nebo selektor provozu je obvykle seznam přístupu v konfiguraci zařízení VPN.

  >[!NOTE]
  >**PolicyBased** se podporuje v Azure, ale ne v Azure Stack.

* **RouteBased**: Sítě VPN založené na směrování používají trasy, které jsou nakonfigurované v tabulce předávání IP nebo směrovací tabulky, k přímému směrování paketů na odpovídající rozhraní tunelového propojení. Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásady nebo selektor provozu pro sítě VPN **RouteBased** jsou nakonfigurovány jako libovolné (nebo používají zástupné karty). Ve výchozím nastavení se nedají změnit. Hodnota pro typ VPN **RouteBased** je **RouteBased**.

Následující příklad prostředí PowerShell Určuje `-VpnType` jako **RouteBased**. Když vytvoříte bránu, musíte se ujistit, že `-VpnType` je správná pro vaši konfiguraci.

```powershell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```

### <a name="gateway-requirements"></a>Požadavky na bránu

V následující tabulce jsou uvedeny požadavky na brány VPN.

| |Základní brána sítě VPN založená na zásadách | Základní brána sítě VPN založená na trasách | Standardní brána sítě VPN založená na trasách | Vysoce výkonná brána sítě VPN založená na trasách|
|--|--|--|--|--|
| **Připojení Site-to-Site (připojení S2S)** | Není podporováno | Konfigurace sítě VPN založené na trasách | Konfigurace sítě VPN založené na trasách | Konfigurace sítě VPN založené na trasách |
| **Metoda ověřování**  | Není podporováno | Předsdílený klíč pro připojení S2S  | Předsdílený klíč pro připojení S2S  | Předsdílený klíč pro připojení S2S  |
| **Maximální počet připojení S2S**  | Není podporováno | 20 | 20| 10|
|**Podpora aktivních směrování (BGP)** | Nepodporuje se | Nepodporuje se | Podporováno | Podporováno |

### <a name="gateway-subnet"></a>Podsíť brány

Než vytvoříte bránu sítě VPN, musíte vytvořit podsíť brány. Podsíť brány obsahuje IP adresy, které používají virtuální počítače a služby brány virtuální sítě. Když vytvoříte bránu virtuální sítě, virtuální počítače brány se nasadí do podsítě brány a nakonfigurují s požadovaným nastavením služby VPN Gateway. Nesaďte nic jiného (například další virtuální počítače) do podsítě brány.

>[!IMPORTANT]
>Pro správné fungování podsítě brány je nutné, aby měla název **GatewaySubnet**. Azure Stack používá tento název k identifikaci podsítě, do které chcete nasadit virtuální počítače a služby brány virtuální sítě.

Při vytváření podsítě brány zadáte počet IP adres, které podsíť obsahuje. IP adresy v podsíti brány jsou přiděleny virtuálním počítačům brány a službám brány. Některé konfigurace vyžadují víc IP adres než jiné. Podívejte se na pokyny pro konfiguraci, kterou chcete vytvořit, a ověřte, že podsíť brány, kterou chcete vytvořit, splňuje tyto požadavky.

Měli byste se také ujistit, že má podsíť brány dostatek IP adres pro zpracování dalších budoucích konfigurací. I když můžete vytvořit podsíť brány, která je menší než/29, doporučujeme vytvořit podsíť brány o velikosti/28 nebo větší (/28,/27,/26 atd.). To znamená, že pokud přidáte funkci v budoucnu, nemusíte odtrhnout bránu a pak ji odstranit a znovu vytvořit, aby bylo možné povolit další IP adresy.

Následující příklad Správce prostředků PowerShell ukazuje podsíť brány s názvem **GatewaySubnet**. Můžete vidět, že zápis CIDR určuje/27, což umožňuje dostatek IP adres pro většinu konfigurací, které aktuálně existují.

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Při práci s podsítěmi brány nepřidružujte skupinu zabezpečení sítě (NSG) k podsíti brány. Přidružení skupiny zabezpečení sítě k této podsíti může způsobit, že vaše brána VPN přestane fungovat podle očekávání. Další informace o skupinách zabezpečení sítě najdete v tématu [co je skupina zabezpečení sítě](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Brány místní sítě

Při vytváření konfigurace brány VPN v Azure, brána místní sítě často představuje vaše místní umístění. V Azure Stack představuje všechna vzdálená zařízení VPN, která jsou umístěná mimo Azure Stack. Toto zařízení může být ve vašem datovém centru (nebo na vzdáleném datovém centru) nebo bráně VPN v Azure.

Bráně místní sítě dáte název, veřejnou IP adresu zařízení VPN a určíte předpony adres, které jsou na místním umístění. Azure nahlíží na předpony cílových adres pro síťový provoz, sleduje konfiguraci, kterou jste zadali pro bránu místní sítě, a odpovídajícím způsobem směruje pakety.

Následující příklad prostředí PowerShell vytvoří novou bránu místní sítě:

```powershell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```

Někdy je potřeba upravit nastavení místní síťové brány; například když přidáte nebo upravíte rozsah adres nebo pokud se změní IP adresa zařízení VPN. Další informace najdete v tématu [Úprava nastavení místní síťové brány pomocí PowerShellu](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parametry protokolu IPsec/IKE

Při nastavování připojení VPN v Azure Stack musíte nakonfigurovat připojení na obou koncích. Pokud konfigurujete připojení VPN mezi Azure Stack a hardwarovým zařízením, jako je přepínač nebo směrovač, který funguje jako brána sítě VPN, může toto zařízení vyžadovat další nastavení.

Na rozdíl od Azure, který podporuje více nabídek jako iniciátor i respondér, Azure Stack ve výchozím nastavení podporuje pouze jednu nabídku.  Pokud pro práci se zařízením VPN potřebujete použít jiné nastavení protokolu IPSec/IKE, máte k dispozici více nastavení pro ruční konfiguraci připojení.  Další informace najdete v tématu [Konfigurace zásad IPSec/IKE pro připojení VPN typu Site-to-site](azure-stack-vpn-s2s.md).

### <a name="ike-phase-1-main-mode-parameters"></a>Parametry protokolu IKE fáze 1 (hlavní režim)

| Vlastnost              | Value|
|-|-|
| Verze IKE           | IKEv2 |
|Skupina Diffie-Hellman   | Skupina 2 (1 024 bitů) |
| Metoda ověření | Předsdílený klíč |
|Algoritmy šifrování a hash | AES256, SHA256 |
|Životnost SA (čas)     | 28 800 sekund|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametry protokolu IKE fáze 2 (rychlý režim)

| Vlastnost| Hodnota|
|-|-|
|Verze IKE |IKEv2 |
|Šifrování & algoritmy hash (šifrování)     | GCMAES256|
|Šifrování šifrovacích & algoritmů hash (ověřování) | GCMAES256|
|Životnost SA (čas)  | 27 000 sekund  |
|Životnost SA (kilobajty) | 33 553 408     |
|Metoda Perfect Forward Secrecy (PFS) |Žádné (viz poznámka 1) |
|Detekce mrtvých partnerských zařízení | Podporováno|  

* *Poznámka 1:*  Před verzí 1807 Azure Stack pro metodu PFS (Perfect Forward Secrecy) používala hodnotu PFS2048.

## <a name="next-steps"></a>Další kroky

* [Připojení pomocí ExpressRoute](../operator/azure-stack-connect-expressroute.md)
