---
title: Nastavení vícenásobného tunelu VPN typu Site-to-site v centru Azure Stack
description: Přečtěte si, jak nastavit několik tunelových propojení VPN typu Site-to-site v centru Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: c7462bebc919b85db64dae9c4a07fdc1ca276e33
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525774"
---
# <a name="how-to-set-up-multiple-site-to-site-vpn-tunnels-in-azure-stack-hub"></a>Jak nastavit několik tunelových propojení VPN typu Site-to-site v centru Azure Stack

V tomto článku se dozvíte, jak použít šablonu Správce prostředků centra Azure Stack k nasazení řešení. Řešení vytvoří několik skupin prostředků s přidruženými virtuálními sítěmi a Postup připojení těchto systémů.

Šablony najdete v úložišti GitHub ve [vzorcích Azure Intelligent Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) . Šablona se nachází ve složce **RRAS-GRE-VNet-VNet** . 

## <a name="scenarios"></a>Scénáře

![V diagramech je pět scénářů sítě VPN: mezi dvěma skupinami prostředků v rámci jednoho předplatného; mezi dvěma skupinami každý ve svém vlastním předplatném; mezi dvěma skupinami v samostatných instancích zásobníku; mezi skupinou a místními místními prostředky; a několik tunelových propojení VPN.](./media/azure-stack-network-howto-vpn-tunnel/scenarios.svg)

## <a name="create-multiple-vpn-tunnels"></a>Vytvoření více tunelů sítě VPN

![Diagram zobrazuje dvě skupiny prostředků, každý ve svém vlastním předplatném a instanci zásobníku připojeným přes síť VPN. a jednu z těchto dvou skupin, které se připojují k místním prostředkům pomocí sítě VPN.](./media/azure-stack-network-howto-vpn-tunnel/azure-stack-network-vpn-tunnel1.svg)

-  Nasazení tří aplikačních vrstev, webu, aplikace a DB.

-  Nasaďte první dvě šablony na samostatné instance centra Azure Stack.

-  **Webvrstva** bude nasazená na PPE1 a **AppTier** se nasadí na PPE2.

-  Připojte **webvrstvu** a **AppTier** pomocí tunelového propojení IKE.

-  Připojte **AppTier** k místnímu systému, který budete volat **DBTier**.

## <a name="steps-to-deploy-multiple-vpns"></a>Postup nasazení více sítí VPN

Toto je proces více kroků. V případě tohoto řešení budete používat portál Azure Stack hub. Můžete ale použít PowerShell, rozhraní příkazového řádku Azure nebo jiné řetězy nástrojů infrastruktury jako kódu k zachycení výstupů a použít je jako vstupy.

![Diagram znázorňuje pět kroků k nasazení tunelů VPN mezi dvěma infrastrukturami. První dva kroky vytvoří ze šablony dvě infrastruktury. Následující dva kroky vytvoří dva tunely VPN ze šablony a poslední krok připojí tunely.](./media/azure-stack-network-howto-vpn-tunnel/azure-stack-network-vpn-tunnel2.svg)

## <a name="walkthrough"></a>Názorný postup

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>Nasazení webové vrstvy do instancí centra Azure Stack PPE1

1. Otevřete portál Azure Stack centrum uživatelů a vyberte **vytvořit prostředek**.

2. Vyberte **nasazení šablony**.

    ![V dialogovém okně "nové" > řídicím panelu se zobrazují různé možnosti. Je zvýrazněno tlačítko Template deployment.](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3. Zkopírujte a vložte obsah azuredeploy.jsz úložiště **Azure-Intelligent-Edge-Patterns/RRAS-VNet-vpntunnel** do okna šablony. Zobrazí se prostředky obsažené v šabloně, vyberte **Uložit**.

    ![Dialogová okna > nové > nasazení šablony řešení > upravit šablonu obsahuje okno, ve kterém je vložen azuredeploy.jspro soubor.](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4. Zadejte název **skupiny prostředků** a ověřte parametry.

    > [!Note]  
    > Adresní prostor webúrovně bude **10.10.0.0/16** a umístění skupiny prostředků je **PPE1** .

    ![Dialogová okna > nové > nasadit šablonu řešení > parametry "řídicí panel" obsahuje textové pole "skupina prostředků" a přepínač. Vyberte tlačítko vytvořit nové a text je "webvrstva".](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>Nasazení vrstvy aplikace do druhé instance centra Azure Stack

Stejný postup můžete použít jako **Webvrstva** , ale jiné parametry, jak je znázorněno zde:

> [!NOTE]  
> Adresní prostor AppTier bude **10.20.0.0/16** a umístění skupiny prostředků je **WestUS2**.

![Dialogové okno "> parametrů šablony řešení > řídicího panelu obsahuje" skupinu prostředků "a přepínač. Je vybráno tlačítko vytvořit nové a text je "AppTier". Osm dalších zvýrazněných textových polí obsahuje parametry šablony.](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Projděte si nasazení pro webovou vrstvu a vrstvu aplikace a zaznamenejte výstupy.

1. Zkontrolujte, jestli se nasazení úspěšně dokončilo. Vyberte **Výstupy**.

    ![Možnost výstupy je zvýrazněna v dialogovém okně "řídicí panel > Microsoft. template-přehled". Zpráva je "vaše nasazení je hotové", následované seznamem prostředků pro skupinu webvrstva, každý s názvem, typem, stavem a odkazem na podrobnosti.](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

1. První čtyři hodnoty zkopírujte do aplikace poznámkového bloku.

    ![Dialogové okno je "řídicí panel > Microsoft. template-Outputs". Čtyři textová pole, která se mají kopírovat z nasazení webové vrstvy, jsou zvýrazněná: LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY a LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

1. Opakujte pro nasazení **AppTier** .

    ![Možnost výstupy je zvýrazněna v dialogovém okně "řídicí panel > Microsoft. template-přehled". Zpráva je "vaše nasazení je hotové", následované seznamem prostředků pro skupinu AppTier, každý s názvem, typem, stavem a odkazem na podrobnosti.](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

    ![Dialogové okno je "řídicí panel > Microsoft. template-Outputs". Čtyři textová pole, která se mají kopírovat z nasazení vrstvy aplikace, jsou zvýrazněná: LOCALTUNNELENDPOINT, LOCALVNETADDRESSSPACE, LOCALVNETGATEWAY a LOCALTUNNELGATEWAY.](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Vytvoření tunelu z webové vrstvy do aplikační vrstvy

1. Otevřete portál Azure Stack centrum uživatelů a vyberte **vytvořit prostředek**.

2. Vyberte **nasazení šablony**.

3. Vložte obsah z **azuredeploy.tunnel.ike.jsv**.

4. Vyberte **Upravit parametry**.

![Dialogová okna > nové > nasadit šablonu řešení > parametry "řídicí panel" obsahuje textové pole "skupina prostředků" a přepínač. Je vybráno tlačítko použít existující a text je "webvrstva". Čtyři další zvýrazněná textová pole obsahují parametry šablony.](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Vytvoření tunelu z aplikační vrstvy do webové vrstvy

1. Otevřete portál Azure Stack centrum uživatelů a vyberte **vytvořit prostředek**.

2. Vyberte **template Deployment**.

3. Vložte obsah z **azuredeploy.tunnel.ike.jsv**.

4. Vyberte **Upravit parametry**.

![Dialogová okna > nové > nasadit šablonu řešení > parametry "řídicí panel" obsahuje textové pole "skupina prostředků" a přepínač. Je vybráno tlačítko použít existující a text je "AppTier". Čtyři další zvýrazněná textová pole obsahují parametry šablony.](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Zobrazení nasazení tunelu

Pokud zobrazíte výstup z rozšíření vlastních skriptů, uvidíte, že se vytváří tunelové propojení a že by měl zobrazit stav. Uvidíte, že se **připojujete** k čekání na to, až druhá strana bude připravená a druhá strana se po nasazení zobrazí jako **připojená** .

![Dialogové okno je "řídicí panel > skupiny prostředků > webvrstva > webvrstva – RRAS-rozšíření". Jsou uvedena dvě rozšíření: CustomScriptExtension, což je zvýrazněné a InstallRRAS. Jak se úspěšně zobrazuje stav zřizování.](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![Dialogové okno je "řídicí panely > skupiny prostředků > webvrstva > webvrstva – RRAS-rozšíření > CustomScriptExtension". Podrobná hodnota stavu je "zřizování bylo úspěšné".](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![Výstup z rozšíření vlastních skriptů pro AppTier a webvrstva se zobrazuje v oknech vedle sebe. AppTier zobrazuje stav tunelu pro připojení. Webvrstva zobrazuje připojené.](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Řešení potíží na virtuálním počítači RRAS

1. Změňte pravidlo vzdálené plochy (RDP) z **Odepřít** na **Povolit**.

1. Připojte se k systému pomocí klienta RDP pomocí přihlašovacích údajů, které jste nastavili během nasazování.

1. Otevřete PowerShell s výzvou se zvýšenými oprávněními a spusťte příkaz `get-VpnS2SInterface` .

    ![V okně PowerShellu se zobrazí spuštění příkazu Get-VpnS2SInterface, který zobrazí podrobnosti o rozhraní site-to-site. Vlastnost ConnectionState je připojen.](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

1. Ke správě systému použijte rutiny **RemoteAccess** .

    ![Na úrovni aplikace je spuštěný cmd.exe okno, ve kterém se zobrazuje výstup příkazu ipconfig. Zobrazí se dvě okna jako překryvná: okno připojení RDP se připojuje k webové vrstvě a okno zabezpečení systému Windows, které se připojuje k webové vrstvě.](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-an-on-premises-vm-db-tier"></a>Instalace služby RRAS na místní úrovni databáze virtuálních počítačů

1. Cílem je bitová kopie systému Windows 2016.

1. Pokud zkopírujete `Add-Site2SiteIKE.ps1` skript z úložiště a spustíte ho místně, skript nainstaluje **WindowsFeature** a **RemoteAccess**.

    > [!NOTE]
    > V závislosti na vašem prostředí možná budete muset restartovat systém.

    Referenční informace najdete v tématu Konfigurace sítě v místním počítači.

    ![V cmd.exem okně se zobrazuje výstup příkazu ipconfig a okno centra síťových a sdílení.](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

1. Spusťte skript, který přidá **výstupní** parametry zaznamenané z nasazení šablony AppTier.

    ![Skript Add-Site2SiteIKE. psa se spustí v okně PowerShellu a zobrazí se výstup.](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

1. Tunelové propojení je teď nakonfigurované a čeká se na připojení AppTier.

### <a name="configure-app-tier-to-db-tier"></a>Konfigurace aplikační vrstvy na úroveň DB

1. Otevřete portál Azure Stack centrum uživatelů a vyberte **vytvořit prostředek**.

2. Vyberte **template Deployment**.

3. Vložte obsah z **azuredeploy.tunnel.ike.jsv**.

4. Vyberte **Upravit parametry**.

    ![Dialogová okna > nové > nasadit šablonu řešení > parametry "řídicí panel" obsahuje textové pole "skupina prostředků" a přepínač. Je vybráno tlačítko použít existující a text je "AppTier". Tři další zvýrazněná textová pole obsahují parametry šablony.](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5. Ověřte, že jste vybrali AppTier a ve vzdálené interní síti na 10.99.0.1.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Potvrzení tunelu mezi aplikační vrstvou a vrstvou DB

1. Pokud chcete zjistit tunel bez přihlášení k virtuálnímu počítači, spusťte rozšíření vlastních skriptů.

2. Přejít na virtuální počítač RRAS (AppTier).

3. Vyberte **rozšíření** a **rozšíření pro vlastní skript** v jazyce R.

4. Přejděte do adresáře Scripts v úložišti **Azure-Intelligent-Edge-Patterns/RRAS-VNet-vpntunnel** . Vyberte **Get-VPNS2SInterfaceStatus.ps1**.

    ![Dialogové okno je "řídicí panely > skupiny prostředků > AppTier > AppTier-RRAS-Extensions > CustomScriptExtension". PODROBNÝ stav má hodnotu "zřizování bylo úspěšné". Podrobnosti jsou v překryvném okně poznámkového bloku.](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5. Pokud povolíte protokol RDP a přihlásíte se, otevřete PowerShell pomocí a spusťte `get-vpns2sinterface` příkaz, abyste viděli, že je tunel připojený.

    **DBTier**

    ![V DBTier se v okně PowerShellu zobrazuje spuštění příkazu Get-VpnS2SInterface, který obsahuje podrobnosti o rozhraní site-to-site. Vlastnost ConnectionState je připojen.](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![V AppTier se v okně PowerShellu zobrazuje spuštění příkazu Get-VpnS2SInterface, který obsahuje podrobnosti o rozhraní site-to-site. Vlastnost ConnectionState je připojen k dvěma cílům.](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!NOTE]  
    > Můžete testovat protokol RDP jak z jednoho počítače na druhý, tak z druhého do prvního.

    > [!NOTE]  
    > K implementaci tohoto řešení v místním prostředí budete muset nasazovat trasy do vzdálené sítě centra Azure Stack v rámci přepínání infrastruktury nebo minimálně na konkrétní virtuální počítače.

### <a name="deploying-a-gre-tunnel"></a>Nasazení tunelu GRE

V této šabloně tento návod použil [šablonu protokolu IKE](network-howto-vpn-tunnel-ipsec.md). Můžete ale také nasadit [tunel GRE](network-howto-vpn-tunnel-gre.md). Toto tunelové propojení nabízí větší propustnost.

Proces je skoro identický. Pokud však šablonu tunelového propojení nasadíte do stávající infrastruktury, bude nutné použít výstupy z druhého systému pro první tři vstupy. Budete muset znát **LOCALTUNNELGATEWAY** pro skupinu prostředků, kterou nasazujete, a ne skupinu prostředků, ke které se pokoušíte připojit.

![Dialogová okna > nové > nasadit šablonu řešení > parametry "řídicí panel" obsahuje textové pole "skupina prostředků" a přepínač. Je vybráno tlačítko použít existující a text je "AppTier". Čtyři další zvýrazněná textová pole obsahují data z výstupů webúrovně.](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)  
[Postup vytvoření tunelu VPN pomocí protokolu GRE](network-howto-vpn-tunnel-gre.md)  
[Postup vytvoření tunelu VPN pomocí protokolu IPSEC](network-howto-vpn-tunnel-ipsec.md)