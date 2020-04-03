---
title: Publikování služby Azure Stack hub ve vašem datovém centru
description: Naučte se publikovat Azure Stack služby centra ve svém datovém centru.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 12/11/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: c43cddeca1067d1bbbe2ef0ec8a62ad3c041a50b
ms.sourcegitcommit: 48e493256b0b8bd6cea931cd68a9bd932ca77090
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80614497"
---
# <a name="publish-azure-stack-hub-services-in-your-datacenter"></a>Publikování služby Azure Stack hub ve vašem datovém centru 

Centrum Azure Stack nastaví virtuální IP adresy (VIP) pro své role infrastruktury. Tyto VIP adresy se přiřazují z fondu veřejných IP adres. Každá virtuální IP adresa je zabezpečená pomocí seznamu řízení přístupu (ACL) ve vrstvě softwarově definované sítě. Seznamy řízení přístupu (ACL) se používají také napříč fyzickými přepínači (tory a BMC) k dalšímu posílení zabezpečení řešení. Pro každý koncový bod v externí zóně DNS, která je zadaná v době nasazení, se vytvoří položka DNS. Například uživatelský portál má přiřazenou položku hostitel DNS na portálu. *&lt;oblast >.&lt;plně kvalifikovaný název domény >* .

Následující diagram architektury znázorňuje různé vrstvy sítě a seznamy ACL:

![Diagram znázorňující různé vrstvy sítě a seznamy ACL](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

### <a name="ports-and-urls"></a>Porty a adresy URL

Chcete-li zpřístupnit služby Azure Stack hub (například portály, Azure Resource Manager, DNS atd.) k dispozici pro externí sítě, musíte povolit příchozí provoz do těchto koncových bodů pro konkrétní adresy URL, porty a protokoly.
 
V nasazení, kde transparentní proxy odchozí připojení k tradičnímu proxy server nebo brána firewall chrání řešení, musíte povolit konkrétní porty a adresy URL pro [příchozí](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) i [odchozí](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) komunikaci. Patří mezi ně porty a adresy URL pro identitu, tržiště, opravu a aktualizace, registrace a data o využití.

Zachycení provozu SSL není [podporované](azure-stack-firewall.md#ssl-interception) a může při přístupu k koncovým bodům způsobit selhání služby. 

## <a name="ports-and-protocols-inbound"></a>Porty a protokoly (příchozí)

K publikování koncových bodů centra Azure Stack do externích sítí se vyžaduje sada virtuálních IP adres infrastruktury. Tabulka *koncový bod (VIP)* zobrazuje jednotlivé koncové body, požadovaný port a protokol. V dokumentaci k nasazení konkrétního poskytovatele prostředků najdete koncové body, které vyžadují další poskytovatele prostředků, jako je třeba poskytovatel prostředků SQL.

Interní virtuální IP adresy infrastruktury nejsou uvedené, protože nejsou nutné k publikování centra Azure Stack. Uživatelské virtuální IP adresy jsou dynamické a definované uživateli, bez řízení pomocí operátoru centra Azure Stack.

> [!Note]  
> IKEv2 VPN je řešení IPsec VPN založené na standardech, které používá port UDP 500 a 4500 a port TCP 50. Brány firewall tyto porty nikdy neotevírají, takže VPN typu IKEv2 nemusí být schopná procházet proxy a brány firewall.

Po přidání [hostitele rozšíření](azure-stack-extension-host-prepare.md)nejsou porty v rozsahu 12495-30015 vyžadovány.

|Koncový bod (VIP)|DNS host A record|Protokol|Porty|
|---------|---------|---------|---------|
|AD FS|službou. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|443|
|Portál (správce)|Adminportal. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|443|
|Adminhosting | *.adminhosting. >\<oblasti.\<plně kvalifikovaný název domény > | HTTPS | 443 |
|Azure Resource Manager (správce)|Adminmanagement. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|443|
|Portál (uživatel)|Bran. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|443|
|Azure Resource Manager (uživatel)|Správu. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|443|
|Graf|zapisovací. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|443|
|Seznam odvolaných certifikátů|Seznam CRL. *&lt;oblast >.&lt;plně kvalifikovaný název domény >*|HTTP|80|
|DNS|&#42;. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|TCP & UDP|53|
|Hosting | *. Hosting. >\<oblasti.\<plně kvalifikovaný název domény > | HTTPS | 443 |
|Key Vault (uživatel)|&#42;hesel. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|443|
|Key Vault (správce)|&#42;.adminvault. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|443|
|Fronta úložiště|&#42;provedených. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTP<br>HTTPS|80<br>443|
|Tabulka úložiště|&#42;stolní. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;příznaky. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTP<br>HTTPS|80<br>443|
|Poskytovatel prostředků SQL|sqladapter.dbadapter. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|44300-44304|
|Poskytovatel prostředků MySQL|mysqladapter.dbadapter. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|HTTPS|44300-44304|
|App Service|&#42;AppService. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;. SCM. AppService. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|TCP|443 (HTTPS)|
|  |API. AppService. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |FTP. AppService. *>&lt;oblasti.&lt;plně kvalifikovaný název domény >*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Brány VPN Gateway|     |     |[Podívejte se na téma Nejčastější dotazy ke službě VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Porty a adresy URL (odchozí)

Centrum Azure Stack podporuje jenom transparentní proxy servery. V nasazení pomocí transparentního odchozího připojení proxy k tradičnímu proxy server musíte pro odchozí komunikaci použít porty a adresy URL v následující tabulce.

Zachycení provozu SSL není [podporované](azure-stack-firewall.md#ssl-interception) a může při přístupu k koncovým bodům způsobit selhání služby. Maximální podporovaný časový limit pro komunikaci s koncovými body vyžadovanými pro identitu je 60 s.

> [!Note]  
> Centrum Azure Stack nepodporuje použití ExpressRoute pro přístup ke službám Azure uvedeným v následující tabulce, protože ExpressRoute nemusí být schopná směrovat provoz do všech koncových bodů.

|Účel|Cílová adresa URL|Protokol|Porty|Zdrojová síť|
|---------|---------|---------|---------|---------|
|Identita|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https:\//management.core.windows.net<br>ARMUri = https:\//management.azure.com<br>https:\//\*. msftauth.net<br>https:\//\*. msauth.net<br>https:\//\*. msocdn.com<br>**Azure Government**<br>https:\//login.microsoftonline.us/<br>https:\//graph.windows.net/<br>**Azure Čína 21Vianet**<br>https:\//login.chinacloudapi.cn/<br>https:\//graph.chinacloudapi.cn/<br>**Azure (Německo)**<br>https:\//login.microsoftonline.de/<br>https:\//graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Veřejná VIP-/27<br>Síť veřejné infrastruktury|
|Syndikace Marketplace|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>https://&#42;. blob.Core.usgovcloudapi.NET/<br>**Azure Čína 21Vianet**<br>https:\//management.chinacloudapi.cn/<br>http://&#42;. blob.Core.chinacloudapi.cn|HTTPS|443|Veřejná VIP-/27|
|Oprava & aktualizace|https://&#42;.azureedge.net<br>https:\//aka.ms/azurestackautomaticupdate|HTTPS|443|Veřejná VIP-/27|
|Registrace|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//management.usgovcloudapi.net/<br>**Azure Čína 21Vianet**<br>https:\//management.chinacloudapi.cn|HTTPS|443|Veřejná VIP-/27|
|Využití|**Azure**<br>https://&#42;. trafficmanager.NET<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.NET<br>**Azure Čína 21Vianet**<br>https://&#42;. trafficmanager.cn|HTTPS|443|Veřejná VIP-/27|
|Windows Defender|&#42;. wdcp.microsoft.com<br>&#42;. wdcpalt.microsoft.com<br>&#42;. wd.microsoft.com<br>&#42;. update.microsoft.com<br>&#42;. download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Veřejná VIP-/27<br>Síť veřejné infrastruktury|
|NTP|(IP adresa serveru NTP, který je k dispozici pro nasazení)|UDP|123|Veřejná VIP-/27|
|DNS|(IP adresa serveru DNS poskytnutá pro nasazení)|TCP<br>UDP|53|Veřejná VIP-/27|
|SYSLOG|(IP adresa serveru SYSLOG poskytnutého pro nasazení)|TCP<br>UDP|6514<br>514|Veřejná VIP-/27|
|VOLANÝ|(Adresa URL v rámci distribučních bodů seznamu CRL na vašem certifikátu)|HTTP|80|Veřejná VIP-/27|
|LDAP|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP<br>UDP|389|Veřejná VIP-/27|
|LDAP SSL|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|636|Veřejná VIP-/27|
|GC PROTOKOLU LDAP|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|3268|Veřejná VIP-/27|
|LDAP GC SSL|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|3269|Veřejná VIP-/27|
|AD FS|Pro integraci AD FS AD FS poskytnutý koncový bod metadat|TCP|443|Veřejná VIP-/27|
|Služba Shromažďování protokolů diagnostiky|Azure Storage zadaná adresa URL SAS objektu BLOB|HTTPS|443|Veřejná VIP-/27|
|     |     |     |     |     |

Odchozí adresy URL využívají vyrovnávání zatížení pomocí Azure Traffic Manageru a poskytují nejlepší možné připojení na základě geografického umístění. V případě adres URL s vyrovnáváním zatížení může Microsoft aktualizovat koncové body back-endu, aniž by to ovlivnilo zákazníky Microsoft nesdílí seznam IP adres pro adresy URL s vyrovnáváním zatížení. Použijte zařízení, které podporuje filtrování podle adresy URL, nikoli podle IP adresy.

Odchozí DNS se vyžaduje ve všech případech. To znamená, že se jedná o zdroj dotazování externí služby DNS a o tom, jaký typ integrace identity byl vybrán. V průběhu nasazení v připojeném scénáři potřebuje DVM, který je umístěn v síti řadiče pro správu základní desky, odchozí přístup. Ale po nasazení se služba DNS přesune do interní součásti, která odešle dotazy prostřednictvím veřejné virtuální IP adresy. V tuto chvíli je možné odebrat odchozí přístup k DNS prostřednictvím sítě řadiče pro správu základní desky, ale přístup k tomuto serveru DNS pomocí veřejné VIP musí zůstat nebo jinak se ověřování nezdaří.

## <a name="next-steps"></a>Další kroky

[Požadavky na infrastrukturu veřejných klíčů centra Azure Stack](azure-stack-pki-certs.md)
