---
title: Obecné požadavky na integraci Datacenter pro Azure Stack integrovaných systémů | Microsoft Docs
description: Přečtěte si, co můžete dělat k naplánování a přípravě na integraci Datacenter pomocí Azure Stack s více uzly.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2018
ms.openlocfilehash: 07191c95ede4f9a3ce80117bfeca9b5a02cfc0b1
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68417366"
---
# <a name="datacenter-integration-considerations-for-azure-stack-integrated-systems"></a>Požadavky na integraci Datacenter pro Azure Stack integrované systémy

Pokud vás zajímá Azure Stack integrovaný systém, měli byste porozumět hlavním hlediskům plánování nasazení a způsobu, jakým systém zapadá do vašeho datového centra. Tento článek poskytuje podrobný přehled těchto důležitých informací, které vám pomůžou s rozhodováním o infrastruktuře pro Azure Stack systému s více uzly. Porozumění těmto hlediskům pomáhá při práci s dodavatelem hardwaru OEM při nasazování Azure Stack do vašeho datacentra.  

> [!NOTE]  
> Systémy s více uzly Azure Stack můžou být zakoupené jenom od autorizovaných dodavatelů hardwaru. 

Aby bylo možné nasadit Azure Stack, je třeba před zahájením nasazení zadat informace o plánování poskytovateli řešení, aby bylo možné proces rychle a hladce urychlit. Informace vyžadovaná pro různé informace o síti, zabezpečení a identitě jsou v mnoha důležitých rozhodnutích, které mohou vyžadovat znalosti z mnoha různých oblastí a tvůrci rozhodnutí. Proto je možné, že budete muset vyžádat uživatele z více týmů ve vaší organizaci, abyste měli jistotu, že budete mít všechny požadované informace připravené ještě před zahájením nasazení. Může pomoct kontaktovat dodavatele hardwaru při shromažďování těchto informací, protože mohou být užitečné při rozhodování.

Při vyhledávání a shromažďování požadovaných informací možná budete muset provést některé změny konfigurace před nasazením v síťovém prostředí. To může zahrnovat rezervaci adresních prostorů IP adres pro řešení Azure Stack, konfiguraci směrovačů, přepínačů a bran firewall pro přípravu připojení k novým přepínačům řešení Azure Stack. Ujistěte se, že máte odborník na oblast předmětu, který vám bude pomáhat s vaším plánováním.

## <a name="capacity-planning-considerations"></a>Požadavky na plánování kapacity
Při vyhodnocování Azure Stack řešení pro akvizici je nutné provést volby konfigurace hardwaru, které mají přímý vliv na celkovou kapacitu jejich Azure Stack řešení. Mezi ně patří možnosti klasického využití procesoru, hustoty paměti, konfigurace úložiště a celková škála řešení (např. počet serverů). Na rozdíl od tradičního řešení virtualizace neplatí jednoduché aritmetické funkce pro určení použitelné kapacity. Prvním důvodem je, že Azure Stack je navržena pro hostování infrastruktury nebo součástí správy v rámci samotného řešení. Druhým důvodem je, že část kapacity řešení je vyhrazena pro podporu odolnosti. aktualizace softwaru řešení způsobem, který minimalizuje narušení zatížení klientů. 

[Tabulka Azure Stack kapacity plánovače](https://aka.ms/azstackcapacityplanner) vám pomůže s ohledem na kapacitu plánování dvěma způsoby: buď výběrem nabídky hardware, a pokusem o přizpůsobení kombinace prostředků nebo definováním zatížení, které Azure Stack má spustit pro zobrazení dostupných hardwarových skladových položek, které ho můžou podporovat. Nakonec je tabulka zamýšlená jako průvodce, který vám pomůže při rozhodování týkajícím se Azure Stack plánování a konfigurace. 

Tabulka nemá sloužit jako náhrada za vaše vlastní šetření a analýzu.  Společnost Microsoft neposkytuje žádné reprezentace ani záruky, vyjádřené ani odvozené, s ohledem na informace uvedené v tabulce.



## <a name="management-considerations"></a>Požadavky na správu
Azure Stack je zapečetěný systém, ve kterém je infrastruktura uzamčená z hlediska oprávnění a sítě. Seznamy řízení přístupu (ACL) sítě se používají k blokování všech neautorizovaných příchozích přenosů a všech zbytečných komunikací mezi součástmi infrastruktury. To ztěžuje neautorizovaným uživatelům přístup k systému.

Pro každodenní správu a provoz není k infrastruktuře k dispozici žádný neomezený přístup správce. Azure Stack operátory musí spravovat systém prostřednictvím portálu pro správu nebo prostřednictvím Azure Resource Manager (prostřednictvím PowerShellu nebo REST API). Jiným nástrojům pro správu, jako je Správce technologie Hyper-V nebo Správce clusteru s podporou převzetí služeb při selhání, neexistuje žádný přístup k systému. V zájmu ochrany systému není možné instalovat software jiných výrobců (například agenty) do součástí infrastruktury Azure Stack. Interoperabilita s externím řízením a zabezpečením softwaru probíhá prostřednictvím PowerShellu nebo REST API.

Pokud je potřeba vyšší úroveň přístupu pro řešení potíží, které se nevyřešily prostřednictvím postupu pro řešení problémů, musíte pracovat s podpora Microsoftu. Prostřednictvím podpory je k dispozici metoda, která poskytuje dočasný úplný přístup správců k systému, aby bylo možné provádět pokročilejší operace. 

## <a name="identity-considerations"></a>Požadavky na identitu

### <a name="choose-identity-provider"></a>Zvolit poskytovatele identity
Musíte zvážit, který poskytovatel identity chcete použít pro Azure Stack nasazení, a to buď Azure AD, nebo AD FS. Po nasazení nemůžete přepínat zprostředkovatele identity bez úplného opětovného nasazení systému. Pokud účet Azure AD nevlastníte a používáte účet, který vám poskytl poskytovatel cloudových služeb, a pokud se rozhodnete přepnout poskytovatele a použít jiný účet služby Azure AD, budete muset v tomto okamžiku kontaktovat poskytovatele řešení a znovu nasadit řešení f. nebo na vaše náklady.

Vaše volba poskytovatele identity nemá žádný vliv na virtuální počítače klienta, systém identit a účty, které používají, ať už se mohou připojit k doméně služby Active Directory atd. To je oddělené.

Další informace o výběru poskytovatele identity najdete v [článku Azure Stack integrovaných systémů připojení modelů](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integrace AD FS a grafů
Pokud se rozhodnete nasadit Azure Stack pomocí AD FS jako zprostředkovatele identity, musíte integrovat instanci AD FS na Azure Stack s existující AD FS instancí prostřednictvím vztahu důvěryhodnosti federace. To umožňuje identitám v existující doménové struktuře služby Active Directory ověřování pomocí prostředků v Azure Stack.

Službu Graph Service můžete také integrovat do Azure Stack s existující službou Active Directory. To vám umožní spravovat Access Control na základě rolí (RBAC) v Azure Stack. Když je delegovaný přístup k prostředku, komponenta grafu vyhledá uživatelský účet v existující doménové struktuře služby Active Directory pomocí protokolu LDAP.

Následující diagram znázorňuje integrovaný tok provozu AD FS a grafů.
![Diagram znázorňující tok provozu AD FS a grafu](media/azure-stack-datacenter-integration/ADFSIntegration.PNG)

## <a name="licensing-model"></a>Model licencování
Musíte se rozhodnout, který model licencování chcete použít. Dostupné možnosti závisí na tom, jestli nasazujete Azure Stack připojená k Internetu:
- V případě [připojeného nasazení](azure-stack-connected-deployment.md)můžete zvolit licencování s průběžnými platbami nebo na základě kapacity. Průběžné platby vyžadují připojení k Azure za účelem hlášení využití, které se pak účtuje prostřednictvím Azure Commerce. 
- Pokud nasazujete [odpojení](azure-stack-disconnected-deployment.md) od Internetu, podporují se jenom licencování na základě kapacity. 

Další informace o licenčních modelech najdete v tématu [Microsoft Azure Stack balení a ceny](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Rozhodnutí o pojmenování

Musíte si představit, jak chcete naplánovat Azure Stack obor názvů, zejména název oblasti a název externí domény. Externí plně kvalifikovaný název domény (FQDN) vašeho nasazení Azure Stack pro veřejné koncové body je kombinací těchto dvou &lt;názvů: *region*&gt;.&lt; *plně kvalifikovaný název domény* &gt;. Například *East.Cloud.fabrikam.com*. V tomto příkladu budou Azure Stack portály k dispozici na následujících adresách URL:

- https://portal.east.cloud.fabrikam.com
- https://adminportal.east.cloud.fabrikam.com

> [!IMPORTANT]
> Název oblasti, který zvolíte pro nasazení Azure Stack, musí být jedinečný a zobrazí se v adresách portálu. 

Následující tabulka shrnuje tato rozhodnutí o pojmenovávání domén.

| Name | Popis | 
| -------- | ------------- | 
|Název oblasti | Název prvního Azure Stack oblasti. Tento název se používá jako součást plně kvalifikovaného názvu domény pro veřejné virtuální IP adresy (VIP), které Azure Stack spravuje. Obvykle by název oblasti představoval identifikátor fyzického umístění, jako je například umístění datového centra.<br><br>Název oblasti se musí skládat jenom z písmen a číslic mezi 0-9. Nejsou povoleny žádné speciální znaky, například "-" nebo "#" atd.| 
| Název externí domény | Název zóny DNS (Domain Name System) pro koncové body s externími VIP adresami. Používá se v plně kvalifikovaném názvu domény pro tyto veřejné virtuální IP adresy. | 
| Privátní (interní) název domény | Název domény (a interní zóny DNS) vytvořený v Azure Stack pro správu infrastruktury. 
| | |

## <a name="certificate-requirements"></a>Požadavky na certifikát

Pro nasazení budete muset zadat SSL (Secure Sockets Layer) (SSL) certifikátů pro veřejné koncové body. Na vysoké úrovni mají certifikáty tyto požadavky:

- Můžete použít jeden certifikát se zástupnými znaky, nebo můžete použít sadu vyhrazených certifikátů a použít zástupné znaky pouze pro koncové body, jako je například úložiště a Key Vault.
- Certifikáty je možné vystavit prostřednictvím veřejné důvěryhodné certifikační autority (CA) nebo certifikační autority spravované zákazníkem.

Další informace o tom, jaké certifikáty PKI jsou potřeba k nasazení Azure Stack a jak je získat, najdete v článku [Azure Stack požadavky na certifikát infrastruktury veřejných klíčů](azure-stack-pki-certs.md).  


> [!IMPORTANT]
> Poskytnuté informace o certifikátu PKI by se měly používat jako obecné pokyny. Před získáním jakýchkoli certifikátů PKI pro Azure Stack Pracujte s vaším hardwarovým partnerem OEM. Budou poskytovat podrobnější pokyny k certifikátu a požadavky.


## <a name="time-synchronization"></a>Synchronizace času
Je nutné zvolit konkrétní časový server, který se používá k synchronizaci Azure Stack.  Synchronizace času je důležitá pro Azure Stack a její role infrastruktury, protože se používají ke generování lístků protokolu Kerberos, které se používají k ověřování vnitřních služeb.

Musíte zadat IP adresu serveru pro synchronizaci času, i když většina komponent v infrastruktuře může adresu URL vyřešit, některé můžou podporovat jenom IP adresy. Pokud používáte možnost odpojeného nasazení, musíte zadat časový server ve vaší podnikové síti, ke kterému jste si jisti, že se můžete dostat ze sítě infrastruktury v Azure Stack.

## <a name="connect-azure-stack-to-azure"></a>Připojení Azure Stack k Azure

V případě scénářů hybridního cloudu budete muset naplánovat, jak se má Azure Stack připojit k Azure. Existují dvě podporované metody připojení virtuálních sítí ve Azure Stack k virtuálním sítím v Azure: 
- **Site-to-site**. Připojení virtuální privátní sítě (VPN) přes IPsec (IKE V1 a IKE v2). Tento typ připojení vyžaduje zařízení VPN nebo službu Směrování a vzdálený přístup (RRAS). Další informace o branách VPN v Azure najdete v tématu [o VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Komunikace přes toto tunelové propojení je šifrovaná a je zabezpečená. Šířka pásma se ale omezí maximální propustností tunelu (100-200 MB/s).
- **Odchozí překlad adres (NAT)** . Ve výchozím nastavení budou mít všechny virtuální počítače v Azure Stack připojení k externím sítím prostřednictvím odchozího překladu adres (NAT). Každá virtuální síť vytvořená v Azure Stack získá přiřazenou veřejnou IP adresu. Bez ohledu na to, jestli je virtuální počítač přímo přiřazený k veřejné IP adrese nebo je za nástrojem pro vyrovnávání zatížení s veřejnou IP adresou, bude mít odchozí přístup prostřednictvím odchozího překladu adres (VIP) pomocí virtuální IP adresy virtuální sítě. Tato funkce funguje jenom pro komunikaci, kterou iniciuje virtuální počítač a která je určená pro externí sítě (buď Internet, nebo intranet). Nedá se použít ke komunikaci s virtuálním počítačem zvenčí.

### <a name="hybrid-connectivity-options"></a>Možnosti hybridního připojení

V případě hybridního připojení je důležité zvážit, jaký typ nasazení chcete nabízet a kde bude nasazen. Musíte vzít v úvahu, jestli potřebujete izolovat síťový provoz na tenanta a jestli máte nasazení v intranetu nebo Internetu.

- **Azure Stack jednoho klienta**. Nasazení Azure Stack, které vypadá aspoň z hlediska sítě, jako by to bylo jeden tenant. Může existovat mnoho předplatných tenantů, ale stejně jako u libovolné intranetové služby, veškerý provoz se v rámci stejných sítí prochází. Síťový provoz z jednoho předplatného prochází přes stejné síťové připojení jako jiné předplatné a nemusí být izolovaný prostřednictvím šifrovaného tunelu.

- **Azure Stack více tenantů**. Nasazení Azure Stack, kde každý z nich vázaný provoz pro sítě, které jsou v sítích, které jsou externě Azure Stack musí být izolované od síťového provozu ostatních tenantů.
 
- **Nasazení**v intranetu. Nasazení Azure Stack, které je umístěné na podnikovém intranetu, obvykle na privátním adresním prostoru IP adres a za jednou nebo více bran firewall. Veřejné IP adresy nejsou skutečně veřejné, protože je nelze směrovat přímo přes veřejný Internet.

- **Internetové nasazení**. Nasazení Azure Stack připojené k veřejnému Internetu a používá veřejné IP adresy směrovatelný pro rozsah veřejných virtuálních IP adres. Nasazení může být pořád za bránou firewall, ale rozsah veřejných virtuálních IP adres je přímo dosažitelný z veřejného Internetu a Azure.
 
Následující tabulka shrnuje scénáře hybridního připojení s případy pro profesionály, nevýhody a použití.

| Scénář | Metoda připojení | V oblasti IT | Nevýhody | Vhodné pro |
| -- | -- | --| -- | --|
| Azure Stack jednoho tenanta, nasazení v intranetu | Odchozí NAT | Lepší šířka pásma pro rychlejší přenos. Jednoduchá implementace; nevyžadují se žádné brány. | Provoz není šifrovaný; žádná izolace ani šifrování mimo zásobník. | Podniková nasazení, kde jsou všichni klienti stejně důvěryhodní.<br><br>Podniky, které mají okruh Azure ExpressRoute do Azure. |
| Víceklientské Azure Stack, intranetové nasazení | Síť VPN typu Site-to-site | Provoz z virtuální sítě VNet do cíle je zabezpečený. | Šířka pásma je omezená tunelovým propojením VPN typu Site-to-site.<br><br>Vyžaduje bránu ve virtuální síti a v zařízení VPN v cílové síti. | Podniková nasazení, kde musí být určitý klientský provoz zabezpečený z jiných tenantů. |
| Jeden tenant Azure Stack, internetové nasazení | Odchozí NAT | Lepší šířka pásma pro rychlejší přenos. | Provoz není šifrovaný; žádná izolace ani šifrování mimo zásobník. | Hostování scénářů, kde tenant získá vlastní nasazení Azure Stack a vyhrazený okruh do Azure Stack prostředí. Například ExpressRoute a přepínání mezi protokoly (MPLS).
| Víceklientské Azure Stack, internetové nasazení | Síť VPN typu Site-to-site | Provoz z virtuální sítě VNet do cíle je zabezpečený. | Šířka pásma je omezená tunelovým propojením VPN typu Site-to-site.<br><br>Vyžaduje bránu ve virtuální síti a v zařízení VPN v cílové síti. | Hostování scénářů, ve kterých poskytovatel chce nabídnout víceklientské Cloud, kde se vzájemně nevztahují jiní klienti a provoz musí být zašifrovaný.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Použití ExpressRoute

Azure Stack můžete k Azure připojit prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pro intranetový i víceklientské scénář. Budete potřebovat zřízený okruh ExpressRoute prostřednictvím [poskytovatele připojení](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Následující diagram znázorňuje ExpressRoute scénář pro jeden tenant (kde "připojení zákazníka" je okruh ExpressRoute).

![Diagram znázorňující scénář ExpressRoute pro jednoho tenanta](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.PNG)

Následující diagram znázorňuje ExpressRoute scénář pro více tenantů.

![Diagram znázorňující ExpressRoute scénář pro více tenantů](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.PNG)

## <a name="external-monitoring"></a>Externí monitorování
Pokud chcete získat jedno zobrazení všech výstrah z Azure Stack nasazení a zařízení a integrovat výstrahy do stávajících pracovních postupů správa IT služeb pro vytváření lístků, můžete [Azure Stack integrovat s externími řešeními monitorování Datacenter](azure-stack-integrate-monitor.md).

V rámci řešení Azure Stack je hostitelem životního cyklu hardwarový počítač mimo Azure Stack, který spouští nástroje pro správu poskytované dodavatelem OEM pro hardware. Můžete použít tyto nástroje nebo jiná řešení, která se přímo integrují se stávajícími řešeními monitorování ve vašem datovém centru.

Následující tabulka shrnuje seznam aktuálně dostupných možností.

| Oblast | Řešení externích monitorování |
| -- | -- |
| Azure Stack software | [Azure Stack Management Pack pro Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Modul plug-in Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Volání rozhraní API založených na REST | 
| Fyzické servery (BMC přes IPMI) | Hardware výrobce OEM Operations Manager Management Pack dodavatele<br>Řešení poskytované dodavatelem hardwaru OEM<br>Moduly plug-in Nagios dodavatele hardwaru.<br>Řešení monitorování podporované partnerem OEM (zahrnuté) | 
| Síťová zařízení (SNMP) | Zjišťování Operations Manager síťových zařízení<br>Řešení poskytované dodavatelem hardwaru OEM<br>Modul plug-in přepínače Nagios |
| Monitorování stavu předplatného tenanta | [Sada Management Pack nástroje System Center pro Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  | 

Vezměte na vědomí následující požadavky:
- Řešení, které použijete, musí být bez agenta. Do komponent Azure Stack nemůžete instalovat agenty třetích stran. 
- Pokud chcete použít System Center Operations Manager, je nutné Operations Manager 2012 R2 nebo Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

Plánování zálohování a zotavení po havárii zahrnuje plánování základní infrastruktury Azure Stack, která je hostitelem virtuálních počítačů s IaaS a PaaS služeb, a pro klientské aplikace a data. Je nutné, abyste je naplánovali samostatně.

### <a name="protect-infrastructure-components"></a>Chránit součásti infrastruktury

Do sdílené složky SMB, kterou zadáte, můžete [zálohovat Azure Stack](azure-stack-backup-back-up-azure-stack.md) součásti infrastruktury:

- Budete potřebovat externí sdílenou složku protokolu SMB na stávajícím souborovém serveru se systémem Windows nebo na zařízení třetí strany.
- Tuto sdílenou složku byste měli použít pro zálohování síťových přepínačů a hostitele životního cyklu hardwaru. Dodavatel hardwaru OEM vám pomůže poskytnout pokyny k zálohování a obnovení těchto komponent, protože se jedná o externí Azure Stack. Zodpovídáte za spouštění pracovních postupů zálohování na základě doporučení dodavatele OEM.

Pokud dojde k závažné ztrátě dat, můžete použít zálohu infrastruktury k opětovnému nasazování dat nasazení, jako jsou vstupy a identifikátory nasazení, účty služeb, kořenový certifikát certifikační autority, federované prostředky (v odpojených nasazeních), plány, nabídky, předplatná, kvóty, přiřazení zásad a rolí RBAC a Key Vault tajných klíčů.
 
### <a name="protect-tenant-applications-on-iaas-virtual-machines"></a>Ochrana klientských aplikací ve virtuálních počítačích s IaaS

Azure Stack nezálohují klientské aplikace a data. Je třeba naplánovat ochranu proti zálohování a zotavení po havárii do cílového externího Azure Stack. Ochrana tenanta je aktivita řízená klientem. U virtuálních počítačů s IaaS můžou klienti používat technologie hostované v systému k ochraně složek souborů, dat aplikací a stavu systému. Jako podnik nebo poskytovatel služeb ale můžete chtít řešení zálohování a obnovení nabízet ve stejném datovém centru nebo externě v cloudu.

Chcete-li zálohovat virtuální počítače se systémem Linux nebo IaaS, je nutné použít záložní produkty s přístupem k hostovanému operačnímu systému a chránit tak soubor, složku, stav operačního systému a data aplikací. Můžete použít Azure Backup, System Center Data Center Protection Manager nebo podporovaných produktů třetích stran.

Chcete-li replikovat data do sekundárního umístění a orchestrovat převzetí služeb při selhání aplikace, můžete použít Azure Site Recovery nebo podporované produkty třetích stran. Aplikace, které podporují nativní replikaci, jako je Microsoft SQL Server, mohou také replikovat data do jiného umístění, kde je aplikace spuštěná.

## <a name="learn-more"></a>Víc se uč

- Informace o případech použití, nákupu, partnerech a dodavatelích hardwaru OEM naleznete na stránce [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktu.
- Informace o plánu a geografické dostupnosti pro Azure Stack integrovaných systémech najdete v dokumentu White Paper: [Azure Stack: Rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Další postup
[Modely připojení k nasazení služby Azure Stack](azure-stack-connection-models.md)
