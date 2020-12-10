---
title: Robustní Síťová integrace centra Azure Stack
description: Přečtěte si o Azure Stack síťové integraci pro robustní zařízení centra Azure Stack.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 91f21fd139e2816e764f5d4a1dc3fc7602b8fecb
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939668"
---
# <a name="azure-stack-hub-ruggedized-network-integration"></a>Robustní Síťová integrace centra Azure Stack 

Toto téma se zabývá Azure Stack integrací sítě.

## <a name="border-connectivity-uplink"></a>Připojení k okraji (odchozí připojení)

Plánování integrace sítě je důležitou podmínkou pro úspěšné Azure Stack nasazení integrovaných systémů, operace a správu. Plánování připojení k okraji začíná tím, že zvolíte, jestli chcete používat dynamické směrování s protokolem BGP (Border Gateway Protocol). K tomu je potřeba přiřadit 16bitové číslo autonomního systému protokolu BGP (veřejné nebo soukromé) nebo pomocí statického směrování, kde se hraniční zařízení přiřazují statická výchozí trasa.

Přepínače rozhraní Top of rack vyžadují odchozí připojení vrstvy 3 s IP adresou typu Point-to-Point (/30 sítí) nakonfigurovanými ve fyzických rozhraních. Odchozí připojení vrstvy 2 s přepínači operačních systémem podporující operace Azure Stack se nepodporují. 

### <a name="bgp-routing"></a>Směrování protokolu BGP

Pomocí protokolu dynamického směrování, jako je BGP, zaručuje, že váš systém vždycky ví o změnách v síti a usnadňuje správu. Pro zvýšení zabezpečení je možné nastavit heslo pro vytvoření partnerského vztahu protokolu BGP mezi MANDÁTem a ohraničením.

Jak je znázorněno v následujícím diagramu, inzerování privátního prostoru IP adres v přepínači MANDÁTu je blokováno pomocí seznamu předpon. Seznam předpon odmítne inzerci privátní sítě a použije se jako mapa směrování na připojení mezi MANDÁTem a ohraničením.

Software Load Balancer (SLB) běžící v rámci řešení Azure Stack partnerských uzlů pro zařízení se systémem zprovoznění, aby mohl dynamicky inzerovat virtuální IP adresy.

Aby se zajistilo, že se přenos dat uživatelů okamžitě a transparentně obnoví z chyby, VPC nebo MLAG nakonfigurované mezi zařízeními s rozhraním systému oprávnění umožňuje použití agregace s více šasi pro hostitele a HSRP nebo nebo VRRP, která zajišťuje redundanci sítě pro sítě IP.




### <a name="static-routing"></a>Statické směrování

Statické směrování vyžaduje další konfiguraci hraničních zařízení. Před jakoukoli změnou vyžaduje více ručních zásahů a správu a také důkladnou analýzu. Problémy způsobené chybou konfigurace můžou v závislosti na provedených změnách trvat delší dobu. Tato metoda směrování se nedoporučuje, ale je podporovaná.

Aby bylo možné integrovat Azure Stack do svého síťového prostředí pomocí statického směrování, musí být připojeni všechny čtyři fyzické odkazy mezi ohraničením a zařízením.
Vysokou dostupnost nelze zaručit kvůli tomu, jak statické směrování funguje.

Hraniční zařízení musí být nakonfigurované se statickými trasami, které odkazují na každou ze čtyř IP adres P2P, které jsou nastavené mezi MANDÁTem a ohraničením provozu určeného pro libovolnou síť v Azure Stack, ale k provozu se vyžaduje jenom *externí* nebo veřejná síť VIP. Pro počáteční nasazení se vyžadují statické trasy k *řadiči pro správu základní desky* a *externí* sítě. Operátoři se můžou rozhodnout pro ponechání statických tras na hranici pro přístup k prostředkům správy, které se nacházejí na řadiči pro správu *základní desky* a v síti *infrastruktury* . Přidání statických tras pro *Přepnutí infrastruktury* a sítí *pro správu přepínačů* je volitelné.

Zařízení se systémem se konfigurují se statickou výchozí trasou, která odesílá veškerý provoz do hraničních zařízení. Jedinou výjimkou provozu na výchozí pravidlo je pro privátní prostor, který se zablokuje pomocí Access Controlho seznamu, který se používá pro připojení k hraniční síti.

Statické směrování platí jenom pro odchozí připojení mezi přepínači pro mandát a ohraničení.
V racku se používá dynamické směrování protokolu BGP, protože jde o nezbytný nástroj pro SLB a další komponenty a nedá se zakázat ani odebrat.




\* Síť řadiče pro správu základní desky je po nasazení volitelná.

\*\* Síť infrastruktury přepínače je volitelná, protože celá síť může být součástí sítě pro správu přepínače.

\*\*\* Síť pro správu přepínače je povinná a je možné ji přidat odděleně ze sítě infrastruktury přepínače.

### <a name="transparent-proxy"></a>Transparentní proxy server

Pokud vaše datacentrum vyžaduje, aby všechny přenosy používaly proxy server, musíte nakonfigurovat *transparentní proxy server* tak, aby zpracovával veškerý provoz z racku, aby ho mohl zpracovat podle zásad, což rozděluje přenos mezi zónami v síti.

Řešení Azure Stack nepodporuje normální webové proxy servery. 


Transparentní proxy server (označovaný také jako zachycení, vložený nebo vynucený proxy server) zachycuje normální komunikaci v síťové vrstvě bez nutnosti jakékoli speciální konfigurace klienta. Klienti nemusí vědět o existenci proxy serveru.



Zachycení provozu SSL není podporované a může při přístupu k koncovým bodům způsobit selhání služby. Maximální podporovaný časový limit pro komunikaci s koncovými body vyžadovanými pro identitu je 60 s se 3 opakovanými pokusy.

## <a name="dns"></a>DNS


Tato část popisuje konfiguraci DNS (Domain Name System).

### <a name="configure-conditional-dns-forwarding"></a>Konfigurace podmíněného předávání DNS

To platí jenom pro nasazení AD FS. 


Pokud chcete povolit překlad názvů pomocí existující infrastruktury DNS, nakonfigurujte podmíněné předávání.

Chcete-li přidat podmíněný předávací server, je nutné použít privilegovaný koncový bod.

Pro tento postup použijte počítač v síti datového centra, který může komunikovat s privilegovaným koncovým bodem v Azure Stack.

1.  Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce) a připojte se k IP adrese privilegovaného koncového bodu. Použijte přihlašovací údaje pro ověřování CloudAdmin.

    ```powershell
    \$cred=Get-Credential Enter-PSSession -ComputerName \<IP Address of ERCS\> -ConfigurationName PrivilegedEndpoint -Credential \$cred 
    ```

2.  Po připojení k privilegovanému koncovému bodu spusťte následující příkaz PowerShellu. Nahraďte vzorové hodnoty poskytované vaším názvem domény a IP adresami serverů DNS, které chcete použít.

    ```powershell
    Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2" 
    ```

### <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Překlad Azure Stack názvů DNS z vnějšku Azure Stack

Autoritativní servery jsou ty, které obsahují informace o externích zónách DNS, a všechny zóny vytvořené uživatelem. Integrací s těmito servery umožníte delegování zóny nebo podmíněné předávání k překladu Azure Stack názvů DNS z vnějšku Azure Stack.

### <a name="get-dns-server-external-endpoint-information"></a>Získat informace o externím koncovém bodu serveru DNS

K integraci nasazení Azure Stack s infrastrukturou DNS potřebujete tyto informace:

-   Plně kvalifikované názvy domén serveru DNS

-   IP adresy serveru DNS

Plně kvalifikované názvy domén pro Azure Stack DNS serverů mají následující formát:

\<NAMINGPREFIX\>– ns01. \<REGION\> .\<EXTERNALDOMAINNAME\>

\<NAMINGPREFIX\>– ns02. \<REGION\> .\<EXTERNALDOMAINNAME\>

Pomocí ukázkových hodnot jsou plně kvalifikované názvy domény pro servery DNS:

azs-ns01.east.cloud.fabrikam.com

azs-ns02.east.cloud.fabrikam.com

Tyto informace jsou k dispozici na portálu pro správu, ale také vytvořené na konci všech Azure Stack nasazení v souboru s názvem AzureStackStampInformation.js.
Tento soubor je umístěn ve složce C: \\ CloudDeployment \\ logs virtuálního počítače nasazení. Pokud si nejste jistí, jaké hodnoty byly použity pro nasazení Azure Stack, můžete získat hodnoty z tohoto místa.

Pokud virtuální počítač pro nasazení už není dostupný nebo není dostupný, můžete hodnoty získat připojením k privilegovanému koncovému bodu a spuštěním rutiny Get-AzureStackStampInformation PowerShellu. Další informace najdete v tématu privilegovaný koncový bod.

### <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Nastavení podmíněného přesměrování do služby Azure Stack

Nejjednodušší a nejbezpečnější způsob, jak integrovat Azure Stack s infrastrukturou DNS, je provést podmíněné přesměrování zóny ze serveru, který hostuje nadřazenou zónu. Tento přístup se doporučuje, pokud máte přímou kontrolu nad servery DNS, které hostují nadřazenou zónu pro Azure Stack externí obor názvů DNS.

Pokud nejste obeznámeni s tím, jak provést podmíněné přesměrování pomocí služby DNS, přečtěte si následující článek na webu TechNet: přiřazení podmíněného předávání pro název domény nebo dokumentaci specifickou pro vaše řešení DNS.

V případech, kdy jste určili svou externí Azure Stack zónu DNS, aby vypadala jako podřízená doména názvu vaší podnikové domény, nelze použít podmíněné přesměrování. Je třeba nakonfigurovat delegování DNS.

Příklad:

-   Název domény služby DNS pro firmy: contoso.com

-   Azure Stack název externí domény DNS: azurestack.contoso.com

### <a name="editing-dns-forwarder-ips"></a>Úprava IP adresy DNS pro přeposílání

IP adresy DNS pro přeposílání jsou nastaveny během nasazování Azure Stack. Pokud se ale IP adresy pro předávací službu musí z nějakého důvodu aktualizovat, můžete je upravit tak, že se připojíte k privilegovanému koncovému bodu a spustíte Get-AzSDnsForwarder a Set-AzSDnsForwarder [[-IPAddress] \<IPAddress[]\> ] rutin PowerShellu. Další informace najdete v tématu privilegovaný koncový bod.

### <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegování externí zóny DNS do služby Azure Stack

Aby bylo možné přeložit názvy DNS z vnějšku nasazení Azure Stack, musíte nastavit delegování DNS.

Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. Na stránce správy DNS registrátora upravte záznamy NS a nahraďte záznamy NS pro zónu těmi, které jsou v Azure Stack.

Většina registrátorů DNS vyžaduje, abyste k dokončení delegování zadali aspoň dva servery DNS.

## <a name="firewall"></a>Brána firewall

Azure Stack nastaví virtuální IP adresy (VIP) pro své role infrastruktury.
Tyto VIP adresy se přiřazují z fondu veřejných IP adres. Každá virtuální IP adresa je zabezpečená pomocí seznamu řízení přístupu (ACL) ve vrstvě softwarově definované sítě. Seznamy řízení přístupu (ACL) se používají také napříč fyzickými přepínači (tory a BMC) k dalšímu posílení zabezpečení řešení. Pro každý koncový bod v externí zóně DNS, která je zadaná v době nasazení, se vytvoří položka DNS. Například uživatelský portál má přiřazenou položku hostitel DNS na portálu. *\<region\>.\<fqdn\>*.

Následující diagram architektury znázorňuje různé vrstvy sítě a seznamy ACL:

![Diagram architektury zobrazuje různé vrstvy sítě a seznamy řízení přístupu (ACL).](media/network-deployment/network-architecture.png) 


### <a name="ports-and-urls"></a>Porty a adresy URL

Chcete-li zpřístupnit Azure Stack služby (například portály, Azure Resource Manager, DNS atd.) k dispozici pro externí sítě, musíte povolit příchozí provoz do těchto koncových bodů pro konkrétní adresy URL, porty a protokoly.

V nasazení, kde transparentní proxy odchozí připojení k tradičnímu proxy server nebo brána firewall chrání řešení, musíte povolit konkrétní porty a adresy URL pro příchozí i odchozí komunikaci. Patří mezi ně porty a adresy URL pro identitu, tržiště, opravu a aktualizace, registrace a data o využití.

### <a name="outbound-communication"></a>Odchozí komunikace

Azure Stack podporuje pouze transparentní proxy servery. V nasazení s transparentním odchozím připojením proxy k tradičním proxy server musíte při nasazování v připojeném režimu pro odchozí komunikaci v následující tabulce povolených portům a adresám URL.

Zachycení provozu SSL není podporované a může při přístupu k koncovým bodům způsobit selhání služby. Maximální podporovaný časový limit pro komunikaci s koncovými body vyžadovanými pro identitu je 60 s.

>[!NOTE] 
>Azure Stack nepodporuje použití ExpressRoute pro přístup ke službám Azure uvedeným v následující tabulce, protože ExpressRoute nemusí být schopná směrovat provoz do všech koncových bodů. 


|Účel|Cílová adresa URL|Protokol|Porty|Zdrojová síť|
|---------|---------|---------|---------|---------|
|Identita|**Azure**<br>login.windows.net<br>login.microsoftonline.com<br>graph.windows.net<br>https:\//secure.aadcdn.microsoftonline-p.com<br>www.office.com<br>ManagementServiceUri = https: \/ /Management.Core.Windows.NET<br>ARMUri = https: \/ /Management.Azure.com<br>https: \/ / \* . msftauth.NET<br>https: \/ / \* . msauth.NET<br>https: \/ / \* . msocdn.com<br>**Azure Government**<br>https: \/ /Login.microsoftonline.us/<br>https: \/ /Graph.Windows.NET/<br>**Azure China 21Vianet**<br>https: \/ /Login.chinacloudapi.cn/<br>https: \/ /Graph.chinacloudapi.cn/<br>**Azure (Německo)**<br>https: \/ /Login.microsoftonline.de/<br>https: \/ /Graph.cloudapi.de/|HTTP<br>HTTPS|80<br>443|Veřejná VIP-/27<br>Síť veřejné infrastruktury|
|Syndikace Marketplace|**Azure**<br>https:\//management.azure.com<br>https://&#42;. blob.core.windows.net<br>https://&#42;. azureedge.net<br>**Azure Government**<br>https: \/ /Management.usgovcloudapi.NET/<br>https://&#42;. blob.core.usgovcloudapi.net/<br>**Azure China 21Vianet**<br>https: \/ /Management.chinacloudapi.cn/<br>http://&#42;. blob.core.chinacloudapi.cn|HTTPS|443|Veřejná VIP-/27|
|Oprava & aktualizace|https://&#42;. azureedge.net<br>https: \/ /aka.MS/azurestackautomaticupdate|HTTPS|443|Veřejná VIP-/27|
|Registrace|**Azure**<br>https:\//management.azure.com<br>**Azure Government**<br>https: \/ /Management.usgovcloudapi.NET/<br>**Azure China 21Vianet**<br>https: \/ /Management.chinacloudapi.cn|HTTPS|443|Veřejná VIP-/27|
|Využití|**Azure**<br>https://&#42;. trafficmanager.net<br>**Azure Government**<br>https://&#42;. usgovtrafficmanager.net<br>**Azure China 21Vianet**<br>https://&#42;. trafficmanager.cn|HTTPS|443|Veřejná VIP-/27|
|Windows Defender|&#42;. wdcp.microsoft.com<br>&#42;. wdcpalt.microsoft.com<br>&#42;. wd.microsoft.com<br>&#42;. update.microsoft.com<br>&#42;. download.microsoft.com<br>https: \/ /www.Microsoft.com/pkiops/CRL<br>https: \/ /www.Microsoft.com/pkiops/certs<br>https: \/ /CRL.Microsoft.com/pki/crl/products<br>https: \/ /www.Microsoft.com/PKI/certs<br>https:\//secure.aadcdn.microsoftonline-p.com<br>|HTTPS|80<br>443|Veřejná VIP-/27<br>Síť veřejné infrastruktury|
|NTP|(IP adresa serveru NTP, který je k dispozici pro nasazení)|UDP|123|Veřejná VIP-/27|
|DNS|(IP adresa serveru DNS poskytnutá pro nasazení)|TCP<br>UDP|53|Veřejná VIP-/27|
|VOLANÝ|(Adresa URL v rámci distribučních bodů seznamu CRL na vašem certifikátu)|HTTP|80|Veřejná VIP-/27|
|LDAP|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP<br>UDP|389|Veřejná VIP-/27|
|PROTOKOL LDAP SSL|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|636|Veřejná VIP-/27|
|GC PROTOKOLU LDAP|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|3268|Veřejná VIP-/27|
|PROTOKOL SSL GC PROTOKOLU LDAP|Doménová struktura služby Active Directory poskytnutá pro integraci grafu|TCP|3269|Veřejná VIP-/27|
|AD FS|Pro integraci AD FS AD FS poskytnutý koncový bod metadat|TCP|443|Veřejná VIP-/27|
|Služba Shromažďování protokolů diagnostiky|Azure Storage zadaná adresa URL SAS objektu BLOB|HTTPS|443|Veřejná VIP-/27|
|     |     |     |     |     |
                                  
                                  
### <a name="inbound-communication"></a>Příchozí komunikace

K publikování koncových bodů Azure Stack externích sítích se vyžaduje sada virtuálních IP adres infrastruktury. Tabulka *koncový bod (VIP)* zobrazuje jednotlivé koncové body, požadovaný port a protokol. V dokumentaci k nasazení konkrétního poskytovatele prostředků najdete koncové body, které vyžadují další poskytovatele prostředků, jako je třeba poskytovatel prostředků SQL.

Interní virtuální IP adresy infrastruktury nejsou uvedené, protože nejsou nutné k publikování Azure Stack. Uživatelské virtuální IP adresy jsou dynamické a definované uživateli, a to bez řízení operátoru Azure Stack.


>[!NOTE] 
>IKEv2 VPN je řešení IPsec VPN založené na standardech, které používá port UDP 500 a 4500 a port TCP 50. Brány firewall tyto porty nikdy neotevírají, takže VPN typu IKEv2 nemusí být schopná procházet proxy a brány firewall. 


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
|Hosting | *. hostování. \<region> ..\<fqdn> | HTTPS | 443 |
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
|Brány VPN Gateway|     |     |[Podívejte se na téma Nejčastější dotazy ke službě VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vpn-faq#can-i-traverse-proxies-and-firewalls-using-point-to-site-capability).|
|     |     |     |     |

