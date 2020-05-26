---
title: Konfigurace připojení VPN typu Site-to-site pomocí protokolu IPsec/IKE v centru Azure Stack
description: Přečtěte si další informace o a konfiguraci zásad IPsec/IKE pro připojení typu Site-to-Site VPN nebo VNet-to-VNet v centru Azure Stack.
author: sethmanheim
ms.custom: contperfq4
ms.topic: article
ms.date: 05/21/2020
ms.author: sethm
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: fdc1f71e5d4c5afa8b3989b69795d150cf96de67
ms.sourcegitcommit: d69eacbf48c06309b00d17c82ebe0ce2bc6552df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780686"
---
# <a name="configure-ipsecike-policy-for-site-to-site-vpn-connections"></a>Konfigurace zásad IPsec/IKE pro připojení site-to-site VPN

Tento článek vás provede jednotlivými kroky konfigurace zásad IPsec/IKE pro připojení VPN typu Site-to-Site (S2S) v centru Azure Stack.

>[!NOTE]
> Abyste mohli tuto funkci používat, musíte mít spuštěný Azure Stack centra sestavení **1809** nebo novější.  Pokud aktuálně spouštíte sestavení před 1809, aktualizujte systém Azure Stack hub na nejnovější sestavení, než budete pokračovat postupem v tomto článku.

## <a name="ipsec-and-ike-policy-parameters-for-vpn-gateways"></a>Parametry zásad IPsec a IKE pro brány VPN Gateway

Protokol IPsec a IKE standard podporuje široké spektrum kryptografických algoritmů v různých kombinacích. Pokud chcete zjistit, které parametry jsou v Azure Stackovém centru podporované, abyste mohli splnit požadavky na dodržování předpisů nebo zabezpečení, přečtěte si téma [parametry protokolu IPSec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters).

Tento článek poskytuje pokyny, jak vytvořit a nakonfigurovat zásadu IPsec/IKE a použít ji pro nové nebo existující připojení.

## <a name="considerations"></a>Požadavky

Při používání těchto zásad Vezměte v vědomí následující důležité informace:

- Zásady IPsec/IKE fungují jenom na SKU brány *Standard* a *HighPerformance* (na základě tras).

- Pro jedno připojení můžete zadat pouze jednu kombinaci zásad.

- Je nutné zadat všechny algoritmy a parametry pro protokol IKE (hlavní režim) i pro protokol IPsec (rychlý režim). Zadání částečných zásad není povoleno.

- Pokud chcete zajistit, aby se zásady na místních zařízeních VPN podporovaly, kontaktujte specifikace dodavatele zařízení VPN. Připojení Site-to-site nelze navázat, pokud jsou zásady nekompatibilní.

### <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že máte následující požadavky:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).

- Rutiny Azure Resource Manager PowerShellu. Další informace o instalaci rutin PowerShellu najdete v tématu [instalace PowerShellu pro Azure Stack hub](../operator/azure-stack-powershell-install.md).

## <a name="part-1---create-and-set-ipsecike-policy"></a>Část 1 – Vytvoření a nastavení zásad IPsec/IKE

Tato část popisuje kroky potřebné k vytvoření a aktualizaci zásad IPsec/IKE na připojení VPN typu Site-to-site:

1. Vytvořte virtuální síť a bránu VPN.

2. Vytvořte bránu místní sítě pro připojení mezi různými místy.

3. Vytvořte zásadu IPsec/IKE s vybranými algoritmy a parametry.

4. Vytvořte připojení IPSec pomocí zásad IPsec/IKE.

5. Přidat nebo aktualizovat nebo odebrat zásady IPsec/IKE pro existující připojení.

Pokyny v tomto článku vám pomůžou nastavit a nakonfigurovat zásady IPsec/IKE, jak je znázorněno na následujícím obrázku:

![Nastavení a konfigurace zásad IPsec/IKE](media/azure-stack-vpn-s2s/site-to-site.svg)

## <a name="part-2---supported-cryptographic-algorithms-and-key-strengths"></a>Část 2 – podporované kryptografické algoritmy a síly klíčů

Následující tabulka uvádí podporované kryptografické algoritmy a síly klíčů konfigurovatelné pomocí centra Azure Stack:

| IPsec/IKEv2                                          | Možnosti                                                                  |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Šifrování protokolem IKEv2                                     | AES256, AES192, AES128, DES3, DES                                        |
| Integrita protokolu IKEv2                                      | SHA384, SHA256, SHA1, MD5                                                |
| Skupina DH                                             | ECP384, ECP256, DHGroup24, DHGroup14, DHGroup2, DHGroup1                 |
| Šifrování protokolem IPsec                                     | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Žádné |
| Integrita protokolu IPsec                                      | GCMASE256, GCMAES192, GCMAES128                                          |
| Skupina PFS                                            | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, PFSMM, None                  |
| Doba života přidružení zabezpečení v rychlém režimu                                       | (Volitelné: použijí se výchozí hodnoty, pokud není zadaný)<br />                         Sekundy (integer; min. 300 / výchozí hodnota 27 000 sekund)<br />                         Kilobajty (integer; min. 1024 / výchozí hodnota 102 400 000 kilobajtů) |
| Selektor provozu                                     | Selektory přenosu na základě zásad nejsou v Azure Stackovém centru podporovány.         |

- Konfigurace vašeho místního zařízení VPN musí odpovídat zásadám brány Azure VPN Gateway nebo musí obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec/IKE Azure:

  - Šifrovací algoritmus IKE (hlavní režim/fáze 1).
  - Algoritmus integrity protokolu IKE (hlavní režim/fáze 1).
  - Skupina DH (hlavní režim/fáze 1)
  - Šifrovací algoritmus IPsec (rychlý režim/fáze 2).
  - Algoritmus integrity protokolu IPsec (rychlý režim/fáze 2).
  - Skupina PFS (rychlý režim/fáze 2).
  - Životnosti přidružení zabezpečení jsou pouze místní specifikace a nemusejí odpovídat.

- Pokud se jako šifrovací algoritmus protokolu IPsec používá GCMAES, musíte vybrat stejný algoritmus GCMAES a délku klíče pro integritu protokolu IPsec. například použití GCMAES128 pro obojí.

- V tabulce:

  - IKEv2 odpovídá hlavnímu režimu nebo fázi 1.
  - Protokol IPsec odpovídá rychlému režimu nebo fázi 2.
  - Skupina DH určuje skupinu Diffie-Hellmen používanou v hlavním režimu nebo ve fázi 1.
  - Skupina PFS určuje skupinu Diffie-Hellmen použitou v rychlém režimu nebo ve fázi 2.

- Životnost přidružení zabezpečení v hlavním režimu IKEv2 je opravena na 28 800 sekund v bránách VPN centra Azure Stack.

V následující tabulce jsou uvedeny odpovídající skupiny Diffie-Hellman podporované vlastními zásadami:

| Skupina Diffie-Hellman | DHGroup   | PFSGroup      | Délka klíče    |
|----------------------|-----------|---------------|---------------|
| 1                    | DHGroup1  | PFS1          | 768bitová skupina MODP  |
| 2                    | DHGroup2  | PFS2          | 1024bitová skupina MODP |
| 14                   | DHGroup14<br/>DHGroup2048 | PFS2048       | 2048bitová skupina MODP |
| 19                   | ECP256    | ECP256        | 256bitová skupina ECP   |
| 20                   | ECP384    | ECP384        | 384bitová skupina ECP   |
| 24                   | DHGroup24 | PFS24         | 2048bitová skupina MODP |

Další informace najdete na stránkách [RFC3526](https://tools.ietf.org/html/rfc3526) a [RFC5114](https://tools.ietf.org/html/rfc5114).

## <a name="part-3---create-a-new-site-to-site-vpn-connection-with-ipsecike-policy"></a>Část 3 – vytvoření nového připojení VPN typu Site-to-site pomocí zásad IPsec/IKE

V této části se seznámíte s postupem vytvoření připojení VPN typu Site-to-site pomocí zásad IPsec/IKE. V následujících krocích se vytvoří připojení, jak je znázorněno na následujícím obrázku:

![Site-to-site – zásady](media/azure-stack-vpn-s2s/site-to-site.svg)

Podrobnější pokyny pro vytvoření připojení VPN typu Site-to-site najdete v tématu [vytvoření připojení VPN typu Site-to-site](/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell).

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Krok 1 – vytvoření virtuální sítě, brány sítě VPN a brány místní sítě

#### <a name="1-declare-variables"></a>1. deklarujete proměnné

Pro toto cvičení začněte deklarací následujících proměnných. Nezapomeňte nahradit zástupné symboly vlastními hodnotami při konfiguraci produkčního prostředí:

```powershell
$Sub1 = "<YourSubscriptionName>"
$RG1 = "TestPolicyRG1"
$Location1 = "East US 2"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPconf1 = "gw1ipconf1"
$Connection16 = "VNet1toSite6"
$LNGName6 = "Site6"
$LNGPrefix61 = "10.61.0.0/16"
$LNGPrefix62 = "10.62.0.0/16"
$LNGIP6 = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojte se k předplatnému a vytvořte novou skupinu prostředků.

Ujistěte se, že jste přešli do režimu prostředí PowerShell, aby bylo možné používat rutiny Resource Manageru. Další informace najdete v tématu [připojení k Azure Stack hub pomocí prostředí PowerShell jako uživatel](azure-stack-powershell-configure-user.md).

Otevřete konzolu PowerShellu a připojte se ke svému účtu. například:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. Vytvořte virtuální síť, bránu VPN a bránu místní sítě.

Následující příklad vytvoří virtuální síť, **virtuální sítě testvnet1**spolu se třemi podsítěmi a bránou VPN. Při nahrazování hodnot je důležité specificky pojmenovat podsíť brány **GatewaySubnet**. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1

$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" `
-VirtualNetwork $vnet1

$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 `
-Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1

New-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 `
-Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix `
$LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-site-to-site-vpn-connection-with-an-ipsecike-policy"></a>Krok 2 – Vytvoření připojení VPN typu Site-to-site pomocí zásad IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. vytvoření zásady IPsec/IKE

Tento ukázkový skript vytvoří zásadu IPsec/IKE s následujícími algoritmy a parametry:

- IKEv2: AES128, SHA1, DHGroup14
- IPsec: AES256, SHA256, None, životnost SA 14400 sekund a 102400000KB

```powershell
$ipsecpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup none -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Pokud pro protokol IPsec používáte GCMAES, musíte použít stejný algoritmus GCMAES a délku klíče pro šifrování a integritu protokolu IPsec.

#### <a name="2-create-the-site-to-site-vpn-connection-with-the-ipsecike-policy"></a>2. vytvoření připojení VPN typu Site-to-site pomocí zásad IPsec/IKE

Vytvořte připojení VPN typu Site-to-site a použijte zásady IPsec/IKE, které jste vytvořili dříve:

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$lng6 = Get-AzureRmLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'Azs123'
```

> [!IMPORTANT]
> Jakmile se v připojení zadá zásada IPsec/IKE, brána Azure VPN Gateway jenom pošle nebo přijme návrh IPsec/IKE se zadanými kryptografickými algoritmy a silnými klíči v tomto konkrétním připojení. Ujistěte se, že vaše místní zařízení VPN pro připojení používá nebo přijímá přesnou kombinaci zásad, jinak nelze vytvořit tunel VPN typu Site-to-site.

## <a name="part-4---update-ipsecike-policy-for-a-connection"></a>Část 4 – aktualizace zásad IPsec/IKE pro připojení

Předchozí část ukázala, jak spravovat zásady protokolu IPsec/IKE pro existující připojení Site-to-site. Tato část vás provede následujícími operacemi s připojením:

- Zobrazí zásady IPsec/IKE připojení.
- Přidejte nebo aktualizujte zásady IPsec/IKE pro připojení.
- Odeberte zásadu IPsec/IKE z připojení.

> [!NOTE]
> Zásady IPsec/IKE se podporují jenom pro brány VPN založené na *standardech* a *HighPerformance* trasách. Nefunguje na *základní* skladové jednotce brány.

### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. zobrazení zásad IPsec/IKE připojení

Následující příklad ukazuje, jak získat zásadu IPsec/IKE nakonfigurovanou pro připojení. Skripty také pokračují v předchozích cvičeních:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Poslední příkaz vypíše aktuální zásadu IPsec/IKE nakonfigurovanou v připojení, pokud existuje. V následujícím příkladu je ukázkový výstup pro připojení:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

Pokud nejsou nakonfigurované žádné zásady IPsec/IKE, příkaz vrátí `$connection6.policy` prázdnou hodnotu Return. Neznamená to, že protokol IPsec/IKE není pro připojení nakonfigurovaný. znamená to, že nejsou k dispozici žádné vlastní zásady IPsec/IKE. Skutečné připojení používá výchozí zásady sjednané mezi místním zařízením VPN a službou Azure VPN Gateway.

### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. Přidání nebo aktualizace zásad IPsec/IKE pro připojení

Postup přidání nové zásady nebo aktualizace existující zásady u připojení je stejný: Vytvořte novou zásadu a pak použijte novou zásadu pro připojení:

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6 = New-AzureRmIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

$connection6.SharedKey = "AzS123"

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Připojení můžete znovu získat a ověřit, jestli se zásada aktualizovala:

```powershell
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Měl by se zobrazit výstup z posledního řádku, jak je znázorněno v následujícím příkladu:

```shell
SALifeTimeSeconds : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption : AES256
IpsecIntegrity : SHA256
IkeEncryption : AES128
IkeIntegrity : SHA1
DhGroup : DHGroup14
PfsGroup : None
```

### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. odebrání zásad IPsec/IKE z připojení

Když z připojení odeberete vlastní zásady, brána Azure VPN Gateway se vrátí k [výchozímu návrhu protokolu IPSec/IKE](azure-stack-vpn-gateway-settings.md#ipsecike-parameters)a znovu vyjedná s vaším místním zařízením VPN.

```powershell
$RG1 = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6 = Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.SharedKey = "AzS123"
$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

Stejný skript můžete použít ke kontrole, zda byla zásada odebrána z připojení.

## <a name="next-steps"></a>Další kroky

- [Nastavení konfigurace brány VPN pro službu Azure Stack Hub](azure-stack-vpn-gateway-settings.md)
