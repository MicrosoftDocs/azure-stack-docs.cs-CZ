---
title: Konfigurace připojení k hybridní cloud s Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat připojení k hybridní cloud s Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: solution
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: affc13f70d1f484b0abb79510f54d92776b58a25
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286856"
---
# <a name="configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Konfigurace připojení k hybridní cloud s Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Přístup k prostředkům se zabezpečením v globální Azure a použití vzoru připojení k hybridní službě Azure Stack.

V tomto řešení vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Zachovejte data místně splňovat zákonné požadavky nebo ochrany osobních údajů, ale zachovat přístup pro globální prostředky Azure.
> - Zachovat starší verze systému při používání nasazení škálovaných cloudových aplikací a prostředků v globální Azure.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí, povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Tento článek [aspekty návrhu pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontroly pro navrhování, nasazování a provozování hybridní pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) aplikace. Aspekty návrhu při optimalizaci návrhu hybridních aplikací, minimalizovat problémy v produkčním prostředí.


## <a name="prerequisites"></a>Požadavky

Několik součásti jsou potřeba k vytvoření hybridního připojení k nasazení. Některé z těchto součástí trvat dobu připravit, takže Plánujte odpovídajícím způsobem.

**Azure Stack**

Partnerem Azure OEM/hardwaru můžete nasadit produkčního prostředí Azure Stack, a všichni uživatelé můžou nasazovat Azure Stack Development Kit (ASDK).

**Komponenty služby Azure Stack**

Operátory Azure stacku musí nasadit službu App Service, vytvořte plány a nabídky, vytvořte předplatné tenanta a přidejte image Windows serveru 2016. Pokud už máte tyto komponenty, ujistěte se, že splňují požadavky před zahájením tohoto řešení.

Toto řešení se předpokládá, že máte některé základní znalosti o Azure a Azure Stack. Další informace před zahájením řešení najdete v následujících článcích:

 - [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Klíčové koncepty služby Azure Stack](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
 - Vytvoření [webovou aplikaci](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) v Azure. Poznamenejte si adresu URL webové aplikace, protože ho budete potřebovat v řešení.

### <a name="azure-stack"></a>Azure Stack

 - Použití služby Azure Stack v produkčním prostředí nebo nasazení Azure Stack Development Kit z https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Nasazení ASDK může trvat až 7 hodin, tak podle toho naplánujte.

 - Nasazení [služby App Service](../operator/azure-stack-app-service-deploy.md) služeb PaaS do služby Azure Stack.
 - [Vytvořit plány a nabídky](../operator/azure-stack-plan-offer-quota-overview.md) v prostředí Azure Stack.
 - [Vytvoření tenanta předplatného](../operator/azure-stack-subscribe-plan-provision-vm.md) v rámci prostředí Azure Stack.

### <a name="before-you-begin"></a>Před zahájením

Ověřte, že splňujete následující kritéria ještě před zahájením konfigurace hybridní připojení ke cloudu:

 - Budete potřebovat veřejnou adresu IPv4 přístupnou zvenčí pro vaše zařízení VPN. Tato IP adresa nemůže být umístěné za službou NAT (Network Address Translation).
 - Všechny prostředky jsou nasazené ve stejné oblasti nebo umístění.

####  <a name="solution-example-values"></a>řešení ukázkové hodnoty

Příklady v tomto řešení používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo můžou sloužit k lepšímu pochopení příkladů. Další informace o VPN gateway nastavení Obecné naleznete v tématu [o nastavení služby VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifikace připojení:

 - **Typ sítě VPN**: založené na trasách
 - **Typ připojení**: site-to-site (IPsec)
 - **Typ brány**: Síť VPN
 - **Název připojení Azure**: Azure-Gateway-AzureStack-S2SGateway (na portálu vyplní tato hodnota)
 - **Název připojení služby Azure Stack**: AzureStack-Gateway-Azure-S2SGateway (na portálu vyplní tato hodnota)
 - **Sdílený klíč**: žádná kompatibilní s hardwarem sítě VPN se shodnými hodnotami na obou stranách připojení
 - **Předplatné**: žádné preferované předplatného
 - **Skupina prostředků**: Test-Infra

Síť a podsíť IP adres:

| Připojení Azure/Azure Stack | Name | Subnet | IP adresa |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Virtuální síť Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Virtuální síť Azure Stack | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Brány virtuální sítě Azure | Azure Gateway |  |  |
| Brána virtuální sítě Azure Stack | AzureStack brány |  |  |
| Veřejná IP adresa Azure | Azure-GatewayPublicIP |  | Určí při vytvoření |
| Veřejná IP adresa Azure Stack | AzureStack-GatewayPublicIP |  | Určí při vytvoření |
| Brány místní sítě Azure | AzureStack S2SGateway<br>   10.100.100.0/23 |  | Hodnota Azure stacku veřejnou IP adresu |
| Brána místní sítě Azure Stack | Azure S2SGateway<br>10.100.102.0/23 |  | Hodnota Azure veřejné IP adresy |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Vytvoření virtuální sítě v globálním Azure a Azure Stack

Následujícím postupem vytvoření virtuální sítě pomocí portálu. Můžete je použít [ukázkové hodnoty](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) Pokud používáte tento článek jako jenom řešení. Pokud používáte v tomto článku Konfigurace produkčním prostředí, příklad nastavení nahraďte vlastními hodnotami.

> [!IMPORTANT]
> Ujistěte se, že není k překrytí IP adres v Azure nebo ve službě Azure Stack adresní prostory virtuální sítě.

Vytvoření virtuální sítě v Azure:

1. Pomocí svého prohlížeče pro připojení k [webu Azure portal](https://portal.azure.com/) a přihlaste se pomocí svého účtu Azure.
2. Vyberte **vytvořit prostředek**. V **Hledat na marketplace** zadejte "virtuální síť". Vyberte **virtuální síť** ve výsledcích.
3. Z **vybrat model nasazení** vyberte **Resource Manageru**a pak vyberte **vytvořit**.
4. Na **vytvořit virtuální síť**, nakonfigurujte nastavení virtuální sítě. Povinná pole názvy mají předponu červenou hvězdičkou.  Při zadání platnou hodnotu hvězdičky se změní na zelená značka zaškrtnutí.

Vytvoření virtuální sítě ve službě Azure Stack:

* Opakujte předchozí kroky (1 – 4) pomocí služby Azure Stack **portál pro klienty**.

## <a name="add-a-gateway-subnet"></a>Přidání podsítě brány

Před připojením virtuální sítě k bráně, je potřeba vytvořit podsíť brány pro virtuální síť, kterou chcete připojit k. Služby brány používají IP adresy, kterou zadáte v podsíti brány.

V [webu Azure portal](https://portal.azure.com/), přejděte na virtuální síť Resource Manageru, kde chcete vytvořit bránu virtuální sítě.

1. Vyberte virtuální síť a otevřete **virtuální síť** stránky.
2. V **nastavení**vyberte **podsítě**.
3. Na **podsítě** stránce **+ podsíť brány** otevřít **přidat podsíť** stránky.

    ![Přidání podsítě brány](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. **Název** pro podsítě se automaticky vyplní hodnotou "GatewaySubnet". Tato hodnota je vyžadována pro Azure podsíť rozpoznala jako podsíť brány.
5. Změnit **rozsah adres** hodnoty, které jsou k dispozici odpovídaly požadavkům vaší konfigurace, a potom vyberte **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Vytvořit bránu virtuální sítě v Azure a Azure Stack

Pomocí následujících kroků a vytvořte bránu virtuální sítě v Azure.

1. Na levé straně stránky portálu vyberte **+** a do vyhledávacího pole zadejte "Brána virtuální sítě".
2. V **výsledky**vyberte **Brána virtuální sítě**.
3. V **Brána virtuální sítě**vyberte **vytvořit** otevřít **vytvořit bránu virtuální sítě** stránky.
4. Na **vytvořit bránu virtuální sítě**, zadejte hodnoty pro bránu sítě, jak je znázorněno v **hodnoty v tomto příkladu**a následující další hodnoty:

   - **Skladová položka**: základní
   - **Virtual Network**: Výběr virtuální sítě, které jste vytvořili dříve. Podsíť brány, kterou jste vytvořili se vybere automaticky.
   - **První konfigurace IP adresy**:  Veřejné IP adresy brány.
     - Vyberte **vytvořit konfiguraci IP adresy brány**, což vás přesměruje na **zvolte veřejnou IP adresu** stránky.
     - Vyberte **+ vytvořit novou** otevřít **vytvoření veřejné IP adresy** stránky.
     - Zadejte **název** pro veřejnou IP adresu. Jako SKU ponechte **základní**a pak vyberte **OK** uložte provedené změny.

       > [!Note]
       > VPN Gateway v současné době podporuje pouze přidělení dynamické veřejné IP adresy. Nicméně to neznamená, že změní IP adresa po přiřazení k vaší bráně VPN. Veřejná IP adresa se změní pouze v případě odstranění a nového vytvoření brány. Změna velikosti, resetování nebo jiné operace údržby/upgradu vaší brány VPN nezmění IP adresu.

4. Ověřte nastavení brány.
5. Vyberte **vytvořit** a vytvořte bránu VPN. Nastavení brány se ověří a na řídicím panelu se zobrazí na dlaždici "Nasazování brány virtuální sítě".

   >[!Note]
   >Vytváření brány může trvat až 45 minut. K zobrazení stavu dokončení může být nutné obnovit stránku portálu.

    Po vytvoření brány můžete zobrazit IP adresa přiřazená zobrazením virtuální sítě na portálu. Brána se zobrazí jako připojené zařízení. Pokud chcete zobrazit další informace o bráně, vyberte zařízení.

6. Opakujte předchozí kroky (1-5) v nasazení Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Vytvořit bránu místní sítě v Azure a Azure Stack

Brána místní sítě obvykle odkazuje na vaše místní umístění. Umístění určíte název, který Azure nebo ve službě Azure Stack může odkazovat na a pak zadejte:

- IP adresa místního zařízení VPN, kterou vytváříte připojení.
- Předpony IP adres, které se budou směrovat přes bránu VPN pro zařízení VPN. Předpony adres, které zadáte, jsou předpony ve vaší místní síti.

  >[!Note]
  >Pokud vaše místní síť se změní nebo potřebujete změnit veřejnou IP adresu pro zařízení VPN, můžete snadno aktualizovat tyto hodnoty později.

1. Na portálu vyberte **+ vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **bránu místní sítě**a pak vyberte **Enter** pro hledání. Zobrazí se seznam výsledků.
3. Vyberte **bránu místní sítě**a pak vyberte **vytvořit** otevřít **vytvořit bránu místní sítě** stránky.
4. Na **vytvořit bránu místní sítě**, zadejte hodnoty brány místní sítě, pomocí našich **hodnoty v tomto příkladu**. Zahrnout následující další hodnoty.

    - **IP adresa**: Veřejná IP adresa zařízení VPN, který chcete připojit k Azure nebo Azure Stack. Zadejte platnou veřejnou IP adresu, která není za službou NAT, takže Azure přístup k adrese. Pokud momentálně nemáte IP adresu, můžete použít hodnotu jako v příkladu jako zástupný znak, ale budete se muset vrátit zpět a nahradit zástupnou hodnotu s veřejnou IP adresu zařízení VPN. Azure se nemůže připojit k zařízení, dokud je zadat platnou adresu.
    - **Adresní prostor**: má rozsah sítě, kterou tato místní síť představuje adres. Můžete přidat více různých rozsahů adres. Ujistěte se, že rozsahy nepřekrývají s rozsahy jiných sítí, které chcete připojit k. Azure bude směrovat zadaný rozsah adres na místní IP adresu zařízení VPN. Použijte vlastní hodnoty, pokud se chcete připojit k místní lokalitě, nikoli příkladem hodnoty.
    - **Konfigurovat nastavení protokolu BGP**: Používejte jenom při konfiguraci BGP. V opačném případě tuto možnost nevybírejte.
    - **Předplatné**: Ověřte, že se zobrazuje správné předplatné.
    - **Skupina prostředků**: Vyberte skupinu prostředků, kterou chcete použít. Můžete buď vytvořit novou skupinu prostředků nebo vyberte ten, který jste vytvořili.
    - **Umístění**: Vyberte umístění, které tento objekt se vytvoří v. Můžete chtít vybrat stejné umístění, která se nachází vaše virtuální síť v, ale není potřeba udělat.
5. Když dokončíte zadáním požadované hodnoty, vyberte **vytvořit** a vytvořte bránu místní sítě.
6. Opakujte tyto kroky (1-5) v nasazení Azure Stack.

## <a name="configure-your-connection"></a>Konfigurace připojení

Připojení Site-to-site k místní síti vyžadují zařízení VPN. Zařízení VPN, které jste nakonfigurovali, se označuje jako připojení. Konfigurace připojení, budete potřebovat:

- Sdílený klíč. Tento klíč je stejný sdílený klíč, který zadáváte při vytváření připojení VPN site-to-site. V našich ukázkách používáme základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
- Veřejnou IP adresu brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Chcete-li najít veřejnou IP adresu brány VPN pomocí webu Azure portal, přejděte do brány virtuální sítě a pak vyberte název brány.

Použijte následující postup k vytvoření připojení VPN typu site-to-site mezi bránou virtuální sítě a vaše místní zařízení VPN.

1. Na webu Azure Portal, vyberte **+ vytvořit prostředek**.
2. Vyhledejte **připojení**.
3. V **výsledky**vyberte **připojení**.
4. Na **připojení**vyberte **vytvořit**.
5. Na **vytvořit připojení**, nakonfigurujte následující nastavení:

    - **Typ připojení**: Vyberte site-to-site (IPSec).
    - **Skupina prostředků**: Vyberte testovací skupinu prostředků.
    - **Virtual Network Gateway**: Vyberte bránu virtuální sítě, kterou jste vytvořili.
    - **Brána místní sítě**: Vyberte bránu místní sítě, kterou jste vytvořili.
    - **Název připojení**: Tento název se vyplní automaticky z hodnot dvě brány.
    - **Sdílený klíč**: Tato hodnota musí odpovídat hodnotě, kterou používáte pro vaše místní zařízení VPN. Tomto příkladu používá "abc123", ale byste měli používat něco složitějšího. Důležité je, že tato hodnota musí být stejnou hodnotu, kterou zadáte při konfiguraci zařízení VPN.
    - Hodnoty pro **předplatné**, **skupiny prostředků**, a **umístění** jsou opraveny.

6. Vyberte **OK** a vytvořte připojení.

Zobrazí se připojení v **připojení** stránku brány virtuální sítě. Stav se změní z *neznámý* k *připojení*a potom do *Succeeded*.

## <a name="next-steps"></a>Další postup

- Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
