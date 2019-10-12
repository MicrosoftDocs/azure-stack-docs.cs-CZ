---
title: Konfigurace připojení k hybridnímu cloudu pomocí Azure a Azure Stack | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat připojení k hybridnímu cloudu pomocí Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: ae1aaa87ab2817ec696e488372bbc8f2cb6d119b
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283714"
---
# <a name="configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>Konfigurace připojení k hybridnímu cloudu pomocí Azure a Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

K prostředkům se zabezpečením můžete přistupovat v globálním Azure a Azure Stack pomocí modelu hybridního připojení.

V tomto řešení sestavíte ukázkové prostředí pro:

> [!div class="checklist"]
> - Udržujte místní data, aby splňovala požadavky na ochranu osobních údajů nebo regulativní předpisy, ale přitom udržujte přístup k globálním prostředkům Azure.
> - Udržování starší verze systému při použití nasazení a prostředků aplikací v cloudu, které jsou v globálním Azure.

> [!Tip]  
> @no__t -0hybrid-Pillars. png @ no__t-1  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a umožňuje jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.


## <a name="prerequisites"></a>Požadavky

K vytvoření hybridního nasazení připojení je potřeba pár součástí. U některých z těchto komponent se připravuje čas, proto proveďte odpovídající plán.

**Azure Stack**

Partner Azure pro výrobce OEM/hardware může nasadit produkční Azure Stack a všichni uživatelé mohou nasadit Azure Stack Development Kit (ASDK).

**Azure Stack komponenty**

Operátor Azure Stack musí nasadit App Service, vytvořit plány a nabídky, vytvořit předplatné tenanta a přidat image Windows serveru 2016. Pokud už máte tyto komponenty, ujistěte se, že splňují požadavky, než toto řešení spustíte.

Toto řešení předpokládá, že máte základní znalosti Azure a Azure Stack. Pokud se chcete dozvědět víc, než začnete s řešením, přečtěte si následující články:

 - [Seznámení s Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack klíčové koncepty](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
 - Vytvořte [webovou aplikaci](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts) v Azure. Poznamenejte si adresu URL webové aplikace, protože ji budete potřebovat v řešení.

### <a name="azure-stack"></a>Azure Stack

 - Použijte produkční Azure Stack nebo nasaďte Azure Stack Development Kit z https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1.
   >[!Note]
   >Nasazení ASDK může trvat až 7 hodin, takže by to mělo mít odpovídající plán.

 - Nasaďte [App Service](../operator/azure-stack-app-service-deploy.md) služby PaaS a Azure Stack.
 - [Vytvářejte plány a nabídky](../operator/service-plan-offer-subscription-overview.md) v prostředí Azure Stack.
 - [Vytvořte předplatné tenanta](../operator/azure-stack-subscribe-plan-provision-vm.md) v prostředí Azure Stack.

### <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace připojení hybridního cloudu ověřte, že splňujete následující kritéria:

 - Pro vaše zařízení VPN budete potřebovat externě veřejnou IPv4 adresu. Tato IP adresa se nedá najít za překladem adres (NAT) (překladu síťových adres).
 - Všechny prostředky jsou nasazeny ve stejné oblasti nebo umístění.

####  <a name="solution-example-values"></a>příklady hodnot řešení

Příklady v tomto řešení používají následující hodnoty. Tyto hodnoty můžete použít k vytvoření testovacího prostředí nebo k lepšímu porozumění příkladům, které se na ně vztahují. Další informace o nastaveních služby VPN Gateway najdete v tématu [informace o nastaveních VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings).

Specifikace připojení:

 - **Typ sítě VPN**: směrování založené na trasách
 - **Typ připojení**: Site-to-Site (IPSec)
 - **Typ brány**: síť VPN
 - **Název připojení Azure**: Azure-Gateway-AzureStack-S2SGateway (na portálu se tato hodnota automaticky vyplní)
 - **Azure Stack název připojení**: AzureStack-Gateway-Azure-S2SGateway (na portálu se tato hodnota automaticky vyplní)
 - **Shared Key**: jakýkoli kompatibilní s hardwarem sítě VPN se shodnými hodnotami na obou stranách připojení
 - **Předplatné**: libovolné preferované předplatné
 - **Skupina prostředků**: test – infrastruktura

IP adresa sítě a podsítě:

| Připojení Azure/Azure Stack | Name | Podsíť | IP adresa |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Virtuální síť Azure | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Brána Azure Virtual Network | Azure – brána |  |  |
| Brána Azure Stack Virtual Network | AzureStack – brána |  |  |
| Veřejná IP adresa Azure | Azure – GatewayPublicIP |  | Určeno při vytvoření |
| Azure Stack veřejné IP adresy | AzureStack – GatewayPublicIP |  | Určeno při vytvoření |
| Brána místní sítě Azure | AzureStack – S2SGateway<br>   10.100.100.0/23 |  | Azure Stack hodnota veřejné IP adresy |
| Azure Stack bránu místní sítě | Azure – S2SGateway<br>10.100.102.0/23 |  | Hodnota veřejné IP adresy Azure |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>Vytvoření virtuální sítě v globálním Azure a Azure Stack

Pomocí následujících kroků můžete vytvořit virtuální síť pomocí portálu. Tyto [příklady hodnot](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values) můžete použít, pokud používáte tento článek jenom jako řešení. Pokud používáte tento článek ke konfiguraci produkčního prostředí, nahraďte vzorová nastavení vlastními hodnotami.

> [!IMPORTANT]
> Je nutné zajistit, aby se v Azure překrývaly IP adresy ani Azure Stack adresní prostory virtuální sítě.

Vytvoření virtuální sítě v Azure:

1. Pomocí prohlížeče se připojte k [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí svého účtu Azure.
2. Vyberte **vytvořit prostředek**. Do pole **Hledat na Marketplace** zadejte ' virtuální síť '. Z výsledků vyberte **virtuální síť** .
3. V seznamu **Vybrat model nasazení** vyberte možnost **Správce prostředků**a pak vyberte **vytvořit**.
4. V části **vytvořit virtuální síť**nakonfigurujte nastavení virtuální sítě. Názvy požadovaných polí mají předponu s červenou hvězdičkou.  Když zadáte platnou hodnotu, hvězdička se změní na zelenou značku zaškrtnutí.

Vytvoření virtuální sítě v Azure Stack:

* Předchozí kroky (1-4) opakujte pomocí Azure Stack **portálu tenanta**.

## <a name="add-a-gateway-subnet"></a>Přidat podsíť brány

Před připojením virtuální sítě k bráně je třeba vytvořit podsíť brány pro virtuální síť, ke které se chcete připojit. Služby brány používají IP adresy, které zadáte v podsíti brány.

V [Azure Portal](https://portal.azure.com/)přejděte do Správce prostředků virtuální sítě, ve které chcete vytvořit bránu virtuální sítě.

1. Vyberte virtuální síť a otevřete stránku **virtuální síť** .
2. V **Nastavení**vyberte **podsítě**.
3. Na stránce **podsítě** vyberte **+ podsíť brány** a otevřete stránku **Přidat podsíť** .

    ![Přidat podsíť brány](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. **Název** podsítě se automaticky vyplní hodnotou ' GatewaySubnet '. Tato hodnota se vyžaduje v případě, že Azure rozpozná podsíť jako podsíť brány.
5. Změňte hodnoty **rozsahu adres** , které jsou k dispozici, aby odpovídaly vašim požadavkům na konfiguraci, a pak vyberte **OK**.

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>Vytvoření Virtual Network brány v Azure a Azure Stack

Pomocí následujícího postupu můžete vytvořit bránu virtuální sítě v Azure.

1. Na levé straně stránky portálu vyberte **+** a do vyhledávacího pole zadejte "Brána virtuální sítě".
2. Ve **výsledcích**vyberte možnost **Brána virtuální sítě**.
3. V **bráně virtuální sítě**vyberte **vytvořit** a otevřete stránku **vytvořit bránu virtuální sítě** .
4. V části **vytvořit bránu virtuální sítě**zadejte hodnoty pro bránu sítě, jak je znázorněno v **příkladu příkladů hodnot**a následující další hodnoty:

   - **SKU**: základní
   - **Virtual Network**: vyberte Virtual Network, který jste vytvořili dříve. Automaticky vybraná podsíť brány, kterou jste vytvořili.
   - **První konfigurace IP adresy**: veřejná IP adresa vaší brány.
     - Vyberte **vytvořit konfiguraci protokolu IP brány**, která vás přesměruje na stránku **zvolit veřejnou IP adresu** .
     - Výběrem **+ vytvořit nové** otevřete stránku **vytvořit veřejnou IP adresu** .
     - Zadejte **název** vaší veřejné IP adresy. Ponechte položku SKU jako **základní**a pak kliknutím na **tlačítko OK** uložte změny.

       > [!Note]
       > V současné době VPN Gateway podporuje pouze dynamické přidělování veřejných IP adres. To ale neznamená, že se IP adresa po přiřazení k vaší bráně VPN mění. Pouze když se změní veřejná IP adresa, dojde k odstranění a opětovnému vytvoření brány. Změna velikosti, resetování nebo jiné interní údržby/upgradu na bránu VPN nemění IP adresu.

4. Ověřte nastavení brány.
5. Vyberte **vytvořit** a vytvořte bránu VPN. Nastavení brány se ověří a na řídicím panelu se zobrazí dlaždice nasazování brány virtuální sítě.

   >[!Note]
   >Vytvoření brány může trvat až 45 minut. Je možné, že budete muset aktualizovat stránku portálu, aby se zobrazil stav dokončeno.

    Po vytvoření brány se zobrazí IP adresa, která je k ní přiřazená, a to tak, že na portálu vyhledáte virtuální síť. Brána se zobrazí jako připojené zařízení. Pokud chcete zobrazit další informace o bráně, vyberte zařízení.

6. Opakujte předchozí kroky (1-5) v nasazení Azure Stack.

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>Vytvoření brány místní sítě v Azure a Azure Stack

Brána místní sítě obvykle odkazuje na vaše místní umístění. Lokalitě dáte název, na který může Azure nebo Azure Stack odkazovat, a pak zadejte:

- IP adresa místního zařízení VPN, pro které vytváříte připojení.
- Předpony IP adres, které budou směrovány přes bránu VPN na zařízení VPN. Předpony adres, které zadáte, jsou předpony umístěné ve vaší místní síti.

  >[!Note]
  >Pokud se vaše místní síť změní nebo potřebujete změnit veřejnou IP adresu pro zařízení VPN, můžete tyto hodnoty snadno aktualizovat později.

1. Na portálu vyberte **+ vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **bránu místní sítě**a pak pro hledání vyberte **ENTER** . Zobrazí se seznam výsledků.
3. Vyberte možnost **Brána místní sítě**a pak výběrem **vytvořit** otevřete stránku **vytvořit bránu místní sítě** .
4. V části **vytvořit bránu místní sítě**zadejte hodnoty pro bránu místní sítě pomocí našeho **ukázkového kurzu**. Zahrňte následující další hodnoty.

    - **IP adresa**: veřejná IP adresa zařízení VPN, ke kterému se má Azure nebo Azure Stack připojit. Zadejte platnou veřejnou IP adresu, která není za překladem adres (NAT), aby Azure mohla dosáhnout této adresy. Pokud nemáte IP adresu hned teď, můžete použít hodnotu z příkladu jako zástupný symbol, ale budete se muset vrátit zpět a nahradit zástupný text veřejnou IP adresou vašeho zařízení VPN. Azure se nemůže připojit k zařízení, dokud nezadáte platnou adresu.
    - **Adresní prostor**: rozsah adres pro síť, kterou tato místní síť představuje. Můžete přidat více rozsahů adresního prostoru. Ujistěte se, že se zadané rozsahy nepřekrývají s rozsahy jiných sítí, ke kterým se chcete připojit. Azure přesměruje rozsah adres, který zadáte, na IP adresu místního zařízení VPN. Použijte vlastní hodnoty, pokud se chcete připojit k místní lokalitě, a ne ukázkovou hodnotu.
    - **Konfigurovat nastavení protokolu BGP**: použijte pouze při konfiguraci protokolu BGP. V opačném případě tuto možnost nevybírejte.
    - **Předplatné**: Ověřte, že se zobrazuje správné předplatné.
    - **Skupina prostředků**: vyberte skupinu prostředků, kterou chcete použít. Můžete buď vytvořit novou skupinu prostředků, nebo vybrat tu, kterou jste už vytvořili.
    - **Umístění**: vyberte umístění, ve kterém se tento objekt vytvoří. Možná budete chtít vybrat stejné umístění, ve kterém se nachází vaše virtuální síť, ale nemusíte to dělat.
5. Po dokončení zadávání požadovaných hodnot vyberte **vytvořit** a vytvořte bránu místní sítě.
6. Opakujte tyto kroky (1-5) ve vašem nasazení Azure Stack.

## <a name="configure-your-connection"></a>Konfigurace připojení

Připojení typu Site-to-site k místní síti vyžadují zařízení VPN. Zařízení VPN, které nakonfigurujete, se označuje jako připojení. Ke konfiguraci připojení budete potřebovat:

- Sdílený klíč. Tento klíč je stejný sdílený klíč, který zadáte při vytváření připojení VPN typu Site-to-site. V našich příkladech používáme základní sdílený klíč. Doporučujeme, abyste vygenerovali složitější klíč, který se má použít.
- Veřejná IP adresa brány virtuální sítě. Veřejnou IP adresu můžete zobrazit pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete zjistit veřejnou IP adresu vaší brány VPN pomocí Azure Portal, přejděte na brány virtuální sítě a potom vyberte název brány.

Pomocí následujících kroků vytvořte připojení VPN typu Site-to-site mezi bránou virtuální sítě a místním zařízením VPN.

1. V Azure Portal vyberte **+ vytvořit prostředek**.
2. Vyhledejte **připojení**.
3. Ve **výsledcích**vyberte **připojení**.
4. V případě **připojení**vyberte **vytvořit**.
5. Při **vytváření připojení**nakonfigurujte následující nastavení:

    - **Typ připojení**: vyberte site-to-Site (IPSec).
    - **Skupina prostředků**: Vyberte svou testovací skupinu prostředků.
    - **Virtual Network brána**: Vyberte bránu virtuální sítě, kterou jste vytvořili.
    - **Brána místní sítě**: Vyberte bránu místní sítě, kterou jste vytvořili.
    - **Název připojení**: Tento název se automaticky vyplní pomocí hodnot ze dvou bran.
    - **Sdílený klíč**: Tato hodnota musí odpovídat hodnotě, kterou používáte pro místní zařízení VPN. Ukázka kurzu používá "abc123", ale měli byste použít něco složitějšího. Důležité je, že tato hodnota musí být stejná jako hodnota, kterou zadáte při konfiguraci zařízení VPN.
    - Jsou opraveny hodnoty pro **předplatné**, **skupinu prostředků**a **umístění** .

6. Vyberte **OK** a vytvořte připojení.

Připojení můžete zobrazit na stránce **připojení** brány virtuální sítě. Stav bude přecházet z *neznámého* pro *připojení*a pak na *úspěšné*.

## <a name="next-steps"></a>Další kroky

- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](https://docs.microsoft.com/azure/architecture/patterns).
