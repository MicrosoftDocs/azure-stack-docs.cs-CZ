---
title: Řešení potíží s připojením VPN typu Site-to-site v centru Azure Stack
description: Postup řešení potíží, který můžete provést po konfiguraci připojení VPN typu Site-to-site mezi místní sítí a virtuální sítí centra Azure Stack.
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5277e908fa9f3c5d8fbcebf28b4d766045d03187
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342901"
---
# <a name="troubleshoot-site-to-site-vpn-connections"></a>Řešení potíží s připojením VPN typu Site-to-site

Tento článek popisuje kroky, které můžete provést po konfiguraci připojení VPN typu Site-to-Site (S2S) mezi místní sítí a virtuální sítí centra Azure Stack, a připojení se náhle zastaví a nedá se znovu připojit.

Pokud se problém s centrem Azure Stack nezabývá v tomto článku, můžete navštívit [fórum služby Azure Stack centra MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Můžete také odeslat žádost o podporu Azure. Podívejte se prosím na [podporu centra Azure Stack](../operator/azure-stack-manage-basics.md#where-to-get-support).

## <a name="initial-troubleshooting-steps"></a>První kroky při řešení potíží

Výchozí parametry centra Azure Stack pro protokol IPsec/IKEV2 se [od verze 1910 buildu](../user/azure-stack-vpn-gateway-settings.md#ike-phase-1-main-mode-parameters) změnily. Obraťte se na svého operátora centra Azure Stack, kde najdete další informace o verzi buildu.

> [!IMPORTANT]
> Při použití tunelového propojení S2S jsou pakety dále zapouzdřeny pomocí dalších hlaviček. Tento zapouzdření zvyšuje celkovou velikost paketu. V těchto scénářích je potřeba, abyste v **1350**zasvorki TCP **MSS** . Pokud vaše zařízení VPN nepodporují svorky MSS, můžete místo toho nastavit jednotku MTU v rozhraní tunelu na **1400** bajtů. Další informace najdete v tématu [ladění výkonu protokolu virtuální sítě TCP](/azure/virtual-network/virtual-network-tcpip-performance-tuning).

- Ověřte, že je konfigurace sítě VPN založená na směrování (IKEv2). Centrum Azure Stack nepodporuje konfigurace založené na zásadách (IKEv1).

- Ověřte, zda používáte [ověřené zařízení VPN a verzi operačního systému](/azure/vpn-gateway/vpn-gateway-about-vpn-devices#devicetable). Pokud se nejedná o ověřené zařízení VPN, možná budete muset kontaktovat výrobce zařízení a zjistit, jestli došlo k potížím s kompatibilitou.

- Ověřte IP adresy partnerského uzlu sítě VPN:

  - Definice IP adresy v objektu **brány místní sítě** v centru Azure stacku by měla odpovídat místní IP adrese zařízení.

  - Definice IP adresy brány centra Azure Stack nastavená na místním zařízení by se měla shodovat s IP adresou brány centra Azure Stack.

## <a name="status-not-connected---intermittent-disconnects"></a>Stav "Nepřipojeno" – přerušované odpojení

- Porovnejte sdílený klíč pro místní zařízení VPN s virtuální sítí VPN AzSH a ujistěte se, že se klíče shodují. Pokud chcete zobrazit sdílený klíč pro připojení k síti VPN AzSH, použijte jednu z následujících metod:

  - **Portál tenanta centra Azure Stack**: Přejít na připojení typu Site-to-site brány VPN, které jste vytvořili. V části **Nastavení** vyberte možnost **sdílený klíč**.

      :::image type="content" source="media/site-to-site/vpn-connection.png" alt-text="Připojení VPN":::

  - **Azure PowerShell**: použijte následující příkaz prostředí PowerShell:

      ```powershell
      Get-AzureRMVirtualNetworkGatewayConnectionSharedKey -Name <Connection name> -ResourceGroupName <Resource group>
      ```

## <a name="status-connected--traffic-not-flowing"></a>Stav "připojeno" – přenos bez toku dat

- Vyhledejte a odstraňte UDR (User-Defined Routing) a skupiny zabezpečení sítě (skupin zabezpečení sítě) v podsíti brány a pak otestujte výsledek. Pokud je problém vyřešen, ověřte nastavení, které UDR nebo NSG použili.

   Trasa definovaná uživatelem v podsíti brány může omezit určitý provoz a povolit další provoz. Tím se zobrazí, že připojení VPN není pro určitý provoz spolehlivé a dobré pro ostatní.

- Ověřte adresu externího rozhraní zařízení VPN na pracovišti. 

  - Pokud je internetová IP adresa zařízení VPN zahrnutá v definici **místní sítě** v centru Azure Stack, může se stát, že se nastanou nepřipojení.

  - Externí rozhraní zařízení musí být přímo na internetu. Mezi Internetem a zařízením by neměl být žádný překlad síťových adres ani brána firewall.

  - Pokud chcete nakonfigurovat clustery brány firewall tak, aby měly virtuální IP adresu, musíte cluster přerušit a vystavit zařízení VPN přímo veřejnému rozhraní, se kterým může Brána používat rozhraní.

- Ověřte, že se podsítě přesně shodují.

  - Ověřte, že se adresní prostory virtuální sítě přesně shodují mezi virtuální sítí centra Azure Stack a místními definicemi.

  - Ověřte, že se podsítě přesně shodují mezi **bránou místní sítě a místními** definicemi místní sítě.

## <a name="create-a-support-ticket"></a>Vytvoření lístku podpory

Pokud žádný z předchozích kroků problém nevyřeší, vytvořte prosím [lístek podpory](../operator/azure-stack-manage-basics.md#where-to-get-support) a použijte [Nástroj pro shromažďování protokolů na vyžádání](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md) k poskytnutí protokolů.
