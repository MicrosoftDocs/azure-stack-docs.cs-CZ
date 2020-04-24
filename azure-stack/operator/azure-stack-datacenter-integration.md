---
title: Požadavky na plánování integrace Datacenter pro integrované systémy centra Azure Stack
description: Naučte se plánovat a připravit integraci Datacenter pomocí integrovaných systémů Azure Stack hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 09/12/2019
ms.openlocfilehash: fbcca6d24f37162fa62729f38d50a6ceb0f0374c
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80638205"
---
# <a name="datacenter-integration-planning-considerations-for-azure-stack-hub-integrated-systems"></a>Požadavky na plánování integrace Datacenter pro integrované systémy centra Azure Stack

Pokud vás zajímá Azure Stack integrovaným systémem centra, měli byste porozumět hlavním hlediskům plánování nasazení a způsobu, jakým systém zapadá do vašeho datového centra. Tento článek poskytuje podrobný přehled těchto důležitých informací, které vám pomůžou s rozhodováním o infrastruktuře pro integrované systémy Azure Stack hub. Porozumění těmto hlediskům pomáhá při práci s dodavatelem hardwaru OEM při nasazování centra Azure Stack do vašeho datového centra.  

> [!NOTE]  
> Integrované systémy Azure Stack hub můžou být zakoupené jenom od autorizovaných dodavatelů hardwaru.

Aby bylo možné nasadit centrum Azure Stack, je třeba před zahájením nasazení zadat informace o plánování poskytovateli řešení, aby bylo možné proces rychle a hladce urychlit. Informace vyžadovaná pro různé informace o síti, zabezpečení a identitě jsou v mnoha důležitých rozhodnutích, které mohou vyžadovat znalosti z mnoha různých oblastí a tvůrci rozhodnutí. Budete potřebovat lidi z více týmů ve vaší organizaci, abyste měli jistotu, že máte před nasazením připravené všechny požadované informace. Může pomoct kontaktovat dodavatele hardwaru při shromažďování těchto informací, protože může mít užitečné rady.

Při vyhledávání a shromažďování požadovaných informací možná budete muset provést některé změny konfigurace před nasazením v síťovém prostředí. Tyto změny můžou zahrnovat rezervaci IP adres pro řešení centra Azure Stack a také konfiguraci směrovačů, přepínačů a bran firewall pro přípravu připojení k novým přepínačům řešení Azure Stack hub. Ujistěte se, že máte odborník na oblast předmětu, který vám bude pomáhat s vaším plánováním.

## <a name="capacity-planning-considerations"></a>Požadavky na plánování kapacity

Když vyhodnocujete řešení centra Azure Stack pro akvizici, provedete volby konfigurace hardwaru, které mají přímý vliv na celkovou kapacitu řešení Azure Stack hub. Mezi ně patří možnosti klasického využití procesoru, hustoty paměti, konfigurace úložiště a celková škála řešení (například počet serverů). Na rozdíl od tradičního řešení virtualizace neplatí jednoduché aritmetické operace s určením použitelné kapacity. Prvním důvodem je, že Azure Stack hub je navržený tak, aby se v rámci samotného řešení hostoval jako infrastruktura nebo součásti pro správu. Druhým důvodem je, že část kapacity řešení je vyhrazena pro podporu odolnosti tím, že aktualizuje software řešení způsobem, který minimalizuje narušení zatížení klientů.

[Tabulka plánovače kapacity centra Azure Stack](https://aka.ms/azstackcapacityplanner) vám pomůže se všemi způsoby plánování kapacity. První je výběr nabídky hardwaru a pokus o přizpůsobení kombinace prostředků. Druhým je definování zatížení, které Azure Stack hub má spustit k zobrazení dostupných hardwarových skladových položek, které je můžou podporovat. Tabulka je nakonec určena jako vodítko, které vám pomůže při rozhodování týkajícím se plánování a konfigurace centra Azure Stack.

Tabulka nemá za cíl sloužit jako náhrada za vaše vlastní šetření a analýzu. Společnost Microsoft neposkytuje žádné reprezentace ani záruky, vyjádřené ani odvozené, s ohledem na informace uvedené v tabulce.

## <a name="management-considerations"></a>Aspekty správy

Azure Stack hub je zapečetěný systém, ve kterém je infrastruktura uzamčená z hlediska oprávnění a sítě. Seznamy řízení přístupu (ACL) sítě se používají k blokování všech neautorizovaných příchozích přenosů a všech zbytečných komunikací mezi součástmi infrastruktury. Tento systém ztěžuje neautorizovaným uživatelům přístup k systému.

Pro každodenní správu a provoz není k infrastruktuře k dispozici žádný neomezený přístup správce. Operátoři centra Azure Stack musí spravovat systém prostřednictvím portálu pro správu nebo prostřednictvím Azure Resource Manager (prostřednictvím PowerShellu nebo REST API). Jiným nástrojům pro správu, jako je Správce technologie Hyper-V nebo Správce clusteru s podporou převzetí služeb při selhání, neexistuje žádný přístup k systému. V zájmu ochrany systému není možné instalovat software jiných výrobců (například agenty) do součástí infrastruktury centra Azure Stack. Interoperabilita s externím řízením a zabezpečením softwaru probíhá prostřednictvím PowerShellu nebo REST API.

Kontaktujte podpora Microsoftu, když potřebujete vyšší úroveň přístupu pro řešení potíží, které se nevyřešily prostřednictvím postupu pro nápravu výstrah. Prostřednictvím podpory je k dispozici metoda, která poskytuje dočasný úplný přístup správce k systému za účelem pokročilejších operací.

## <a name="identity-considerations"></a>Požadavky na identitu

### <a name="choose-identity-provider"></a>Zvolit poskytovatele identity

Musíte zvážit, který poskytovatel identity chcete použít pro Azure Stack nasazení centra, a to buď Azure AD, nebo AD FS. Po nasazení nemůžete přepínat zprostředkovatele identity bez úplného opětovného nasazení systému. Pokud účet Azure AD nevlastníte a používáte účet, který vám poskytl poskytovatel cloudového řešení, a pokud se rozhodnete přepnout poskytovatele a použít jiný účet Azure AD, budete se muset obrátit na svého poskytovatele řešení a znovu nasadit řešení za vás.

Vaše volba poskytovatele identity nemá žádný vliv na virtuální počítače (VM), systém identity, účty, které používají, nebo na to, jestli se můžou připojit k doméně služby Active Directory a tak dále. Tyto věci jsou oddělené.

Další informace o výběru poskytovatele identity najdete v [článku modely připojení integrovaných systémů Azure Stack hub](./azure-stack-connection-models.md).

### <a name="ad-fs-and-graph-integration"></a>Integrace AD FS a grafů

Pokud se rozhodnete nasadit Azure Stack hub pomocí AD FS jako poskytovatele identity, musíte integrovat instanci AD FS v Azure Stackovém centru s existující instancí AD FS prostřednictvím vztahu důvěryhodnosti federace. Tato integrace umožňuje identitám v existující doménové struktuře služby Active Directory ověřování pomocí prostředků v centru Azure Stack.

Službu Graph Service můžete také integrovat do centra Azure Stack s existující službou Active Directory. Tato integrace vám umožní spravovat Access Control na základě rolí (RBAC) v centru Azure Stack. Když je delegovaný přístup k prostředku, komponenta grafu vyhledá uživatelský účet v existující doménové struktuře služby Active Directory pomocí protokolu LDAP.

Následující diagram znázorňuje integrovaný tok provozu AD FS a grafů.<br/><br/>
![Diagram znázorňující tok provozu AD FS a grafu](media/azure-stack-datacenter-integration/ADFSIntegration.svg)

## <a name="licensing-model"></a>Model licencování

Musíte se rozhodnout, který model licencování chcete použít. Dostupné možnosti závisí na tom, jestli nasazujete Azure Stack hub připojená k Internetu:

- V případě [připojeného nasazení](azure-stack-connected-deployment.md)můžete zvolit licencování s průběžnými platbami nebo na základě kapacity. Průběžné platby vyžadují připojení k Azure za účelem hlášení využití, které se pak účtuje prostřednictvím Azure Commerce. 
- Pokud [nasazujete odpojení](azure-stack-disconnected-deployment.md) od Internetu, podporují se jenom licencování na základě kapacity. 

Další informace o licenčních modelech najdete v tématu [centra Microsoft Azure Stackho balení a ceny](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).


## <a name="naming-decisions"></a>Rozhodnutí o pojmenování

Musíte si představit, jak chcete naplánovat obor názvů centra Azure Stack, zejména název oblasti a název externí domény. Externí plně kvalifikovaný název domény (FQDN) vašeho nasazení centra Azure Stack pro veřejné koncové body je kombinací těchto dvou názvů: &lt; *region*&gt;. &lt; *plně kvalifikovaný název domény*&gt;. Například *East.Cloud.fabrikam.com*. V tomto příkladu budou portály Azure Stack hub k dispozici na následujících adresách URL:

- `https://portal.east.cloud.fabrikam.com`
- `https://adminportal.east.cloud.fabrikam.com`

> [!IMPORTANT]
> Název oblasti, který zvolíte pro nasazení centra Azure Stack, musí být jedinečný a zobrazí se na adrese portálu. 

Následující tabulka shrnuje tato rozhodnutí o pojmenovávání domén.

| Název | Popis |
| -------- | ------------- |
|Název oblasti | Název první oblasti centra Azure Stack. Tento název se používá jako součást plně kvalifikovaného názvu domény pro veřejné virtuální IP adresy (VIP), které Azure Stack hub spravuje. Obvykle by název oblasti představoval identifikátor fyzického umístění, jako je například umístění datového centra.<br><br>Název oblasti se musí skládat jenom z písmen a číslic mezi 0-9. Nejsou povoleny žádné speciální znaky `-`( `#`například, atd.).| 
| Název externí domény | Název zóny DNS (Domain Name System) pro koncové body s externími VIP adresami. Používá se v plně kvalifikovaném názvu domény pro tyto veřejné virtuální IP adresy. | 
| Privátní (interní) název domény | Název domény (a interní zóny DNS) vytvořený na Azure Stack hub pro správu infrastruktury
| | |

## <a name="certificate-requirements"></a>Požadavky na certifikáty

Pro nasazení budete muset zadat SSL (Secure Sockets Layer) (SSL) certifikátů pro veřejné koncové body. Na vysoké úrovni mají certifikáty tyto požadavky:

- Můžete použít jeden certifikát se zástupnými znaky, nebo můžete použít sadu vyhrazených certifikátů a potom použít zástupné znaky pouze pro koncové body, jako je úložiště a Key Vault.
- Certifikáty je možné vystavit prostřednictvím veřejné důvěryhodné certifikační autority (CA) nebo certifikační autority spravované zákazníkem.

Další informace o tom, jaké certifikáty PKI jsou potřeba k nasazení centra Azure Stack a jak je získat, najdete v článku [požadavky na certifikát infrastruktury veřejných klíčů centra Azure Stack](azure-stack-pki-certs.md).  

> [!IMPORTANT]
> Poskytnuté informace o certifikátu PKI by se měly používat jako obecné pokyny. Než získáte všechny certifikáty PKI pro centrum Azure Stack, pracujte s vaším hardwarovým partnerem OEM. Poskytnou podrobnější pokyny k certifikátu a požadavky.

## <a name="time-synchronization"></a>Čas synchronizace

Musíte zvolit konkrétní časový server, který se používá k synchronizaci centra Azure Stack. Synchronizace času je důležitá pro Azure Stack centra a jeho rolí infrastruktury, protože se používá ke generování lístků protokolu Kerberos. Lístky protokolu Kerberos slouží k ověřování vnitřních služeb navzájem.

Je nutné zadat IP adresu serveru pro synchronizaci času. I když většina komponent v infrastruktuře dokáže vyřešit adresu URL, některá podporují jenom IP adresy. Pokud používáte možnost odpojeného nasazení, musíte zadat časový server ve vaší podnikové síti, na kterém jste si jisti, že se můžete dostat ze sítě infrastruktury v centru Azure Stack.

## <a name="connect-azure-stack-hub-to-azure"></a>Připojení centra Azure Stack k Azure

V případě scénářů hybridního cloudu budete muset naplánovat, jak se má Azure Stack centrum připojit k Azure. Existují dva podporované metody, jak propojit virtuální sítě v Azure Stack hub k virtuálním sítím v Azure:

- **Site-to-site**: připojení k virtuální privátní síti (VPN) přes IPSec (IKE V1 a IKE v2). Tento typ připojení vyžaduje zařízení VPN nebo službu Směrování a vzdálený přístup (RRAS). Další informace o branách VPN v Azure najdete v tématu [o VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Komunikace prostřednictvím tohoto tunelového propojení je šifrovaná a zabezpečená. Šířka pásma se ale omezí maximální propustností tunelu (100-200 MB/s).

- **Odchozí NAT**: ve výchozím nastavení budou všechny virtuální počítače v centru Azure Stack připojení k externím sítím prostřednictvím odchozího překladu adres (NAT). Každá virtuální síť, která je vytvořená v centru Azure Stack, získá veřejnou IP adresu přiřazenou. Bez ohledu na to, jestli je virtuální počítač přímo přiřazený k veřejné IP adrese nebo se nachází za nástrojem pro vyrovnávání zatížení s veřejnou IP adresou, bude mít odchozí přístup prostřednictvím odchozího překladu adres (VIP) pomocí virtuální IP adresy virtuální sítě. Tato metoda funguje jenom pro komunikaci, kterou iniciuje virtuální počítač a která je určená pro externí sítě (buď Internet, nebo intranet). Nedá se použít ke komunikaci s virtuálním počítačem zvenčí.

### <a name="hybrid-connectivity-options"></a>Možnosti hybridního připojení

V případě hybridního připojení je důležité zvážit, jaký typ nasazení chcete nabízet a kde bude nasazen. Musíte vzít v úvahu, jestli potřebujete izolovat síťový provoz na tenanta a jestli máte nasazení v intranetu nebo Internetu.

- **Centrum Azure Stack pro jednoho tenanta**: nasazení Azure Stackho centra, které vypadá aspoň z perspektivy sítě, jako by to byl jeden tenant. Může existovat mnoho předplatných tenantů, ale stejně jako u libovolné intranetové služby, veškerý provoz se v rámci stejných sítí prochází. Síťový provoz z jednoho předplatného prochází přes stejné síťové připojení jako jiné předplatné a nemusí být izolovaný prostřednictvím šifrovaného tunelu.

- **Centrum Azure Stack pro více tenantů**: nasazení Azure Stackho centra, ve kterém jsou přenosy každého předplatného klienta, které jsou vázané na sítě, které jsou externí pro Azure Stack centra, izolované od ostatních síťových přenosů tenantů.

- **Nasazení v intranetu**: nasazení centra Azure Stack, které je umístěné na podnikovém intranetu, obvykle na privátním adresním prostoru IP adres a za jednou nebo více bran firewall. Veřejné IP adresy nejsou skutečně veřejné, protože není možné je směrovat přímo přes veřejný Internet.

- **Internetové nasazení**: nasazení centra Azure Stack, které je připojené k veřejnému Internetu a používá veřejné IP adresy, které se používají pro rozsah veřejných virtuálních IP adres. Nasazení může být pořád za bránou firewall, ale rozsah veřejných virtuálních IP adres je přímo dosažitelný z veřejného Internetu a Azure.

Následující tabulka shrnuje scénáře hybridního připojení s případy pro profesionály, nevýhody a použití.

| Scénář | Metoda připojení | Výhody | Nevýhody | Vhodné pro |
| -- | -- | --| -- | --|
| Centrum Azure Stack pro jednoho tenanta, nasazení v intranetu | Odchozí NAT | Lepší šířka pásma pro rychlejší přenos. Jednoduchá implementace; nevyžadují se žádné brány. | Provoz není šifrovaný; žádná izolace ani šifrování mimo zásobník. | Podniková nasazení, kde jsou všichni klienti stejně důvěryhodní.<br><br>Podniky, které mají okruh Azure ExpressRoute do Azure. |
| Centrum Azure Stack pro více tenantů, nasazení v intranetu | Site-to-site VPN | Provoz z virtuální sítě VNet do cíle je zabezpečený. | Šířka pásma je omezená tunelovým propojením VPN typu Site-to-site.<br><br>Vyžaduje bránu ve virtuální síti a v zařízení VPN v cílové síti. | Podniková nasazení, kde musí být určitý klientský provoz zabezpečený z jiných tenantů. |
| Centrum Azure Stack s jedním klientem, nasazení Internetu | Odchozí NAT | Lepší šířka pásma pro rychlejší přenos. | Provoz není šifrovaný; žádná izolace ani šifrování mimo zásobník. | Hostování scénářů, kde tenant získá vlastní nasazení centra Azure Stack a vyhrazený okruh do prostředí Azure Stack hub. Například ExpressRoute a přepínání mezi protokoly (MPLS).
| Víceklientské centrum Azure Stack, internetové nasazení | Site-to-site VPN | Provoz z virtuální sítě VNet do cíle je zabezpečený. | Šířka pásma je omezená tunelovým propojením VPN typu Site-to-site.<br><br>Vyžaduje bránu ve virtuální síti a v zařízení VPN v cílové síti. | Hostování scénářů, ve kterých poskytovatel chce nabídnout víceklientské Cloud, kde se vzájemně nevztahují jiní klienti a provoz musí být zašifrovaný.
|  |  |  |  |  |

### <a name="using-expressroute"></a>Použití ExpressRoute

Rozbočovač služby Azure Stack můžete připojit k Azure prostřednictvím [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) pro intranetový i víceklientské scénář. Budete potřebovat zřízený okruh ExpressRoute prostřednictvím [poskytovatele připojení](https://docs.microsoft.com/azure/expressroute/expressroute-locations).

Následující diagram znázorňuje ExpressRoute scénář pro jeden tenant (kde "připojení zákazníka" je okruh ExpressRoute).

![Diagram znázorňující scénář ExpressRoute pro jednoho tenanta](media/azure-stack-datacenter-integration/ExpressRouteSingleTenant.svg)

Následující diagram znázorňuje ExpressRoute scénář pro více tenantů.<br/><br/>

![Diagram znázorňující ExpressRoute scénář pro více tenantů](media/azure-stack-datacenter-integration/ExpressRouteMultiTenant.svg)

## <a name="external-monitoring"></a>Externí monitorování
Pokud chcete získat jediné zobrazení všech výstrah z nasazení a zařízení centra Azure Stack a začlenit výstrahy do stávajících pracovních postupů pro vytváření lístků v oddělení IT, můžete [Azure Stack centrum integrovat s externími řešeními monitorování datových center](azure-stack-integrate-monitor.md).

V rámci řešení centra Azure Stack je hostitel životního cyklu hardwaru počítač mimo Azure Stack centrum, které spouští nástroje pro správu poskytované dodavatelem OEM pro hardware. Můžete použít tyto nástroje nebo jiná řešení, která se přímo integrují se stávajícími řešeními monitorování ve vašem datovém centru.

Následující tabulka shrnuje seznam aktuálně dostupných možností.

| Oblast | Řešení externích monitorování |
| -- | -- |
| Software centra Azure Stack | [Sada Management Pack centra Azure Stack pro Operations Manager](https://azure.microsoft.com/blog/management-pack-for-microsoft-azure-stack-now-available/)<br>[Modul plug-in Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details)<br>Volání rozhraní API založených na REST | 
| Fyzické servery (BMC přes IPMI) | Hardware výrobce OEM Operations Manager Management Pack dodavatele<br>Řešení poskytované dodavatelem hardwaru OEM<br>Moduly plug-in Nagios dodavatele hardwaru.<br>Řešení monitorování podporované partnerem OEM (zahrnuté) | 
| Síťová zařízení (SNMP) | Zjišťování Operations Manager síťových zařízení<br>Řešení poskytované dodavatelem hardwaru OEM<br>Modul plug-in přepínače Nagios |
| Monitorování stavu předplatného tenanta | [Sada Management Pack nástroje System Center pro Windows Azure](https://www.microsoft.com/download/details.aspx?id=50013) | 
|  |  |

Vezměte na vědomí následující požadavky:

- Řešení, které použijete, musí být bez agenta. Do komponent centra Azure Stack nemůžete instalovat agenty třetích stran.
- Pokud chcete použít System Center Operations Manager, je nutné Operations Manager 2012 R2 nebo Operations Manager 2016.

## <a name="backup-and-disaster-recovery"></a>Zálohování a zotavení po havárii

Plánování zálohování a zotavení po havárii zahrnuje plánování pro základní infrastrukturu centra Azure Stack, která je hostitelem virtuálních počítačů s IaaS a PaaS služeb, a pro klientské aplikace a data. Naplánujte tyto věci samostatně.

### <a name="protect-infrastructure-components"></a>Ochrana komponent infrastruktury

Můžete zálohovat součásti infrastruktury [centra Azure Stack](azure-stack-backup-back-up-azure-stack.md) do sdílené složky SMB, kterou zadáte:

- Budete potřebovat externí sdílenou složku protokolu SMB na stávajícím souborovém serveru se systémem Windows nebo na zařízení třetí strany.
- Tuto sdílenou složku použijte pro zálohování síťových přepínačů a hostitele životního cyklu hardwaru. Dodavatel hardwaru OEM vám pomůže poskytnout pokyny k zálohování a obnovení těchto komponent, protože se jedná o externí služby Azure Stack hub. Zodpovídáte za spouštění pracovních postupů zálohování na základě doporučení dodavatele OEM.

Pokud dojde k závažné ztrátě dat, můžete použít zálohu infrastruktury k opětovnému nasazování dat nasazení, jako například: 

- Vstupy a identifikátory nasazení
- Service Accounts
- Kořenový certifikát certifikační autority
- prostředky fFederated (v odpojených nasazeních)
- Plány, nabídky, předplatné a kvóty
- Přiřazení zásad a rolí RBAC
- Key Vault tajných klíčů

### <a name="protect-tenant-apps-on-iaas-vms"></a>Ochrana tenantů aplikací na virtuálních počítačích s IaaS

Centrum Azure Stack nezálohuje klientské aplikace a data. Ochranu proti zálohování a zotavení po havárii je třeba naplánovat na cílové externí služby Azure Stack hub. Ochrana tenanta je aktivita řízená klientem. U virtuálních počítačů s IaaS můžou klienti používat technologie hostované v systému k ochraně složek souborů, dat aplikací a stavu systému. Jako podnik nebo poskytovatel služeb ale můžete chtít řešení zálohování a obnovení nabízet ve stejném datovém centru nebo externě v cloudu.

Pokud chcete zálohovat virtuální počítače se systémem Linux nebo Windows IaaS, musíte použít záložní produkty s přístupem k hostovanému operačnímu systému a chránit tak soubor, složku, stav operačního systému a data aplikací. Můžete použít Azure Backup, System Center Datacenter Protection Manager nebo podporované produkty třetích stran.

Chcete-li replikovat data do sekundárního umístění a orchestrovat převzetí služeb při selhání aplikace, můžete použít Azure Site Recovery nebo podporované produkty třetích stran. Aplikace, které podporují nativní replikaci, jako je Microsoft SQL Server, můžou taky replikovat data do jiného umístění, kde je aplikace spuštěná.

## <a name="learn-more"></a>Další informace

- Informace o případech použití, nákupu, partnerech a dodavatelích hardwarových výrobců OEM najdete na stránce produktu [Azure Stack hub](https://azure.microsoft.com/overview/azure-stack/) .
- Informace o plánu a geografické dostupnosti pro integrované systémy Azure Stack hub najdete v dokumentu White Paper: [Azure Stack hub: rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 

## <a name="next-steps"></a>Další kroky
[Modely připojení nasazení centra Azure Stack](azure-stack-connection-models.md)
