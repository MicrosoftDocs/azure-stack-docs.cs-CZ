---
title: Připojení serveru k Azure Virtual Network – síťový adaptér Azure
description: Tento článek popisuje požadavky a postupy, jak pomocí síťového adaptéru Azure připojit server k Virtual Network Azure.
ms.topic: article
author: thomasmaurer
ms.author: thmaure
ms.date: 07/14/2020
ms.openlocfilehash: 523ee87a5673a79dbb17c18251fc8837b9d2f7e4
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573645"
---
# <a name="use-azure-network-adapter-to-connect-a-server-to-an-azure-virtual-network"></a>Připojení serveru k Azure Virtual Network pomocí síťového adaptéru Azure

>Platí pro: Windows Server 2019, Windows Server 2016, Windows Server 2012 R2

Spousta úloh běžících místně a v prostředí s více cloudy vyžaduje připojení k virtuálním počítačům, které běží v Microsoft Azure. Pokud chcete připojit server k Virtual Network Azure, máte několik možností, mezi které patří VPN typu Site-to-site, Azure Express Route a VPN typu Point-to-site.

Centrum pro správu Windows a síťový adaptér Azure poskytují prostředí jedním kliknutím pro připojení serveru k virtuální síti pomocí připojení [VPN typu Point-to-site](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) . Proces automatizuje konfiguraci brány virtuální sítě a místního klienta VPN.

## <a name="when-to-use-azure-network-adapter"></a>Kdy použít síťový adaptér Azure
Připojení VPN typu Point-to-site pro síťové adaptéry Azure jsou užitečná, když se chcete připojit k virtuální síti ze vzdáleného umístění, například z pobočky, úložiště nebo jiného umístění. Pokud pro připojení k virtuální síti potřebujete jenom několik serverů, můžete použít taky síťový adaptér Azure místo VPN typu Site-to-site. Připojení síťových adaptérů Azure nevyžadují zařízení VPN ani veřejnou IP adresu.

### <a name="requirements"></a>Požadavky
Použití síťového adaptéru Azure pro připojení k virtuální síti vyžaduje následující:
- Účet Azure s aspoň jedním aktivním předplatným.
- Existující virtuální síť.
- Přístup k Internetu pro cílové servery, které chcete připojit ke službě Azure Virtual Network.
- Připojení centra pro správu systému Windows k Azure.
  Další informace najdete v tématu [Konfigurace integrace Azure](/windows-server/manage/windows-admin-center/azure/azure-integration).
- Nejnovější verzi centra pro správu systému Windows.
  Další informace najdete v [centru pro správu systému Windows](https://www.microsoft.com/windows-server/windows-admin-center).

> [!NOTE]
> Na serveru, který chcete připojit k Azure, není nutné instalovat centrum pro správu systému Windows. Můžete to ale udělat v případě jednoho serveru.

## <a name="add-an-azure-network-adapter-to-a-server"></a>Přidání síťového adaptéru Azure na server
Pokud chcete nakonfigurovat síťový adaptér Azure, vyhledejte v centru pro správu Windows síťové rozšíření pro IT.

V centru pro správu systému Windows:
1. Přejděte na server, který je hostitelem virtuálních počítačů, které chcete přidat do síťového adaptéru Azure.
1. V části **nástroje**vyberte **sítě**.
1. Vyberte **Přidat síťový adaptér Azure**.
1. V podokně **Přidat síťový adaptér Azure** zadejte následující požadované informace a pak vyberte **vytvořit**:
    - **Předplatné**
    - **Umístění**
    - **Virtual Network**
    - **Podsíť brány** (Pokud neexistuje)
    - **SKU brány** (Pokud neexistuje)
    - **Adresní prostor klienta**

        Fond adres klienta je rozsah privátních IP adres, který zadáte. Klienti připojující se přes síť VPN typu Point-to-Site dynamicky obdrží IP adresu z tohoto rozsahu. Použijte rozsah privátních IP adres, který se nepřekrývá s místním umístěním, ze kterého se připojujete, nebo s virtuální sítí, ke které se chcete připojit. Doporučujeme používat IP adresy, které jsou v rozsahu určeném pro privátní sítě (10. x. x. x, 192.168. x. x nebo 172.16.0.0 až 172.31.255.255).

    - **Ověřovací certifikát**

        Azure používá certifikáty k ověřování klientů, kteří se připojují k virtuální síti přes připojení VPN typu Point-to-site. Informace o veřejném klíči kořenového certifikátu se nahrají do Azure. Kořenový certifikát se pak Azure považuje za důvěryhodný pro připojení typu Point-to-site k virtuální síti. Klientské certifikáty musí být vygenerovány z důvěryhodného kořenového certifikátu a nainstalovány na klientském serveru. Klientský certifikát se používá k ověření klienta při inicializaci připojení k virtuální síti.
    
        Další informace najdete v části "konfigurace typu ověřování" v tématu [Konfigurace připojení VPN typu Point-to-site k virtuální síti s použitím nativního ověřování certifikátů Azure: Azure Portal](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal).

    :::image type="content" source="media/azure-network-adapter/add-azure-network-adapter.png" alt-text="Podokno přidat síťový adaptér Azure v centru pro správu systému Windows.":::

> [!NOTE]
> Síťová zařízení, jako jsou VPN Gateway a Application Gateway, která běží ve virtuální síti, přináší další náklady. Další informace najdete v tématu [Virtual Network ceny](https://azure.microsoft.com/pricing/details/virtual-network/).

Pokud neexistuje žádná existující brána Azure Virtual Network, centrum pro správu systému Windows jednu za vás vytvoří. Proces instalace může trvat až 25 minut. Po vytvoření síťového adaptéru Azure můžete spustit přístup k virtuálním počítačům ve virtuální síti přímo z vašeho serveru.

Pokud už připojení nepotřebujete, vyberte v části **sítě**síťový adaptér Azure, který chcete odpojit, v horní nabídce vyberte **Odpojit**a pak v místním okně pro **potvrzení odpojení VPN** vyberte **Ano**.

## <a name="next-steps"></a>Další kroky
Další informace o službě Azure Virtual Network najdete v tématu také:

- [Nejčastější dotazy ke službě Azure Virtual Network](/azure/virtual-network/virtual-networks-faq)
