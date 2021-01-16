---
title: Konfigurace bran firewall pro Azure Stack HCI
description: V tomto tématu najdete pokyny ke konfiguraci bran firewall pro operační systém Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: a67881f2dd4be5e4dce5fb967c88484c27025624
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255227"
---
# <a name="configure-firewalls-for-azure-stack-hci"></a>Konfigurace bran firewall pro Azure Stack HCI

>Platí pro: Azure Stack HCI, verze 20H2

V tomto tématu najdete pokyny ke konfiguraci bran firewall pro operační systém Azure Stack HCI. Zahrnuje požadavky na připojení a vysvětluje, jak značky služby seskupují IP adresy skupin v Azure, k jejichž přístupu potřebuje operační systém. V tématu najdete také postup aktualizace firewallu v programu Microsoft Defender.

## <a name="connectivity-requirements"></a>Požadavky na připojení
Azure Stack HCI se musí pravidelně připojovat k Azure. Přístup je omezený jenom na:
- Dobře známé IP adresy Azure
- Odchozí směr
- Port 443 (HTTPS)

Další informace najdete v části Azure Stack připojení ke službě HCI v [Azure Stack HCL – Nejčastější dotazy](../faq.md) .

Toto téma popisuje, jak volitelně použít vysoce uzamčenou konfiguraci brány firewall k blokování všech přenosů do všech cílů kromě těch, které jsou uvedeny na seznamu povolených.

   >[!IMPORTANT]
   > Pokud je odchozí připojení omezeno externí firemní bránou firewall nebo proxy server, ujistěte se, že adresy URL uvedené v následující tabulce nejsou blokované. Související informace najdete v části "konfigurace sítě" v tématu [Přehled agenta serverů s podporou ARC Azure](/azure/azure-arc/servers/agent-overview#networking-configuration).


Jak vidíte níže, Azure Stack HCL přistupuje k Azure pomocí více než jedné brány firewall potenciálně.

:::image type="content" source="./media/configure-firewalls/firewalls-diagram.png" alt-text="Diagram zobrazuje Azure Stack HCL přístup k koncovým bodům značek Service prostřednictvím portu 443 (HTTPS) bran firewall." lightbox="./media/configure-firewalls/firewalls-diagram.png":::

## <a name="working-with-service-tags"></a>Práce s visačkami služeb
*Značka služby* představuje skupinu IP adres z dané služby Azure. Společnost Microsoft spravuje IP adresy, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako IP adresy, aby byly aktualizace minimální. Další informace najdete v tématu [značky služby virtuální sítě](/azure/virtual-network/service-tags-overview).

## <a name="required-endpoint-daily-access-after-azure-registration"></a>Denní přístup k požadovanému koncovému bodu (po registraci Azure)
Azure udržuje správné známé IP adresy pro služby Azure, které jsou organizované pomocí značek služeb. Azure publikuje týdenní soubor JSON všech IP adres pro každou službu. IP adresy se často nemění, ale mění se několik časů za rok. V následující tabulce jsou uvedeny koncové body značek služby, které operační systém potřebuje k přístupu.

| Popis                   | Značka služby pro rozsah IP adres  | URL                                                                                 |
| :-----------------------------| :-----------------------  | :---------------------------------------------------------------------------------- |
| Azure Active Directory        | Azureactivedirectory selhala      | `https://login.microsoftonline.com`<br> `https://graph.microsoft.com`               |
| Azure Resource Manager        | AzureResourceManager      | `https://management.azure.com`                        |
| Cloudová služba Azure Stack HCI | AzureFrontDoor. front-end   | `https://azurestackhci.azurefd.net` |
| Azure Arc                     | AzureArcInfrastructure<br> AzureTrafficManager | Závisí na funkcích, které chcete použít:<br> Služba hybridní identity: `*.his.arc.azure.com`<br> Konfigurace hosta: `*.guestconfiguration.azure.com`<br> **Poznámka:** Při povolování více funkcí je očekáváno více adres URL. |

## <a name="update-microsoft-defender-firewall"></a>Aktualizovat firewall v programu Microsoft Defender
V této části se dozvíte, jak nakonfigurovat bránu firewall v programu Microsoft Defender tak, aby povolovala IP adresy přidružené ke značce služby pro připojení k operačnímu systému:

1. Stáhněte soubor JSON z následujícího prostředku do cílového počítače, na kterém běží operační systém: [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519).

1. K otevření souboru JSON použijte následující příkaz prostředí PowerShell:

    ```powershell
    $json = Get-Content -Path .\ServiceTags_Public_20201012.json | ConvertFrom-Json
    ```

1. Získá seznam rozsahů IP adres pro danou značku služby, jako je například značka služby AzureResourceManager:

    ```powershell
    $IpList = ($json.values | where Name -Eq "AzureResourceManager").properties.addressPrefixes
    ```

1. Pokud používáte seznam povolených adres, importujte seznam IP adres na externí podnikovou bránu firewall.

1. Vytvořte pravidlo brány firewall pro každý server v clusteru, abyste povolili odchozí přenosy 443 (HTTPS) do seznamu rozsahů IP adres:

    ```powershell
    New-NetFirewallRule -DisplayName "Allow Azure Resource Manager" -RemoteAddress $IpList -Direction Outbound -LocalPort 443 -Protocol TCP -Action Allow -Profile Any -Enabled True
    ```

## <a name="additional-endpoint-for-one-time-azure-registration"></a>Dodatečný koncový bod pro jednorázovou registraci Azure
Když v rámci procesu registrace Azure spouštíte `Register-AzStackHCI` nebo použijete centrum pro správu Windows, pokusí se rutina kontaktovat Galerie prostředí PowerShell, aby ověřil, že máte nejnovější verzi požadovaných modulů PowerShellu, třeba AZ a AzureAD. I když je Galerie prostředí PowerShell hostovaný v Azure, pro ni aktuálně není k dispozici žádný příznak služby. Pokud nemůžete spustit `Register-AzStackHCI` rutinu z uzlu serveru z důvodu nedostatku přístupu k Internetu, doporučujeme stáhnout moduly do počítače pro správu a pak je ručně přenést do uzlu serveru, kde chcete spustit rutinu.

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu také:
- Část připojení [Azure Stack HCL – Nejčastější dotazy](../faq.md)