---
title: Připojení dvou Azure Stack centra virtuální sítě
description: Přečtěte si, jak propojit dvě centra Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 3920e0606bc4e101286f22796d7269b0774701d4
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525655"
---
# <a name="vnet-to-vnet-connectivity-with-rras"></a>Připojení VNet-to-VNet ke službě RRAS

Můžete propojit dvě Azure Stack centra virtuální sítě mezi sebou v rámci stejného Azure Stack centra. V současné době není možné připojit Azure Stack centra virtuální sítě pomocí integrované [Virtual Network brány](./azure-stack-network-differences.md). K vytvoření tunelu VPN mezi dvěma Azure Stack centra virtuální sítě musíte použít zařízení síťové virtuální zařízení. V odkazech na šablony v tomto článku se nasadí dva virtuální počítače s Windows serverem 2016 s nainstalovaným serverem RRAS. Dva servery RRAS jsou nakonfigurovány k implementaci tunelu S2SVPN IKEv2 mezi dvěma virtuální sítě. Vytvoří se odpovídající pravidla NSG a UDR umožňující směrování mezi podsítěmi v každé virtuální síti, která je označena jako **interní**. 

Tento vzor nasazení je základem, který umožní vytvořit tunely sítě VPN nejen v rámci instance centra Azure Stack, ale také mezi instancemi centra Azure Stack a dalšími prostředky, jako jsou místní sítě s použitím tunelových propojení S2S VPN v rámci služby Windows RRAS. 

Šablony najdete v úložišti [GitHub ve vzorcích Azure Intelligent Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns
) . Šablona se nachází ve složce **S2SVPNTunnel** .

![Diagram zobrazuje implementaci, která poskytuje tunelové propojení sítě VPN mezi dvěma virtuální sítě. V každé virtuální síti je server RRAS a také interní podsíť a podsíť tunelového propojení.](./media/azure-stack-network-howto-vnet-peering/overview.svg)

## <a name="requirements"></a>Požadavky

- Bylo použito nasazení s nejnovějšími aktualizacemi. 
- Požadované položky centra Azure Stack na webu Marketplace:
    -  Windows Server 2016 Datacenter (doporučuje se nejnovější sestavení)
    -  Rozšíření vlastních skriptů

## <a name="things-to-consider"></a>Co je potřeba zvážit

- Skupina zabezpečení sítě se použije pro podsíť tunelu šablony. Doporučuje se zabezpečit interní podsíť v každé virtuální síti s dalšími NSG.
- Pravidlo pro zamítnutí protokolu RDP se aplikuje na NSG tunelu a bude nutné ho nastavit na povolit, pokud máte v úmyslu přistupovat k virtuálním počítačům přes veřejnou IP adresu.
- Toto řešení nebere v úvahu překlad názvů DNS.
- Kombinace názvu virtuální sítě a vmName musí být kratší než 15 znaků.
- Tato šablona je navržená tak, aby názvy virtuálních sítí přizpůsobené pro VNet1 a VNet2.
- Tato šablona používá BYOL Windows.
- Při odstraňování skupiny prostředků (aktuálně zapnuto (1907) musíte ručně odpojit skupin zabezpečení sítě z podsítě tunelového propojení, aby se zajistilo dokončení odstranění skupiny prostředků.
- Tato šablona používá virtuální počítač DS3v2. Služba RRAS nainstaluje a spustí interní SQL Server systému Windows. To může způsobit problémy s pamětí, pokud je velikost virtuálního počítače příliš malá. Než zmenšíte velikost virtuálního počítače, ověřte výkon.
- Toto řešení není vysoce dostupné. Pokud potřebujete řešení s více HA, můžete přidat druhý virtuální počítač. je třeba ručně změnit trasu v tabulce směrování na interní IP adresu sekundárního rozhraní. Je také potřeba nakonfigurovat více tunelů pro vzájemné propojení.

## <a name="options"></a>Možnosti

- Pomocí parametrů _artifactsLocation a _artifactsLocationSasToken můžete použít vlastní účet úložiště objektů BLOB a token SAS.
- Existují dva výstupy v této šabloně INTERNALSUBNETREFVNET1 a INTERNALSUBNETREFVNET2, což jsou ID prostředků pro interní podsítě, pokud je chcete použít ve vzoru nasazení stylu kanálu.

Šablona poskytuje výchozí hodnoty pro pojmenovávání virtuálních sítí a IP adresování. Vyžaduje heslo pro správce (rrasadmin) a také nabízí možnost používat vlastní objekt BLOB úložiště s tokenem SAS. Buďte opatrní, abyste tyto hodnoty zachovali v rámci platných rozsahů, protože nasazení může selhat. Balíček PowerShell DSC se spustí na každém virtuálním počítači RRAS a nainstaluje směrování a všechny požadované závislé služby a funkce. V případě potřeby můžete tento DSC přizpůsobit dál. Rozšíření vlastních skriptů spustí následující skript a `Add-Site2Site.ps1` nakonfiguruje tunel VPNS2S mezi dvěma servery RRAS se sdíleným klíčem. Pokud chcete zobrazit výsledky konfigurace tunelu VPN, můžete zobrazit podrobný výstup z rozšíření vlastních skriptů.

![Diagram s názvem S2SVPNTunnel zobrazuje dva virtuální sítěy připojené tunelem VPN typu Site-to-site.](./media/azure-stack-network-howto-vnet-peering/s2svpntunnels2.svg)

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)  
