---
title: Postup vytvoření tunelu VPN pomocí protokolu IPSEC v Azure Stack | Microsoft Docs
description: Naučte se vytvořit tunel VPN pomocí protokolu IPSEC v Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 762d3f631823d1acb8445148a164a18605fa3762
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167561"
---
# <a name="how-to-create-a-vpn-tunnel-using-ipsec--in-azure-stack"></a>Postup vytvoření tunelu VPN pomocí protokolu IPSEC v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Šablonu Azure Stack Správce prostředků v tomto řešení můžete použít k propojení dvou Azure Stack virtuální sítě v rámci stejného Azure Stackho prostředí. [Azure Stack virtuální sítě nelze připojit](https://docs.microsoft.com/azure-stack/user/azure-stack-network-differences) pomocí předdefinované Virtual Network brány. Prozatím musíte použít síťová virtuální zařízení (síťové virtuální zařízení) s k vytvoření tunelu VPN mezi dvěma Azure Stack virtuální sítě. Šablona řešení nasadí dva virtuální počítače se systémem Windows Server 2016 s nainstalovanou službou RRAS. Řešení nakonfiguruje dva servery RRAS tak, aby používaly tunel S2SVPN IKEv2 mezi dvěma virtuální sítě. Vytvoří se odpovídající pravidla NSG a UDR, která umožní směrování mezi podsítěmi v každé virtuální síti určené jako **interní** . 

Toto řešení je základem, který umožní vytvořit tunely sítě VPN nejen v rámci Azure Stack instance, ale také mezi Azure Stack instancemi a dalšími prostředky, jako jsou například místní sítě s použitím tunelových propojení VPN S2S sítě Windows RRAS.

Šablony najdete v úložišti GitHub ve [vzorcích Azure Intelligent Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) . Šablona se nachází ve složce **RRAS-GRE-VNet-VNet** . 

![alternativní text](./media/azure-stack-network-howto-vpn-tunnel-ipsec/overview.png)

## <a name="requirements"></a>Požadavky

- ASDK nebo Azure Stack integrovaný systém s nejnovějšími aktualizacemi. 
- Požadované položky Azure Stack Marketplace:
    -  Windows Server 2016 Datacenter nebo Windows Server 2019 Datacenter (doporučuje se nejnovější sestavení)
    -  Rozšíření vlastních skriptů

## <a name="things-to-consider"></a>Co je potřeba zvážit

- Skupina zabezpečení sítě se použije pro podsíť tunelu šablony.  Doporučuje se zabezpečit interní podsíť v každé virtuální síti s dalšími NSG.
- Pravidlo pro zamítnutí protokolu RDP se aplikuje na NSG tunelu a bude nutné ho nastavit na povolit, pokud máte v úmyslu přistupovat k virtuálním počítačům přes veřejnou IP adresu.
- Toto řešení nebere v úvahu překlad názvů DNS.
- Kombinace názvu virtuální sítě a vmName musí být kratší než 15 znaků.
- Tato šablona je navržená tak, aby názvy virtuálních sítí přizpůsobené pro VNet1 a VNet2.
- Tato šablona používá BYOL Windows.
- Při odstraňování skupiny prostředků (aktuálně zapnuto (1907) musíte ručně odpojit skupin zabezpečení sítě z podsítě tunelového propojení, aby se zajistilo dokončení odstranění skupiny prostředků.
- Tato šablona používá virtuální počítač DS3v2.  Služba RRAS nainstaluje a spustí interní SQL Server systému Windows.  To může způsobit problémy s pamětí, pokud je velikost virtuálního počítače příliš malá.  Než zmenšíte velikost virtuálního počítače, ověřte výkon.
- Toto řešení není vysoce dostupné.  Pokud potřebujete řešení s více HA, můžete přidat druhý virtuální počítač. je třeba ručně změnit trasu v tabulce směrování na interní IP adresu sekundárního rozhraní.  Je také potřeba nakonfigurovat více tunelů pro vzájemné propojení.

## <a name="optional"></a>Volitelné

- Pomocí parametrů _artifactsLocation a _artifactsLocationSasToken můžete použít vlastní účet úložiště objektů BLOB a token SAS.
- Existují dva výstupy v této šabloně INTERNALSUBNETREFVNET1 a INTERNALSUBNETREFVNET2, což jsou ID prostředků pro interní podsítě, pokud je chcete použít ve vzoru nasazení stylu kanálu.

Tato šablona poskytuje výchozí hodnoty pro pojmenování virtuální sítě a IP adresování.  Vyžaduje heslo pro správce (rrasadmin) a také nabízí možnost používat vlastní objekt BLOB úložiště s tokenem SAS.  Buďte opatrní, abyste tyto hodnoty zachovali v rámci platných rozsahů, protože nasazení může selhat.  Balíček PowerShell DSC se spustí na každém virtuálním počítači RRAS a nainstaluje směrování a všechny požadované závislé služby a funkce.  V případě potřeby můžete tento DSC přizpůsobit dál.  Rozšíření vlastních skriptů spustí následující skript a Add-Site2SiteIKE. ps1 nakonfiguruje tunel VPNS2S mezi dvěma servery RRAS pomocí sdíleného klíče.  Pokud chcete zobrazit výsledky konfigurace tunelu VPN, můžete zobrazit podrobný výstup z rozšíření vlastních skriptů.

![alternativní text](./media/azure-stack-network-howto-vpn-tunnel-ipsec/s2svpntunnel.png)

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro Azure Stack sítě](azure-stack-network-differences.md)  
[Jak nastavit několik tunelových propojení VPN typu Site-to-site](network-howto-vpn-tunnel.md)  
[Postup vytvoření tunelu VPN pomocí protokolu GRE](network-howto-vpn-tunnel-gre.md)