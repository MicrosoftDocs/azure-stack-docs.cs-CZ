---
title: Integrace Azure Stack Datacenter – publikování koncových bodů | Microsoft Docs
description: Informace o publikování Azure Stackch koncových bodů ve vašem datovém centru
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 07/30/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/30/2019
ms.openlocfilehash: b97d542c5a885078fa80108cdb0c16e6ccb79b98
ms.sourcegitcommit: 0e0d010c4e010f2fd6799471db8bf71652d8d4e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68806953"
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Integrace Azure Stack Datacenter – publikování koncových bodů

Azure Stack nastaví virtuální IP adresy (VIP) pro své role infrastruktury. Tyto VIP adresy se přiřazují z fondu veřejných IP adres. Každá virtuální IP adresa je zabezpečená pomocí seznamu řízení přístupu (ACL) ve vrstvě softwarově definované sítě. Seznamy řízení přístupu (ACL) se používají také napříč fyzickými přepínači (tory a BMC) k dalšímu posílení zabezpečení řešení. Položka DNS se vytvoří pro každý koncový bod v externí zóně DNS, která se zadala v době nasazení.


Následující diagram architektury znázorňuje různé vrstvy sítě a seznamy ACL:

![Strukturální obrázek](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>Porty a protokoly (příchozí)

K publikování koncových bodů Azure Stack externích sítích se vyžaduje sada virtuálních IP adres infrastruktury. Tabulka *koncový bod (VIP)* zobrazuje jednotlivé koncové body, požadovaný port a protokol. V dokumentaci k nasazení konkrétního poskytovatele prostředků najdete koncové body, které vyžadují další poskytovatele prostředků, jako je třeba poskytovatel prostředků SQL.

Interní virtuální IP adresy infrastruktury nejsou uvedené, protože nejsou nutné k publikování Azure Stack.

> [!Note]  
> Uživatelské virtuální IP adresy jsou dynamické, definovány vlastními uživateli bez řízení operátorem Azure Stack.

> [!Note]  
> IKEv2 VPN. IKEv2 VPN řešení IPsec VPN založené na standardech, které využívá porty UDP 500 a 4500 a protokol IP č. 50. Brány firewall tyto porty neotvírají vždycky, takže je možné, že IKEv2 VPN nebude moct procházet servery proxy a branami firewall.

> [!Note]  
> Od aktualizace 1811 již není nutné, aby byly porty v rozsahu 12495-30015 otevřené z důvodu přidání [hostitele rozšíření](azure-stack-extension-host-prepare.md).

|Koncový bod (VIP)|DNS host A record|Protocol|Porty|
|---------|---------|---------|---------|
|AD FS|Adfs. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Portál (správce)|Adminportal.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Adminhosting | *.adminhosting.\<region>.\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (správce)|Adminmanagement.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Portál (uživatel)|Bran.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Azure Resource Manager (uživatel)|Správu.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Graph|Graph. *&lt;region>.&lt;fqdn>*|HTTPS|443|
|Seznam odvolaných certifikátů|Seznam CRL. *&lt;> oblasti&lt; . plně kvalifikovaný název domény >*|HTTP|80|
|DNS|&#42;.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*|TCP & UDP|53|
|Hostování | *.hosting.\<region>.\<fqdn> | HTTPS | 443 |
|Key Vault (uživatel)|&#42;hesel.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Key Vault (správce)|&#42;.adminvault.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*|HTTPS|443|
|Fronta úložiště|&#42;.queue. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Tabulka úložiště|&#42;.table. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;.blob. *&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|Poskytovatel prostředků SQL|sqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|Poskytovatel prostředků MySQL|mysqladapter.dbadapter. *&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service|&#42;.appservice. *&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice. *&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice. *&lt;region>.&lt;fqdn>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Brány VPN Gateway|     |     |[Podívejte se na téma Nejčastější dotazy ke službě VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Porty a adresy URL (odchozí)

Azure Stack podporuje pouze transparentní proxy servery. V nasazení pomocí transparentního odchozího připojení proxy k tradičnímu proxy server musíte pro odchozí komunikaci použít porty a adresy URL v následující tabulce.

> [!Note]  
> Azure Stack nepodporuje použití ExpressRoute pro přístup ke službám Azure uvedeným v následující tabulce.

|Účel|Cílová adresa URL|Protocol|Porty|Zdrojová síť|
|---------|---------|---------|---------|---------|
|Identita|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>**Azure Government**<br>https:\//Login.microsoftonline.us/<br>https:\//Graph.Windows.NET/<br>**Azure China 21Vianet**<br>https:\//Login.chinacloudapi.cn/<br>https:\//Graph.chinacloudapi.cn/<br>|HTTP<br>HTTPS|80<br>443|Veřejná VIP-/27<br>Síť veřejné infrastruktury|
|Syndikace Marketplace|**Azure**<br>https:\//management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://&#42;.azureedge.net<br>**Azure Government**<br>https:\//Management.usgovcloudapi.NET/<br>https://&#42;. blob.Core.usgovcloudapi.NET/<br>**Azure China 21Vianet**<br>https:\//Management.chinacloudapi.cn/<br>http://&#42;. blob.Core.chinacloudapi.cn/|HTTPS|443|Veřejná VIP-/27|
|Oprava & aktualizace|https://&#42;.azureedge.net<br>https:\//aka.MS/azurestackautomaticupdate|HTTPS|443|Veřejná VIP-/27|
|Registrace|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https:\//Management.usgovcloudapi.NET/<br>**Azure China 21Vianet**<br>https:\//Management.chinacloudapi.cn/|HTTPS|443|Veřejná VIP-/27|
|Použití|**Azure**<br>https://&#42;. trafficmanager.NET<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.NET<br>**Azure China 21Vianet**<br>https://&#42;. trafficmanager.cn|HTTPS|443|Veřejná VIP-/27|
|Windows Defender|&#42;. wdcp.microsoft.com<br>&#42;. wdcpalt.microsoft.com<br>&#42;. wd.microsoft.com<br>&#42;. update.microsoft.com<br>&#42;. download.microsoft.com<br>https:\//www.microsoft.com/pkiops/crl<br>https:\//www.microsoft.com/pkiops/certs<br>https:\//crl.microsoft.com/pki/crl/products<br>https:\//www.microsoft.com/pki/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Veřejná VIP-/27<br>Síť veřejné infrastruktury|
|NTP|(IP adresa serveru NTP, který je k dispozici pro nasazení)|UDP|123|Veřejná VIP-/27|
|DNS|(IP adresa serveru DNS poskytnutá pro nasazení)|TCP<br>UDP|53|Veřejná VIP-/27|
|VOLANÝ|(Adresa URL v rámci distribučních bodů seznamu CRL na vašem certifikátu)|HTTP|80|Veřejná VIP-/27|
|LDAP|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP<br>UDP|389|Veřejná VIP-/27|
|LDAP SSL|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|636|Veřejná VIP-/27|
|GC PROTOKOLU LDAP|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|3268|Veřejná VIP-/27|
|LDAP GC SSL|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|3269|Veřejná VIP-/27|
|AD FS|Pro integraci AD FS AD FS poskytnutý koncový bod metadat|TCP|443|Veřejná VIP-/27|
|Služba Shromažďování protokolů diagnostiky|Azure Storage zadaná adresa URL SAS objektu BLOB|HTTPS|443|Veřejná VIP-/27|
|     |     |     |     |     |

Odchozí adresy URL využívají vyrovnávání zatížení pomocí Azure Traffic Manageru a poskytují nejlepší možnou konektivitu na základě geografického umístění. V případě adres URL s vyrovnáváním zatížení může společnost Microsoft aktualizovat a změnit koncové body back-endu bez dopadu na zákazníky. Společnost Microsoft nesdílí seznam IP adres pro adresy URL s vyrovnáváním zatížení. Měli byste použít zařízení, které podporuje filtrování podle adresy URL, nikoli podle IP adresy.

Odchozí DNS se vyžaduje ve všech časech, to znamená, že se jedná o zdroj dotazování externí služby DNS a o tom, co se vybralo řazení identity. Pokud se jedná o připojený scénář, během nasazování DVM, který je umístěn v síti řadiče pro správu základní desky, potřebuje tento odchozí přístup, ale po nasazení služby DNS se přesunou do interní komponenty, která odešle dotazy prostřednictvím veřejné virtuální IP adresy. V tuto chvíli je možné odebrat odchozí přístup DNS prostřednictvím sítě BMC, ale přístup k veřejné VIP k tomuto serveru DNS musí zůstat nebo jinak nebude ověřování úspěšné.

## <a name="next-steps"></a>Další postup

[Azure Stack požadavky PKI](azure-stack-pki-certs.md)
