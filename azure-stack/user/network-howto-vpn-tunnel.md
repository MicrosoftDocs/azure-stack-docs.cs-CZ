---
title: Jak nastavit několik tunelových propojení VPN typu Site-to-site v Azure Stack | Microsoft Docs
description: Přečtěte si, jak nastavit několik tunelových propojení VPN typu Site-to-site v Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: d85de1892e2e6620249ff3a95ee2debb01b81981
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167660"
---
# <a name="how-to-set-up-a-multiple-site-to-site-vpn-tunnel-in-azure-stack"></a>Jak nastavit několik tunelových propojení VPN typu Site-to-site v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

V tomto článku se dozvíte, jak použít šablonu Azure Stack Správce prostředků k nasazení řešení. Řešení vytvoří několik skupin prostředků s přidruženými virtuálními sítěmi a Postup připojení těchto systémů.

Šablony najdete v úložišti GitHub ve [vzorcích Azure Intelligent Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) . Šablona se nachází ve složce **RRAS-GRE-VNet-VNet** . 

## <a name="scenarios"></a>Scénáře

![](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>Vytvoření více tunelů sítě VPN

![](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  Nasazení tří aplikačních vrstev, webu, aplikace a DB.

-  Nasaďte první dvě šablony na samostatné instance Azure Stack.

-  **Webvrstva** bude nasazená na PPE1 a **AppTier** se nasadí na PPE2.

-  Připojte **webvrstvu** a **AppTier** pomocí tunelového propojení IKE.

-  Připojte **AppTier** k místnímu systému, který budete volat **DBTier**.

## <a name="steps-to-deploy-multiple-vpns"></a>Postup nasazení více sítí VPN

Toto je proces více kroků. V případě tohoto řešení budete používat portál Azure Stack. Můžete ale použít PowerShell, rozhraní příkazového řádku Azure nebo jiné řetězy nástrojů infrastruktury jako kódu k zachycení výstupů a použít je jako vstupy.

![alternativní text](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>Názorný postup

### <a name="deploy-web-tier-to-azure-stack-instances-ppe1"></a>Nasazení webové vrstvy do instancí Azure Stack PPE1

1.  Otevřete Azure Stack User Portal a vyberte **vytvořit prostředek**.

2.  Vyberte **nasazení šablony**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3.  Zkopírujte a vložte obsah azuredeploy. JSON z úložiště **Azure-Intelligent-Edge-Patterns/RRAS-VNet-vpntunnel** do okna šablony. Zobrazí se prostředky obsažené v šabloně, vyberte **Uložit**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4.  Zadejte název **skupiny prostředků** a ověřte parametry.

    > ! Značte  
    > Adresní prostor webúrovně bude **10.10.0.0/16** a umístění skupiny prostředků je **PPE1** .

    ![](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-instances"></a>Nasazení vrstvy aplikace do druhé instance Azure Stack

Stejný postup můžete použít jako **Webvrstva** , ale jiné parametry, jak je znázorněno zde:

> [!Note]  
> Adresní prostor AppTier bude **10.20.0.0/16** a umístění skupiny prostředků je **WestUS2**.

![](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>Projděte si nasazení pro webovou vrstvu a vrstvu aplikace a zaznamenejte výstupy.

1.  Zkontrolujte, jestli se nasazení úspěšně dokončilo. Vyberte **výstupy**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

3.  První čtyři hodnoty zkopírujte do aplikace poznámkového bloku.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

5.  Opakujte pro nasazení **AppTier** .

![](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>Vytvoření tunelu z webové vrstvy do aplikační vrstvy

1.  Otevřete Azure Stack User Portal a vyberte **vytvořit prostředek**.

2.  Vyberte **nasazení šablony**.

3.  Vložte obsah z **azuredeploy. Tunnel. IKE. JSON**.

4.  Vyberte **Upravit parametry**.

![](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>Vytvoření tunelu z aplikační vrstvy do webové vrstvy

1.  Otevřete Azure Stack User Portal a vyberte **vytvořit prostředek**.

2.  Vyberte **Template deployment**.

3.  Vložte obsah z **azuredeploy. Tunnel. IKE. JSON**.

4.  Vyberte **Upravit parametry**.

![](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>Zobrazení nasazení tunelu

Pokud zobrazíte výstup z rozšíření vlastních skriptů, uvidíte, že se vytváří tunelové propojení a že by měl zobrazit stav. Uvidíte, že se **připojujete** k čekání na to, až druhá strana bude připravená a druhá strana se po nasazení zobrazí jako **připojená** .

![](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>Řešení potíží na virtuálním počítači RRAS

1.  Změňte pravidlo protokolu RDP z **Odepřít** na **Povolit**.

2.  Připojte se k systému pomocí klienta vzdálené plochy (DRP) s přihlašovacími údaji, které jste nastavili během nasazování.

3.  Otevřete PowerShell s výzvou se zvýšenými oprávněními a spusťte `get-VPNS2SInterface`.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

5.  Ke správě systému použijte rutiny **RemoteAccess** .

    ![](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-a-on-premises-vm-db-tier"></a>Instalace služby RRAS na místní úrovni databáze virtuálních počítačů

1.  Cílem je bitová kopie systému Windows 2016.

2.  Pokud zkopírujete skript `Add-Site2SiteIKE.ps1` z úložiště a spustíte ho místně, skript nainstaluje **WindowsFeature** a **RemoteAccess**.

    > [!Note]
    > V závislosti na vašem prostředí možná budete muset restartovat systém.

    Referenční informace najdete v tématu Konfigurace sítě v místním počítači.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

3.  Spusťte skript, který přidá **výstupní** parametry zaznamenané z nasazení šablony AppTier.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

5.  Tunelové propojení je teď nakonfigurované a čeká se na připojení AppTier.

### <a name="configure-app-tier-to-db-tier"></a>Konfigurace aplikační vrstvy na úroveň DB

1.  Otevřete Azure Stack User Portal a vyberte **vytvořit prostředek**.

2.  Vyberte **Template deployment**.

3.  Vložte obsah z **azuredeploy. Tunnel. IKE. JSON**.

4.  Vyberte **Upravit parametry**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5.  Ověřte, že jste vybrali AppTier a ve vzdálené interní síti na 10.99.0.1.

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>Potvrzení tunelu mezi aplikační vrstvou a vrstvou DB

1.  Pokud chcete zjistit tunel bez přihlášení k virtuálnímu počítači, spusťte rozšíření vlastních skriptů.

2.  Přejít na virtuální počítač RRAS (AppTier).

3.  Vyberte **rozšíření** a**rozšíření pro vlastní skript**v jazyce R.

4.  Přejděte do adresáře Scripts v úložišti **Azure-Intelligent-Edge-Patterns/RRAS-VNet-vpntunnel** . Vyberte **Get-VPNS2SInterfaceStatus. ps1**.

    ![](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5.  Pokud povolíte RDP a přihlásíte se, otevřete PowerShell pomocí a spusťte `get-vpns2sinterface`a můžete vidět, že je připojení tunelu.

    **DBTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!Note]  
    > Můžete testovat protokol RDP jak z jednoho počítače na druhý, tak z druhého do prvního.

    > [!Note]  
    > K implementaci tohoto řešení v místním prostředí budete muset nasazovat trasy do Azure Stack vzdálené sítě, abyste přepnuli infrastrukturu nebo minimálně na konkrétní virtuální počítače.

### <a name="deploying-a-gre-tunnel"></a>Nasazení tunelu GRE

V této šabloně tento návod použil [šablonu protokolu IKE](network-howto-vpn-tunnel-ipsec.md). Můžete ale také nasadit [tunel GRE](network-howto-vpn-tunnel-gre.md). Toto tunelové propojení nabízí větší propustnost.

Proces je skoro identický. Pokud však šablonu tunelového propojení nasadíte do stávající infrastruktury, bude nutné použít výstupy z druhého systému pro první tři vstupy. Budete muset znát **LOCALTUNNELGATEWAY** pro skupinu prostředků, kterou nasazujete, a ne skupinu prostředků, ke které se pokoušíte připojit.

![](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro Azure Stack sítě](azure-stack-network-differences.md)  
[Postup vytvoření tunelu VPN pomocí protokolu GRE](network-howto-vpn-tunnel-gre.md)  
[Postup vytvoření tunelu VPN pomocí protokolu IPSEC](network-howto-vpn-tunnel-ipsec.md)