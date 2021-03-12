---
title: Publikování služby Azure Stack hub ve vašem datovém centru
description: Naučte se publikovat Azure Stack služby centra ve svém datovém centru.
author: patricka
ms.topic: article
ms.date: 03/02/2021
ms.author: patricka
ms.reviewer: wamota
ms.lastreviewed: 09/24/2020
ms.openlocfilehash: 928e3fe6fbaa5c41d52d89617796ac726afcf2d6
ms.sourcegitcommit: 4f1d22747c02ae280609174496933fca8c04a6cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102606393"
---
# <a name="publish-azure-stack-hub-services-in-your-datacenter"></a>Publikování služby Azure Stack hub ve vašem datovém centru

Centrum Azure Stack nastaví virtuální IP adresy (VIP) pro své role infrastruktury. Tyto VIP adresy se přiřazují z fondu veřejných IP adres. Každá virtuální IP adresa je zabezpečená pomocí seznamu řízení přístupu (ACL) ve vrstvě softwarově definované sítě. Seznamy řízení přístupu (ACL) se používají také napříč fyzickými přepínači (tory a BMC) k dalšímu posílení zabezpečení řešení. Pro každý koncový bod v externí zóně DNS, která je zadaná v době nasazení, se vytvoří položka DNS. Například uživatelský portál má přiřazenou položku hostitel DNS na portálu. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*.

Následující diagram architektury znázorňuje různé vrstvy sítě a seznamy ACL:

![Diagram znázorňující různé vrstvy sítě a seznamy ACL](media/azure-stack-integrate-endpoints/integrate-endpoints-01.svg)

## <a name="ports-and-urls"></a>Porty a adresy URL

Chcete-li zpřístupnit služby Azure Stack hub (například portály, Azure Resource Manager, DNS atd.) k dispozici pro externí sítě, musíte povolit příchozí provoz do těchto koncových bodů pro konkrétní adresy URL, porty a protokoly.

V nasazení, kde transparentní proxy odchozí připojení k tradičnímu proxy server nebo brána firewall chrání řešení, musíte povolit konkrétní porty a adresy URL pro [příchozí](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound) i [odchozí](azure-stack-integrate-endpoints.md#ports-and-urls-outbound) komunikaci. Patří mezi ně porty a adresy URL pro identitu, tržiště, opravu a aktualizace, registrace a data o využití.

Zachycení provozu SSL není [podporované](azure-stack-firewall.md#ssl-interception) a může při přístupu k koncovým bodům způsobit selhání služby. 

## <a name="ports-and-protocols-inbound"></a>Porty a protokoly (příchozí)

K publikování koncových bodů centra Azure Stack do externích sítí se vyžaduje sada virtuálních IP adres infrastruktury. Tabulka *koncový bod (VIP)* zobrazuje jednotlivé koncové body, požadovaný port a protokol. V dokumentaci k nasazení konkrétního poskytovatele prostředků najdete koncové body, které vyžadují další poskytovatele prostředků, jako je třeba poskytovatel prostředků SQL.

Interní virtuální IP adresy infrastruktury nejsou uvedené, protože nejsou nutné k publikování centra Azure Stack. Uživatelské virtuální IP adresy jsou dynamické a definované uživateli, bez řízení pomocí operátoru centra Azure Stack.

> [!Note]  
> IKEv2 VPN je řešení IPsec VPN založené na standardech, které používá port UDP 500 a 4500 a port TCP 50. Brány firewall tyto porty nikdy neotevírají, takže VPN typu IKEv2 nemusí být schopná procházet proxy a brány firewall.

Po přidání [hostitele rozšíření](azure-stack-extension-host-prepare.md)nejsou porty v rozsahu 12495-30015 vyžadovány.

|Koncový bod (VIP)|Záznam o záznamu hostitele DNS|Protokol|Porty|
|---------|---------|---------|---------|
|AD FS|Službou. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|443|
|Portál (správce)|Adminportal. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|443|
|Adminhosting | *. adminhosting. \<region> .\<fqdn> | HTTPS | 443 |
|Azure Resource Manager (správce)|Adminmanagement. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|443|
|Portál (uživatel)|Bran. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|443|
|Azure Resource Manager (uživatel)|Správu. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|443|
|Graph|Zapisovací. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|443|
|Seznam odvolaných certifikátů|Seznam CRL.*&lt;> oblasti. &lt; plně kvalifikovaný název domény>*|HTTP|80|
|DNS|&#42;. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|TCP & UDP|53|
|Hostování | *. hostování. \<region> ..\<fqdn> | HTTPS | 443 |
|Key Vault (uživatel)|&#42;. trezor. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|443|
|Key Vault (správce)|&#42;. adminvault. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|443|
|Fronta úložiště|&#42;. Queue. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTP<br>HTTPS|80<br>443|
|Tabulka úložiště|&#42;. Table. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTP<br>HTTPS|80<br>443|
|Storage Blob|&#42;. blob. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTP<br>HTTPS|80<br>443|
|Poskytovatel prostředků SQL|sqladapter.dbadapter. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|44300-44304|
|Poskytovatel prostředků MySQL|mysqladapter.dbadapter. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|HTTPS|44300-44304|
|App Service|&#42;. AppService. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;. SCM. AppService. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|TCP|443 (HTTPS)|
|  |API. AppService. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |FTP. AppService. *&lt;> &lt; oblasti plně kvalifikovaný název domény>*|TCP, UDP|21, 1021, 10001-10100 (FTP)<br>990 (FTPS)|
|Brány VPN Gateway|     |     |[Podívejte se na téma Nejčastější dotazy ke službě VPN Gateway](/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

## <a name="ports-and-urls-outbound"></a>Porty a adresy URL (odchozí)

Centrum Azure Stack podporuje jenom transparentní proxy servery. V nasazení pomocí transparentního odchozího připojení proxy k tradičnímu proxy server musíte pro odchozí komunikaci použít porty a adresy URL v následující tabulce. Další informace o konfiguraci transparentních proxy serverů najdete v tématu [transparentní proxy server pro Azure Stack hub](azure-stack-transparent-proxy.md).

Zachycení provozu SSL není [podporované](azure-stack-firewall.md#ssl-interception) a může při přístupu k koncovým bodům způsobit selhání služby. Maximální podporovaný časový limit pro komunikaci s koncovými body vyžadovanými pro identitu je 60 s.

> [!Note]  
> Centrum Azure Stack nepodporuje použití ExpressRoute pro přístup ke službám Azure uvedeným v následující tabulce, protože ExpressRoute nemusí být schopná směrovat provoz do všech koncových bodů. 

|Účel|Cílová adresa URL|Protokol/porty|Zdrojová síť|Požadavek|
|---------|---------|---------|---------|---------|
|**Identita**<br>Umožňuje Azure Stack rozbočovači připojit se k Azure Active Directory pro ověřování služby & uživatele.|**Azure**<br>`login.windows.net`<br>`login.microsoftonline.com`<br>`graph.windows.net`<br>`https://secure.aadcdn.microsoftonline-p.com`<br>`www.office.com`<br>ManagementServiceUri = `https://management.core.windows.net`<br>ARMUri = `https://management.azure.com`<br>`https://*.msftauth.net`<br>`https://*.msauth.net`<br>`https://*.msocdn.com`<br>**Azure Government**<br>`https://login.microsoftonline.us/`<br>`https://graph.windows.net/`<br>**Azure (Čína) 21Vianet**<br>`https://login.chinacloudapi.cn/`<br>`https://graph.chinacloudapi.cn/`<br>**Azure (Německo)**<br>`https://login.microsoftonline.de/`<br>`https://graph.cloudapi.de/`|HTTP 80,<br>HTTPS 443|Veřejná VIP-/27<br>Síť veřejné infrastruktury|Povinné pro připojené nasazení.|
|**Syndikace Marketplace**<br>Umožňuje stahovat položky do centra Azure Stack z webu Marketplace a zpřístupnit je všem uživatelům pomocí prostředí Azure Stack hub.|**Azure**<br>`https://management.azure.com`<br>`https://*.blob.core.windows.net`<br>`https://*.azureedge.net`<br>**Azure Government**<br>`https://management.usgovcloudapi.net/`<br>`https://*.blob.core.usgovcloudapi.net/`<br>**Azure (Čína) 21Vianet**<br>`https://management.chinacloudapi.cn/`<br>`http://*.blob.core.chinacloudapi.cn`|HTTPS 443|Veřejná VIP-/27|Nevyžadují se. Pomocí [pokynů k odpojenému scénáři](azure-stack-download-azure-marketplace-item.md) nahrajte obrázky do centra Azure Stack.|
|**Oprava & aktualizace**<br>Když jste připojeni k aktualizacím koncových bodů, zobrazují se aktualizace softwaru centra Azure Stack a opravy hotfix, které jsou k dispozici ke stažení.|`https://*.azureedge.net`<br>`https://aka.ms/azurestackautomaticupdate`|HTTPS 443|Veřejná VIP-/27|Nevyžadují se. K ručnímu stažení a přípravě aktualizace použijte [pokyny pro připojení k odpojenému nasazení](azure-stack-update-prepare-package.md) .|
|**Evidenc**<br>Umožňuje registraci centra Azure Stack s využitím Azure ke stažení položek Azure Marketplace a nastavení vykazování obchodních dat zpět společnosti Microsoft. |**Azure**<br>`https://management.azure.com`<br>**Azure Government**<br>`https://management.usgovcloudapi.net/`<br>**Azure (Čína) 21Vianet**<br>`https://management.chinacloudapi.cn`|HTTPS 443|Veřejná VIP-/27|Nevyžadují se. K [registraci offline](azure-stack-registration.md)můžete použít odpojený scénář.|
|**Použití**<br>Umožňuje operátorům centra Azure Stack nakonfigurovat svoji instanci centra Azure Stack, aby nahlásila data o využití do Azure.|**Azure**<br>`https://*.trafficmanager.net`<br>**Azure Government**<br>`https://*.usgovtrafficmanager.net`<br>**Azure (Čína) 21Vianet**<br>`https://*.trafficmanager.cn`|HTTPS 443|Veřejná VIP-/27|Vyžaduje se pro licenční model založený na spotřebě centra Azure Stack.|
|**Windows Defender**<br>Umožňuje poskytovateli prostředků aktualizace stahovat definice antimalwaru a aktualizace modulu víckrát za den.|`*.wdcp.microsoft.com`<br>`*.wdcpalt.microsoft.com`<br>`*.wd.microsoft.com`<br>`*.update.microsoft.com`<br>`*.download.microsoft.com`<br><br>`https://secure.aadcdn.microsoftonline-p.com`<br>|HTTPS 80, 443|Veřejná VIP-/27<br>Síť veřejné infrastruktury|Nevyžadují se. [K aktualizaci souborů signatur antivirové ochrany můžete použít odpojený scénář](azure-stack-security-av.md#disconnected-scenario).|
|**NTP**<br>Umožňuje rozbočovači Azure Stack připojení k časovým serverům.|(IP adresa serveru NTP, který je k dispozici pro nasazení)|UDP 123|Veřejná VIP-/27|Vyžadováno|
|**DNS**<br>Umožňuje službě Azure Stack hub připojit se k serveru DNS pro přeposílání.|(IP adresa serveru DNS poskytnutá pro nasazení)|TCP & UDP 53|Veřejná VIP-/27|Vyžadováno|
|**SYSLOG**<br>Umožňuje Azure Stack centru odesílat zprávy syslog pro účely monitorování nebo zabezpečení.|(IP adresa serveru SYSLOG poskytnutého pro nasazení)|TCP 6514,<br>UDP 514|Veřejná VIP-/27|Volitelné|
|**VOLANÝ**<br>Umožňuje Azure Stack centru ověřování certifikátů a kontrolu odvolaných certifikátů.|(Adresa URL v rámci distribučních bodů seznamu CRL na vašem certifikátu)<br>`http://crl.microsoft.com/pki/crl/products`<br>`http://mscrl.microsoft.com/pki/mscorp`<br>`http://www.microsoft.com/pki/certs`<br>`http://www.microsoft.com/pki/mscorp`<br>`http://www.microsoft.com/pkiops/crl`<br>`http://www.microsoft.com/pkiops/certs`<br>|HTTP 80|Veřejná VIP-/27|Nevyžadují se. Osvědčený postup zabezpečení se důrazně doporučuje.|
|**LDAP**<br>Umožňuje službě Azure Stack hub komunikovat s místním adresářem Microsoft Active Directory.|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP & UDP 389|Veřejná VIP-/27|Požadováno při nasazení Azure Stack hub pomocí AD FS.|
|**PROTOKOL LDAP SSL**<br>Umožňuje, aby centrum Azure Stack komunikovalo s místní službou Microsoft Active Directory jako šifrované.|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP 636|Veřejná VIP-/27|Požadováno při nasazení Azure Stack hub pomocí AD FS.|
|**GC PROTOKOLU LDAP**<br>Umožňuje službě Azure Stack hub komunikovat s aktivními servery globálního katalogu Microsoft.|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP 3268|Veřejná VIP-/27|Požadováno při nasazení Azure Stack hub pomocí AD FS.|
|**PROTOKOL SSL GC PROTOKOLU LDAP**<br>Umožňuje službě Azure Stack hub komunikovat se servery globálního katalogu služby Microsoft Active Directory.|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP 3269|Veřejná VIP-/27|Požadováno při nasazení Azure Stack hub pomocí AD FS.|
|**AD FS**<br>Umožňuje službě Azure Stack hub komunikovat s místními AD FS.|Pro integraci AD FS AD FS poskytnutý koncový bod metadat|TCP 443|Veřejná VIP-/27|Nepovinný parametr. Vztah důvěryhodnosti zprostředkovatele deklarací AD FS lze vytvořit pomocí [souboru metadat](azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file).|
|**Shromažďování protokolů diagnostiky**<br>Umožňuje, aby centrum Azure Stack odesílalo protokoly buď proaktivně, nebo ručně prostřednictvím operátoru podpory společnosti Microsoft.|`https://*.blob.core.windows.net`<br>`https://azsdiagprdlocalwestus02.blob.core.windows.net`<br>`https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com`<br>`https://azsdiagprdwestusfrontend.westus.cloudapp.azure.com` | HTTPS 443 | Veřejná VIP-/27 |Nevyžadují se. Protokoly můžete [ukládat lokálně](diagnostic-log-collection.md#save-logs-locally).|

Odchozí adresy URL využívají vyrovnávání zatížení pomocí Azure Traffic Manageru a poskytují nejlepší možné připojení na základě geografického umístění. V případě adres URL s vyrovnáváním zatížení může Microsoft aktualizovat koncové body back-endu, aniž by to ovlivnilo zákazníky Microsoft nesdílí seznam IP adres pro adresy URL s vyrovnáváním zatížení. Použijte zařízení, které podporuje filtrování podle adresy URL, nikoli podle IP adresy.

Odchozí DNS se vyžaduje ve všech případech. To znamená, že se jedná o zdroj dotazování externí služby DNS a o tom, jaký typ integrace identity byl vybrán. V průběhu nasazení v připojeném scénáři potřebuje DVM, který je umístěn v síti řadiče pro správu základní desky, odchozí přístup. Ale po nasazení se služba DNS přesune do interní součásti, která odešle dotazy prostřednictvím veřejné virtuální IP adresy. V tuto chvíli je možné odebrat odchozí přístup k DNS prostřednictvím sítě řadiče pro správu základní desky, ale přístup k tomuto serveru DNS pomocí veřejné VIP musí zůstat nebo jinak se ověřování nezdaří.

## <a name="next-steps"></a>Další kroky

[Požadavky na infrastrukturu veřejných klíčů centra Azure Stack](azure-stack-pki-certs.md)
