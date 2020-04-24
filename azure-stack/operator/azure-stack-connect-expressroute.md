---
title: Připojení centra Azure Stack k Azure pomocí ExpressRoute
description: Naučte se, jak propojit virtuální sítě v Azure Stack hub k virtuálním sítím v Azure pomocí ExpressRoute.
author: sethmanheim
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2019
ms.openlocfilehash: 1b68435317136afdbfcc5d1ade16b18a2210baad
ms.sourcegitcommit: a3ae6dd8670f8fb24224880df7eee256ebbcc4ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772699"
---
# <a name="connect-azure-stack-hub-to-azure-using-azure-expressroute"></a>Připojení centra Azure Stack k Azure pomocí Azure ExpressRoute

Tento článek popisuje, jak připojit virtuální síť centra Azure Stack k virtuální síti Azure pomocí [Microsoft Azure ExpressRoute](/azure/expressroute/) přímého připojení.

Tento článek můžete použít jako kurz a příklady můžete použít k nastavení stejného testovacího prostředí. Případně můžete článek použít jako návod, který vás provede nastavením vlastního prostředí ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Přehled, předpoklady a předpoklady

Azure ExpressRoute umožňuje rozmístit vaše místní sítě do cloudu Microsoftu přes soukromé připojení, které poskytuje poskytovatel připojení. ExpressRoute není připojení VPN přes veřejný Internet.

Další informace o Azure ExpressRoute najdete v tématu [Přehled ExpressRoute](/azure/expressroute/expressroute-introduction).

### <a name="assumptions"></a>Předpoklady

V tomto článku se předpokládá, že:

* Máte praktické znalosti Azure.
* Máte základní znalosti centra Azure Stack.
* Máte základní znalosti o sítích.

### <a name="prerequisites"></a>Požadavky

Pokud chcete připojit Azure Stack hub a Azure pomocí ExpressRoute, musíte splnit následující požadavky:

* Zřízený [okruh ExpressRoute](/azure/expressroute/expressroute-circuit-peerings) prostřednictvím [poskytovatele připojení](/azure/expressroute/expressroute-locations).
* Předplatné Azure pro vytvoření okruhu ExpressRoute a virtuální sítě v Azure.
* Směrovač, který musí:
  * Podpora připojení VPN typu Site-to-site mezi rozhraním LAN a bránou víceklientské architektury centra Azure Stack.
  * Podpora vytváření více VRFs (virtuální směrování a předávání), pokud je v nasazení centra Azure Stack více než jeden tenant.
* Směrovač, který má:
  * Port sítě WAN připojený k okruhu ExpressRoute.
  * Port LAN připojený k bráně Azure Stack hub pro více tenantů.

### <a name="expressroute-network-architecture"></a>Architektura sítě ExpressRoute

Následující obrázek ukazuje centrum Azure Stack a prostředí Azure po dokončení nastavení ExpressRoute pomocí příkladů v tomto článku:

![ExpressRoute síť](media/azure-stack-connect-expressroute/conceptual.svg)

Následující obrázek ukazuje, jak se více klientů připojuje z infrastruktury centra Azure Stack k Azure prostřednictvím směrovače ExpressRoute:

![Připojení s více klienty pomocí ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

V příkladu v tomto článku se používá stejná víceklientská architektura jako v tomto diagramu k připojení centra Azure Stack k Azure pomocí privátního partnerského vztahu ExpressRoute. Připojení se provádí pomocí připojení VPN typu Site-to-Site z brány virtuální sítě v Azure Stack hub ke směrovači ExpressRoute.

V krocích v tomto článku se dozvíte, jak vytvořit ucelené připojení mezi dvěma virtuální sítěy ze dvou různých tenantů v centru Azure Stack a odpovídající virtuální sítě v Azure. Nastavení dvou tenantů je volitelné. Tento postup můžete použít také pro jednoho tenanta.

## <a name="configure-azure-stack-hub"></a>Konfigurace centra Azure Stack

Chcete-li nastavit prostředí centra Azure Stack pro prvního klienta, použijte následující postup jako vodítko. Pokud nastavujete více než jednoho tenanta, opakujte tento postup:

>[!NOTE]
>Tyto kroky ukazují, jak vytvořit prostředky pomocí portálu centra Azure Stack, ale můžete také použít PowerShell.

![Nastavení sítě centra Azure Stack](media/azure-stack-connect-expressroute/azure-stack-connect-expressrouteimage-image2.svg)

### <a name="before-you-begin"></a>Před zahájením

Než začnete s konfigurací centra Azure Stack, budete potřebovat:

* Nasazení centra Azure Stack.
* Nabídka centra Azure Stack, ke které se uživatelé můžou přihlásit Další informace najdete v tématu [Přehled služeb, plánů, nabídek a předplatných](service-plan-offer-subscription-overview.md).

### <a name="create-network-resources-in-azure-stack-hub"></a>Vytváření síťových prostředků v centru Azure Stack

Pro vytvoření požadovaných síťových prostředků v centru Azure Stack pro tenanta použijte následující postupy.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Vytvoření virtuální sítě a podsítě virtuálních počítačů

1. Přihlaste se k portálu pro uživatele centra Azure Stack.

2. Na portálu vyberte **+ vytvořit prostředek**.

3. V části **Azure Marketplace**vyberte **sítě**.

4. V části **Doporučené**vyberte **virtuální síť**.

5. V části **vytvořit virtuální síť**zadejte do příslušných polí hodnoty uvedené v následující tabulce:

   |Pole  |Hodnota  |
   |---------|---------|
   |Název     |Tenant1VNet1         |
   |Adresní prostor     |10.1.0.0/16|
   |Název podsítě     |Tenant1-Sub1|
   |Rozsah adres podsítě     |10.1.1.0/24|

6. V poli **předplatné** by se mělo zobrazovat předplatné, které jste vytvořili dříve. Zbývající pole:

    * V části **Skupina prostředků**vyberte **vytvořit novou** , pokud chcete vytvořit novou skupinu prostředků, nebo pokud ji už máte, vyberte **použít existující**.
    * Ověřte výchozí **umístění**.
    * Klikněte na **Vytvořit**.
    * Volitelné Klikněte na **Připnout na řídicí panel**.

#### <a name="create-the-gateway-subnet"></a>Vytvoření podsítě brány

1. V části **virtuální síť**vyberte **Tenant1VNet1**.
1. V části **NASTAVENÍ** vyberte **Podsítě**.
1. Vyberte **+ podsíť brány** a přidejte tak podsíť brány do virtuální sítě.
1. Ve výchozím nastavení je název této podsítě nastavený na **GatewaySubnet**. Podsítě brány jsou zvláštním případem a musí používat tento název k tomu, aby fungovaly správně.
1. Ověřte, zda je **Rozsah adres** **10.1.0.0/24**.
1. Kliknutím na **OK** vytvořte podsíť brány.

#### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

1. V uživatelském portálu centra Azure Stack klikněte na **+ vytvořit prostředek**.
1. V části **Azure Marketplace**vyberte **sítě**.
1. V seznamu síťových prostředků vyberte **bránu virtuální sítě**.
1. Do pole **název** zadejte **gw1**.
1. Vyberte **virtuální síť**.
1. V rozevíracím seznamu vyberte **Tenant1VNet1** .
1. Vyberte **Veřejná IP adresa**, pak **Zvolte veřejná IP adresa**a pak klikněte na **vytvořit novou**.
1. Do pole **název** zadejte **gw1-PIP**a pak klikněte na **OK**.
1. Jako **Typ VPN** by ve výchozím nastavení měla být vybraná možnost **Založená na trasách**. Toto nastavení zachovejte.
1. Ověřte, že nastavení **Předplatné** a **Umístění** jsou správná. Klikněte na **Vytvořit**.

#### <a name="create-the-local-network-gateway"></a>Vytvoření brány místní sítě

Prostředek brány místní sítě identifikuje vzdálenou bránu na druhém konci připojení VPN. V tomto příkladu je vzdáleným koncem připojení dílčí rozhraní sítě LAN směrovače ExpressRoute. U tenanta 1 v předchozím diagramu je Vzdálená adresa 10.60.3.255.

1. Přihlaste se k portálu User Portal centra Azure Stack a vyberte **+ vytvořit prostředek**.
1. V části **Azure Marketplace**vyberte **sítě**.
1. V seznamu prostředků vyberte **bránu místní sítě**.
1. Do pole **název** zadejte **ER-router-GS**.
1. Pro pole **IP adresa** se podívejte na předchozí obrázek. IP adresa dílčího rozhraní ExpressRoute směrovače sítě LAN pro klienta 1 je 10.60.3.255. Ve svém vlastním prostředí zadejte IP adresu odpovídajícího rozhraní vašeho směrovače.
1. Do pole **adresní prostor** zadejte adresní prostor virtuální sítě, ke kterému se chcete připojit v Azure. Podsítě pro tenanta 1 jsou následující:

   * 192.168.2.0/24 je virtuální síť centra v Azure.
   * 10.100.0.0/16 je virtuální síť rozbočovače v Azure.

   > [!IMPORTANT]
   > V tomto příkladu se předpokládá, že používáte statické trasy pro připojení VPN typu Site-to-site mezi bránou centra Azure Stack a směrovačem ExpressRoute.

1. Ověřte, jestli je vaše **předplatné**, **Skupina prostředků**a **umístění** správné. Pak vyberte **vytvořit**.

#### <a name="create-the-connection"></a>Vytvoření připojení

1. Na portálu Azure Stack hub User Portal vyberte **+ vytvořit prostředek**.
1. V části **Azure Marketplace**vyberte **sítě**.
1. V seznamu prostředků vyberte **Připojení**.
1. V části **základy**vyberte **Typ připojení** **site-to-Site (IPSec)** .
1. Vyberte **předplatné**, **skupinu prostředků**a **umístění**. Klikněte na tlačítko **OK**.
1. V části **Nastavení**vyberte **Brána virtuální sítě**a pak vyberte **gw1**.
1. Vyberte **Brána místní sítě**a pak vyberte **ER router GS**.
1. Do pole **název připojení** zadejte **ConnectToAzure**.
1. Do pole **sdílený klíč (PSK)** zadejte **abc123** a pak vyberte **OK**.
1. V části **Souhrn**vyberte **OK**.

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Získat veřejnou IP adresu brány virtuální sítě

Po vytvoření brány virtuální sítě můžete získat veřejnou IP adresu brány. Tuto adresu si poznamenejte pro případ, že ji pro nasazení budete potřebovat později. V závislosti na vašem nasazení se tato adresa používá jako **interní IP adresa**.

1. V uživatelském portálu Azure Stack centra vyberte **všechny prostředky**.
1. V části **všechny prostředky**vyberte v příkladu bránu virtuální sítě, která je **gw1** .
1. V části **Brána virtuální sítě**vyberte **Přehled** ze seznamu prostředků. Případně můžete vybrat **vlastnosti**.
1. IP adresa, kterou chcete zaznamenat, je uvedena v části **Veřejná IP adresa**. Pro příklad konfigurace je tato adresa 192.68.102.1.

#### <a name="create-a-virtual-machine-vm"></a>Vytvořit virtuální počítač

K otestování přenosů dat přes připojení VPN potřebujete virtuální počítače pro posílání a přijímání dat ve virtuální síti centra Azure Stack. Vytvořte virtuální počítač a nasaďte ho do podsítě virtuálních počítačů pro virtuální síť.

1. Na portálu Azure Stack hub User Portal vyberte **+ vytvořit prostředek**.
1. V části **Azure Marketplace**vyberte **COMPUTE**.
1. V seznamu imagí virtuálních počítačů vyberte Image **Windows Server 2016 Datacenter Eval** .

   >[!NOTE]
   >Pokud bitová kopie použitá pro tento článek není k dispozici, požádejte svého operátora centra Azure Stack, aby poskytoval jinou bitovou kopii Windows serveru.

1. V části **vytvořit virtuální počítač**vyberte **základy**a jako **název**zadejte **VM01** .
1. Zadejte platné uživatelské jméno a heslo. Tento účet použijete pro přihlášení k virtuálnímu počítači po jeho vytvoření.
1. Zadejte **předplatné**, **skupinu prostředků**a **umístění**. Vyberte **OK**.
1. V části **zvolit velikost**vyberte velikost virtuálního počítače pro tuto instanci a pak vyberte **Vybrat**.
1. V části **Nastavení**potvrďte, že:

   * Virtuální síť je **Tenant1VNet1**.
   * Podsíť je nastavená na **10.1.1.0/24**.

   Použijte výchozí nastavení a klikněte na **OK**.

1. V části **Souhrn**Zkontrolujte konfiguraci virtuálního počítače a pak klikněte na **OK**.

Chcete-li přidat další klienty, opakujte postup, který jste provedli v těchto částech:

* [Vytvoření virtuální sítě a podsítě virtuálních počítačů](#create-the-virtual-network-and-vm-subnet)
* [Vytvoření podsítě brány](#create-the-gateway-subnet)
* [Vytvoření brány virtuální sítě](#create-the-virtual-network-gateway)
* [Vytvoření brány místní sítě](#create-the-local-network-gateway)
* [Vytvoření připojení](#create-the-connection)
* [Vytvoření virtuálního počítače](#create-a-virtual-machine)

Pokud používáte jako příklad klienta 2, nezapomeňte změnit IP adresy, aby se předešlo překrytí.

### <a name="configure-the-nat-vm-for-gateway-traversal"></a>Konfigurace virtuálního počítače NAT pro procházení bran

> [!IMPORTANT]
> Tato část je určena jenom pro nasazení ASDK. Překlad adres (NAT) není potřebný pro nasazení ve více uzlech.

ASDK je samostatný a izolovaný od sítě, ve které je nasazený fyzický hostitel. Síť VIP, ke které jsou brány připojené, není externí. je skrytý za směrovačem, který provádí překlad síťových adres (NAT).

Směrovač je ASDK hostitel, na kterém běží role služby Směrování a vzdálený přístup (RRAS). Na hostiteli ASDK je nutné nakonfigurovat překlad adres (NAT), aby připojení VPN typu Site-to-site bylo možné připojit na obou koncích.

#### <a name="configure-the-nat"></a>Konfigurace překladu adres (NAT)

1. Přihlaste se k hostitelskému počítači centra Azure Stack pomocí svého účtu správce.
1. Spusťte skript v PowerShellu se zvýšenými oprávněními ISE. Tento skript vrátí vaši **externí adresu BGPNAT**.

   ```powershell
   Get-NetNatExternalAddress
   ```

1. Pokud chcete nakonfigurovat překlad adres (NAT), zkopírujte a upravte následující skript PowerShellu. Upravte skript tak, aby nahradil `External BGPNAT address` hodnoty `Internal IP address` a pomocí následujících ukázkových hodnot:

   * Pro *externí adresu BGPNAT* použijte 10.10.0.62.
   * Pro *interní IP adresu* použijte 192.168.102.1

   Spusťte následující skript z PowerShellu se zvýšenými oprávněními ISE:

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500
   ```

## <a name="configure-azure"></a>Konfigurace Azure

Po dokončení konfigurace centra Azure Stack můžete nasadit prostředky Azure. Následující obrázek ukazuje příklad virtuální sítě tenanta v Azure. Pro virtuální síť v Azure můžete použít jakékoli schéma názvů a adresování. Rozsah adres virtuální sítě v Azure a centru Azure Stack ale musí být jedinečný a nesmí se překrývat:

![Virtuální sítě Azure](media/azure-stack-connect-expressroute/azurearchitecture.svg)

Prostředky, které nasadíte v Azure, se podobají prostředkům, které jste nasadili v Azure Stack hub. Nasadíte následující komponenty:

* Virtuální sítě a podsítě
* Podsíť brány
* Brána virtuální sítě
* Připojení
* Okruh ExpressRoute

Ukázková síťová infrastruktura Azure je nakonfigurovaná takto:

* Model virtuální sítě Standard hub (192.168.2.0/24) a paprskový (10.100.0.0./16). Další informace o síťové topologii centra a paprsků najdete v tématu [implementace síťové topologie centra s paprsky v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* Úlohy se nasazují ve virtuální síti paprsku a okruh ExpressRoute je připojený k virtuální síti centra.
* Oba virtuální sítě jsou připojené pomocí partnerského vztahu virtuálních sítí.

### <a name="configure-the-azure-vnets"></a>Konfigurace Azure virtuální sítě

1. Přihlaste se k Azure Portal pomocí přihlašovacích údajů Azure.
1. Vytvořte virtuální síť centra pomocí rozsahu adres 192.168.2.0/24.
1. Vytvořte podsíť pomocí rozsahu 192.168.2.0/25 adres a přidejte podsíť brány pomocí rozsahu adres 192.168.2.128/27.
1. Vytvořte virtuální síť a podsíť paprsků pomocí rozsahu adres 10.100.0.0/16.

Další informace o vytváření virtuálních sítí v Azure najdete v tématu [vytvoření virtuální sítě](/azure/virtual-network/manage-virtual-network#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Konfigurace okruhu ExpressRoute

1. Přečtěte si požadavky ExpressRoute v tématu [ExpressRoute požadavky & kontrolní seznam](/azure/expressroute/expressroute-prerequisites).

1. Postupujte podle kroků v části [Vytvoření a úprava okruhu ExpressRoute](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) a vytvořte okruh ExpressRoute pomocí předplatného Azure.

   >[!NOTE]
   >Poskytněte klíč služby pro váš okruh vaší služby, aby mohl na svém konci nastavit okruh ExpressRoute.

1. Postupujte podle kroků v části [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](/azure/expressroute/expressroute-howto-routing-portal-resource-manager) pro konfiguraci privátního partnerského vztahu na okruhu ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Vytvoření brány virtuální sítě

Postupujte podle kroků v části [Konfigurace brány virtuální sítě pro ExpressRoute pomocí PowerShellu](/azure/expressroute/expressroute-howto-add-gateway-resource-manager) a vytvořte bránu virtuální sítě pro ExpressRoute ve virtuální síti centra.

### <a name="create-the-connection"></a>Vytvoření připojení

Pokud chcete propojit okruh ExpressRoute s virtuální sítí centra, postupujte podle kroků v části [připojení virtuální sítě k okruhu ExpressRoute](/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

### <a name="peer-the-vnets"></a>Vytvoření partnerského vztahu virtuálních sítí

Navázat na střed a paprsk virtuální sítě pomocí kroků v části [vytvoření partnerského vztahu virtuálních sítí pomocí Azure Portal](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). Při konfiguraci partnerského vztahu VNet se ujistěte, že používáte následující možnosti:

* Z centra až po paprsek **Povolte přenos brány**.
* Z paprsku do centra **použijte vzdálenou bránu**.

### <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Nasaďte virtuální počítače pro úlohy do virtuální sítě rozbočovače.

Opakujte tento postup pro všechny další virtuální sítě tenanta, které chcete v Azure připojit prostřednictvím svých příslušných okruhů ExpressRoute.

## <a name="configure-the-router"></a>Konfigurace směrovače

Jako vodítko pro konfiguraci směrovače ExpressRoute můžete použít následující diagram konfigurace směrovače ExpressRoute. Tento obrázek ukazuje dva klienty (klienta 1 a tenanta 2) s odpovídajícími okruhy ExpressRoute. Každý tenant je propojen s vlastním VRF (virtuální směrování a předávání) na straně sítě LAN a sítě WAN směrovače ExpressRoute. Tato konfigurace zajišťuje ucelenou izolaci mezi dvěma klienty. Poznamenejte si IP adresy používané v rozhraních směrovačů, jak budete postupovat podle příkladu konfigurace.

![Konfigurace směrovače ExpressRoute](media/azure-stack-connect-expressroute/EndToEnd.png)

K ukončení připojení VPN typu Site-to-Site z centra Azure Stack můžete použít libovolný směrovač, který podporuje IKEv2 VPN a BGP. Stejný směrovač se používá pro připojení k Azure pomocí okruhu ExpressRoute.

Následující příklad konfigurace směrovače Cisco ASR 1000 series agregací služby podporuje síťovou infrastrukturu zobrazenou v diagramu *Konfigurace směrovače ExpressRoute* .

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Otestování připojení

Po vytvoření připojení Site-to-site a okruhu ExpressRoute otestujte připojení.

Proveďte následující testy pro příkazy:

* Přihlaste se k jednomu z virtuálních počítačů ve virtuální síti Azure a otestujte virtuální počítač, který jste vytvořili v Azure Stack hub.
* Přihlaste se k jednomu z virtuálních počítačů, které jste vytvořili v centru Azure Stack hub, a otestujte virtuální počítač, který jste vytvořili ve virtuální síti Azure.

>[!NOTE]
>Abyste se ujistili, že odesíláte přenosy přes připojení typu Site-to-site a ExpressRoute, musíte na obou koncích poslat adresu vyhrazené IP adresy (DIP) a ne virtuální IP adresa virtuálního počítače.

### <a name="allow-icmp-in-through-the-firewall"></a>Povolení protokolu ICMP v bráně firewall

Ve výchozím nastavení Windows Server 2016 nepovoluje příchozí pakety protokolu ICMP přes bránu firewall. U každého virtuálního počítače, který používáte pro testy pomocí testu pro příkazy, je nutné, abyste povolili příchozí pakety protokolu ICMP. Pokud chcete vytvořit pravidlo brány firewall pro protokol ICMP, spusťte v okně PowerShellu se zvýšenými oprávněními následující rutinu:

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-hub-vm"></a>Odeslání požadavku na virtuální počítač centra Azure Stack

1. Přihlaste se k portálu pro uživatele centra Azure Stack.

1. Najděte virtuální počítač, který jste vytvořili, a vyberte ho.

1. Vyberte **Connect** (Připojit).

1. Z příkazového řádku Windows nebo PowerShellu se zvýšenými oprávněními zadejte **ipconfig/all**. Poznamenejte si adresu IPv4 vrácenou ve výstupu.

1. Otestujte adresu IPv4 z virtuálního počítače ve virtuální síti Azure.

   V tomto ukázkovém prostředí je adresa IPv4 z podsítě 10.1.1. x/24. Ve vašem prostředí se adresa může lišit, ale měla by být v podsíti, kterou jste vytvořili pro podsíť virtuální sítě tenanta.

### <a name="view-data-transfer-statistics"></a>Zobrazit statistiku přenosu dat

Pokud chcete zjistit, kolik přenosů prochází přes vaše připojení, najdete tyto informace na portálu pro uživatele centra Azure Stack. Zobrazení statistik přenosu dat je také dobrým způsobem, jak zjistit, jestli vaše data testů pro testování pomocí připojení VPN a ExpressRoute provedla:

1. Přihlaste se k portálu User Portal centra Azure Stack a vyberte **všechny prostředky**.
1. Přejděte do skupiny prostředků pro vaši VPN Gateway a vyberte typ objektu **připojení** .
1. Vyberte připojení **ConnectToAzure** ze seznamu.
1. V části**Přehled** **připojení** > vidíte statistiku pro **data v** nástroji a **data odchozí**. Měly by se zobrazit některé nenulové hodnoty.

   ![Data a odchozí data](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Další kroky

[Nasazení aplikací do Azure a centra Azure Stack](../user/azure-stack-solution-pipeline.md)
