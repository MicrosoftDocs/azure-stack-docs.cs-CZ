---
title: Známé problémy centra Azure Stack pro robustní centrum Azure Stack
description: Přečtěte si o známých problémech v centru Azure Stack Azure Stackovém centru, které je robustní.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: 4b75024a1ff747d39f4f2194d0d86c3882475dee
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939571"
---
# <a name="known-issues-in-azure-stack-hub-ruggedized"></a>Známé problémy v případě robustního centra Azure Stack 

V tomto článku jsou uvedené známé problémy v Azure Stackovém centru, které je robustní. Seznam se aktualizuje, protože se identifikují nové problémy.

## <a name="update"></a>Aktualizace

Známé problémy s aktualizacemi centra Azure Stack najdete [v tématu řešení potíží s aktualizacemi v centru Azure Stack](../operator/azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Při aktualizaci se nepovedlo nainstalovat balíček Microsoft. AzureStack. Compute. Installer do virtuálního počítače certifikační autority.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: během aktualizace proces převezme zámek na novém obsahu, který se musí zkopírovat do virtuálního počítače certifikační autority. V případě, že se aktualizace nezdařila, je zámek uvolněn.
- Náprava: pokračovat v aktualizaci.
- Výskyt: vzácná

## <a name="portal"></a>Portál

### <a name="administrative-subscriptions"></a>Předplatná pro správu

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: nemusíte používat dvě předplatná pro správu, která byla představena s verzí 1804. Typy předplatného jsou odběry **měření** a předplatné **spotřeby** .
- Náprava: Pokud máte na těchto dvou předplatných prostředky, znovu je vytvořte v předplatných uživatele.
- Výskyt: běžné

## <a name="networking"></a>Sítě

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

#### <a name="denyalloutbound-rule-cannot-be-created"></a>Pravidlo DenyAllOutbound nejde vytvořit.

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: explicitní pravidlo **DenyAllOutbound** na Internet nejde během vytváření virtuálního počítače vytvořit v NSG, protože to zabrání dokončení komunikace vyžadované pro nasazení virtuálního počítače. Tím se taky odmítne dvě základní IP adresy, které jsou potřeba k nasazení virtuálních počítačů: DHCP IP: 169.254.169.254 a DNS IP: 168.63.129.16.

- Náprava: během vytváření virtuálního počítače povolte odchozí přenosy na Internet a po dokončení vytváření virtuálního počítače změňte NSG tak, aby zablokovala požadovaný provoz.
- Výskyt: běžné

### <a name="virtual-network-gateway"></a>Brána virtuální sítě

#### <a name="documentation"></a>Dokumentace

- Platí: Tento problém se vztahuje na všechny podporované verze.
- Příčina: dokumentace k dokumentaci na stránce Přehled na Virtual Network bráně odkazuje na Azure místo centra Azure Stack. Pro dokumentaci centra Azure Stack použijte následující odkazy:

  - [Skladové položky brány](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Připojení s vysokou dostupností](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Konfigurace protokolu BGP v centru Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Okruhy ExpressRoute](../operator/azure-stack-connect-expressroute.md)
  - [Zadat vlastní zásady IPsec/IKE](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer směrování provozu na jeden back-end virtuální počítač v určitých scénářích

- Platí: Tento problém se vztahuje na všechny podporované verze. 
- Příčina: při povolování **spřažení relace** na nástroji pro vyrovnávání zatížení se 2 řazená kolekce členů použije místo privátních IP adres přiřazených k virtuálním počítačům IP adresa PA (IP adresa fyzické adresy). Ve scénářích, kdy se provoz směrovaného na nástroj pro vyrovnávání zatížení dorazí přes síť VPN, nebo pokud je povolené, aby se všechny virtuální počítače klienta (zdrojové IP adresy) nacházely na stejném uzlu a spřažení relace, veškerý provoz se směruje na jeden back-end virtuální počítač.
- Výskyt: běžné
