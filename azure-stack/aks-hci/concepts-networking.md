---
title: Koncepty – sítě v Azure Kubernetes Services (AKS) na Azure Stack HCL
description: Přečtěte si o sítích ve službě Azure Kubernetes Service (AKS) na Azure Stack HCL, včetně statických IP adres a sítí DHCP a nástrojů pro vyrovnávání zatížení.
author: abha
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: abha
ms.custom: fasttrack-edit
ms.openlocfilehash: a8e96783be3228aae525027855c1498b0269bd57
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873660"
---
# <a name="network-concepts-for-deploying-azure-kubernetes-service-aks-on-azure-stack-hci"></a>Koncepty sítě pro nasazení služby Azure Kubernetes Service (AKS) na Azure Stack HCL

V rámci přístupu mikroslužeb založených na kontejneru ke vývoji aplikací musí komponenty aplikace spolupracovat na zpracování svých úkolů. Kubernetes poskytuje různé prostředky, které umožňují komunikaci této aplikace. Můžete se připojit k aplikacím a vystavit je interně nebo externě. K vytváření aplikací s vysokou dostupností můžete vyrovnávat zatížení aplikací. 

V tomto článku se seznámíte se základními koncepcemi, které poskytují sítě vašim aplikacím v AKS na Azure Stack HCI. Pak vás provede různými modely nasazení a příklady nastavení nejlepší síťové infrastruktury pro místní nasazení AKS.

## <a name="kubernetes-on-azure-stack-hci-basics"></a>Kubernetes na Azure Stack základy HCI

Pro umožnění přístupu k aplikacím nebo pro komunikaci mezi komponentami aplikace Kubernetes poskytuje abstrakci vrstvu pro virtuální sítě. Uzly Kubernetes jsou připojené k virtuální síti a můžou poskytovat příchozí a odchozí připojení pro lusky. Komponenta *Kube-proxy* se spouští na všech uzlech, aby poskytovala tyto síťové funkce.

V Kubernetes *služby* logicky seskupují, aby umožňovaly přímý přístup prostřednictvím IP adresy nebo názvu DNS a na určitém portu. Provoz můžete také distribuovat pomocí *Nástroje pro vyrovnávání zatížení*. 

Platforma Azure Stack HCI také pomáhá zjednodušit virtuální sítě pro AKS v Azure Stackch clusterech HCI. Když vytvoříte cluster na AKS v Azure Stack HCI, `HAProxy` vytvoří se a nakonfiguruje se základní prostředek nástroje pro vyrovnávání zatížení. Při sestavování aplikací nad clustery Kubernetes se IP adresy konfigurují pro vaše služby Kubernetes.

## <a name="aks-on-azure-stack-hci-resources"></a>AKS na Azure Stackch prostředků HCI 

Aby se zjednodušila konfigurace sítě pro úlohy aplikací, AKS v Azure Stack HCI přiděluje IP adresy následujícím objektům v nasazení: 

- **Server API clusteru Kubernetes** – Server API je součástí roviny ovládacího prvku Kubernetes, která zpřístupňuje rozhraní Kubernetes API. Server rozhraní API je front-end pro plochu ovládacího prvku Kubernetes. Servery API jsou vždycky přidělené statické IP adresy bez ohledu na základní síťový model.

- **Kubernetes uzly (na virtuálních počítačích)** – cluster Kubernetes se skládá ze sady pracovních počítačů nazývaných uzly, které spouštějí aplikace s podporou kontejnerů. Každý cluster má alespoň jeden pracovní uzel. V případě AKS na Azure Stack HCI se uzly Kubernetes spouštějí v rámci virtuálních počítačů. Tyto virtuální počítače se vytvářejí na Azure Stack clusteru HCI. 

- **Kubernetes Services** – v Kubernetes se *služby* logicky seskupují s tím, že umožňují přímý přístup prostřednictvím IP adresy nebo názvu DNS a na určitém portu. Provoz můžete také distribuovat pomocí *Nástroje pro vyrovnávání zatížení*. Služby Kubernetes se vždycky přiřazují statické IP adresy bez ohledu na základní síťový model.

- **HAProxy Load** Balancer – HAProxy (proxy s vysokou dostupností) je nástroj pro vyrovnávání zatížení TCP/HTTP a proxy server, který umožňuje, aby se na serveru rozšířily příchozí požadavky mezi více koncových bodů. Každý cluster úloh v AKS-HCI má HAProxy Nástroj pro vyrovnávání zatížení, který běží uvnitř vlastního specializovaného virtuálního počítače.

- **Microsoft on-premises Cloud Service** – Toto je poskytovatel cloudu Azure Stack HCI pro spuštění Kubernetes v místním clusteru Azure Stack HCI. Model sítě, za nímž následuje cluster Azure Stack HCI, určuje metodu přidělování IP adres pro tento objekt.


## <a name="virtual-networks"></a>Virtuální sítě

V AKS na Azure Stack HCI se virtuální sítě používají k přidělování IP adres prostředkům Kubernetes, které je vyžadují, jak je uvedeno výše. Existují dva síťové modely, ze kterých si můžete vybrat, v závislosti na požadované AKSě v Azure Stack síťové architektuře HCI. Všimněte si, že virtuální Síťová architektura, která je zde definovaná pro vaše AKS nasazení v Azure Stack HCI, se liší od základní fyzické síťové architektury ve vašem datovém centru.

- Statická IP síť – virtuální síť přiděluje statické IP adresy serveru rozhraní API clusteru Kubernetes, uzlům Kubernetes, podkladovým virtuálním počítačům, nástrojům pro vyrovnávání zatížení a jakýmkoli službám Kubernetes, které spouštíte nad vaším clusterem.

- Sítě DHCP – virtuální síť přiděluje dynamické IP adresy uzlům Kubernetes, podkladovým virtuálním počítačům a nástrojům pro vyrovnávání zatížení pomocí serveru DHCP. Statické IP adresy jsou pořád přidělené serverem API clusteru Kubernetes a všemi Kubernetes službami, které spouštíte nad clusterem.

### <a name="virtual-ip-pool"></a>Fond virtuálních IP adres
Fond virtuálních IP adres (VIP) je povinný pro AKS nasazení HCI pro Azure Stack. Fond VIP je rozsah rezervovaných statických IP adres, které se používají k přidělování IP adres serveru rozhraní API clusteru Kubernetes a externím IP adresám pro služby Kubernetes, aby se zajistilo, že jsou vaše aplikace vždycky dostupné. Bez ohledu na model virtuální sítě musíte zřídit fond VIP pro nasazení hostitele AKS. Počet IP adres ve fondu VIP závisí na počtu clusterů úloh a Kubernetes služeb spuštěných ve vašem nasazení, u statických i síťových modelů DHCP. Fond VIP se ale v závislosti na vašem síťovém modelu liší následujícími způsoby:

- Statická IP adresa: Pokud používáte statickou IP adresu, ujistěte se, že virtuální IP adresy jsou ze stejné podsítě. 
- DHCP – Pokud je vaše síť nakonfigurovaná pomocí protokolu DHCP, budete muset spolupracovat se správcem sítě a vyhradit rozsah IP adres, který se má použít pro fond VIP.

### <a name="kubernetes-node-ip-pool"></a>Fond IP adres uzlu Kubernetes
Jak je vysvětleno výše, uzly Kubernetes běží na specializovaných virtuálních počítačích v AKS nasazení v prostředí Azure Stack HCI. AKS na Azure Stack HCI přiděluje IP adresy těmto virtuálním počítačům, aby mohl komunikovat mezi uzly Kubernetes. V případě síťového modelu DHCP nemusíte určovat fond virtuálních počítačů uzlu Kubernetes, protože IP adresy uzlů Kubernetes se dynamicky přiřazují serverem DHCP ve vaší síti. U síťového modelu založeného na statických IP adresách musíte zadat rozsah IP adres uzlu Kubernetes Node. Počet IP adres v tomto rozsahu závisí na celkovém počtu Kubernetes uzlů napříč hostitelem AKS a clustery Kubernetes úloh.

### <a name="virtual-network-with-static-ip-networking"></a>Virtuální síť se statickými IP sítěmi

Tento síťový model vytvoří virtuální síť, která přidělí statické IP adresy všem objektům ve vašem nasazení. Přidaná výhoda použití sítě statických IP adres spočívá v tom, že dlouhodobá nasazení a úlohy aplikací jsou zaručeny tak, aby byly vždy dosažitelné. Pro AKS nasazení HCI doporučujeme vytvořit model virtuální sítě statických IP adres pro vaše Azure Stack.

Při definování virtuální sítě s konfiguracemi statických IP adres je nutné zadat následující parametry:

- Název: název vaší virtuální sítě.
- AddressPrefix: Předpona IP adresy, která se má použít pro vaši podsíť.
- Brána: IP adresa výchozí brány pro podsíť.
- Server DNS: pole IP adres ukazující na servery DNS, které se mají použít pro podsíť. Je možné zadat minimálně jeden a maximálně 3 servery.
- Fond IP adres uzlu Kubernetes: nepřetržitý rozsah IP adres, který se má použít pro virtuální počítače uzlu Kubernetes.
- Virtuální fond IP adres: nepřetržitý rozsah IP adres, který se má použít pro Server API Kubernetes clusteru a služby Kubernetes, které vyžadují externí IP adresu.
- ID sítě vLAN: ID sítě vLAN pro virtuální síť. Pokud tento parametr vynecháte, nebude tato virtuální síť označená.

### <a name="virtual-network-with-dhcp-networking"></a>Virtuální síť s využitím sítě DHCP

Tento síťový model vytvoří virtuální síť, která přidělí dynamické IP adresy všem objektům ve vašem nasazení.  

Při definování virtuální sítě s konfiguracemi statických IP adres je nutné zadat následující parametry:

- Název: název vaší virtuální sítě.
- Virtuální fond IP adres: nepřetržitý rozsah IP adres, který se má použít pro Server API Kubernetes clusteru a služby Kubernetes, které vyžadují externí IP adresu.
- ID sítě vLAN: ID sítě vLAN pro virtuální síť. Pokud tento parametr vynecháte, nebude tato virtuální síť označená.

## <a name="microsoft-on-premises-cloud-service"></a>Místní cloudová služba Microsoftu

Microsoft místní Cloud (Course) je nová sada pro správu, která umožňuje správu virtuálních počítačů na Azure Stack HCL a cloudových SDDC založených na Windows serveru. Course se skládá z těchto:

- jedna instance vysoce dostupné `cloud agent` služby, která je nasazena v clusteru. Tento agent běží na jednom uzlu v clusteru Azure Stack HCI. 
- `node agent`běžící na každém Azure Stack fyzický uzel HCI. 

`-cloudserviceCIDR`Je parametr v `Set-AksHciConfig` příkazu, který se používá k přiřazení virtuální IP adresy, abyste měli jistotu, že máme vysoce dostupnou službu cloudového agenta.

Přidělování IP adres pro službu Course závisí na podkladovém síťovém modelu, po kterém následují nasazení clusteru Azure Stack HCI. **Služba přidělování IP adres pro službu Course je nezávislá na modelu virtuální sítě a je závislá jenom na základní fyzické síti, která poskytuje IP adresy uzlům clusteru Azure Stack HCI ve vašem datovém centru.**

### <a name="azure-stack-hci-cluster-nodes-with-a-dhcp-based-ip-address-allocation-model"></a>Azure Stack uzlech clusteru HCI s modelem přidělování IP adres založeným na protokolu DHCP.

Pokud se Azure Stack uzlů HCI přiřadí IP adresa ze serveru DHCP přítomného ve fyzické síti, nemusíte explicitně poskytovat IP adresu službě Course.

### <a name="azure-stack-hci-cluster-nodes-with-a-static-ip-allocation-model"></a>Azure Stack uzlech clusteru HCI s modelem přidělování statických IP adres 

Pokud jsou na Azure Stack uzlem clusteru HCI přiřazeny statické IP adresy, musíte explicitně zadat IP adresu pro cloudovou službu Course. Tato IP adresa pro službu Course musí být ve stejné podsíti jako uzly clusteru Azure Stack HCI. K explicitnímu přiřazení IP adresy pro službu Course použijte `-cloudserviceCIDR` parametr v `Set-AksHciConfig` příkazu. Ujistěte se, že jste zadali IP adresu ve formátu CIDR, například "10.11.23.45/16".

## <a name="compare-network-models"></a>Porovnání síťových modelů

DHCP i statická IP adresa poskytují síťové připojení k vašemu AKS nasazení v Azure Stack HCI. Existují však i výhody a nevýhody. Na nejvyšší úrovni platí následující požadavky:

* **DHCP**
    - Nezaručuje u některých typů prostředků v Azure Stack AKS nasazení HCI pro některé typy prostředků dlouhotrvající IP adresy.
    - Podporuje rozšíření rezervovaných IP adres DHCP, pokud vaše nasazení bude větší, než jste původně předpokládali.
    
* **Statická IP adresa**
    - Garantuje dlouhodobé IP adresy pro všechny prostředky v AKS nasazení v Azure Stack HCI.
    - Vzhledem k tomu, že nepodporujeme automatické rozšiřování fondu IP adres uzlu Kubernetes, možná nebudete moci vytvořit nové clustery, pokud jste vyčerpali fond IP adres uzlu Kubernetes.


Následující tabulka porovnává přidělování IP adres pro prostředky mezi statickými a síťovými modely IP a DHCP:

| Schopnost                                                                                   | Statická IP adresa   | DHCP |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Server API clusteru Kubernetes                                             | Přiřazeno staticky pomocí fondu VIP | Přiřazeno staticky pomocí fondu VIP |
| Uzly Kubernetes (na virtuálních počítačích)                                                                         | Přiřazeno pomocí Kubernetes fondu IP adres uzlu | Dynamicky přiřazené |
| Služby Kubernetes                                          | Přiřazeno staticky pomocí fondu VIP | Přiřazeno staticky pomocí fondu VIP |
| Virtuální počítač HAProxy pro vyrovnávání zatížení                                          | Přiřazeno pomocí Kubernetes fondu IP adres uzlu | Dynamicky přiřazené |
| Microsoft místní cloudová služba                                               | Závisí na konfiguraci fyzické sítě pro Azure Stack uzly clusteru HCI. | Závisí na konfiguraci fyzické sítě pro Azure Stack uzly clusteru HCI. |
| Fond VIP                                             | Povinné | Povinné |
| Fond IP adres uzlu Kubernetes | Povinné | Nepodporuje se |


## <a name="minimum-ip-address-reservations-for-an-aks-on-azure-stack-hci-deployment"></a>Minimální rezervace IP adres pro AKS při nasazení rozhraní HCI pro Azure Stack
Bez ohledu na váš model nasazení zůstává počet rezervovaných IP adres stejný. V této části se dozvíte o počtu IP adres, které se mají vyhradit na základě vašeho Azure Stack AKS modelu nasazení HCI.

### <a name="minimum-ip-address-reservation"></a>Minimální rezervace IP adresy

Přinejmenším byste měli rezervovat následující počet IP adres pro nasazení:

| Typ clusteru  | Řídicí uzel roviny | Pracovní uzel | Pro operace aktualizace | Nástroj pro vyrovnávání zatížení  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| Hostitel AKS |  1 IP ADRESA |  NA  |  2 IP |  NA  |
| Cluster úloh  |  1 IP adresa na uzel  | 1 IP adresa na uzel |  5 IP  |  1 IP ADRESA |

Kromě toho byste měli rezervovat následující počet IP adres pro váš fond VIP:

| Typ prostředku  | Počet IP adres 
| ------------- | ------------------
| Server API clusteru |  1 na cluster 
| Služby Kubernetes  |  1 na službu  

Jak vidíte, je počet požadovaných IP adres proměnlivý podle Azure Stack AKS architektury HCI a počtu služeb, které v clusteru Kubernetes spustíte. Pro vaše nasazení doporučujeme rezervovat celkem 256 IP adres (/24 podsítí).

### <a name="walking-through-an-example-deployment"></a>Procházení ukázkového nasazení

Jana je správce IT, který se právě začíná AKSem na Azure Stack HCI. Chce nasadit 2 Kubernetes clustery – cluster Kubernetes a a Kubernetes cluster B na svém Azure Stack clusteru HCI. Také chce spustit hlasovací aplikaci na jejím clusteru. Tato aplikace má 3 instance uživatelského rozhraní front-endu spuštěné v obou clusterech a 1 instanci back-end databáze.

- Cluster Kubernetes a má 3 uzly řídicí plochy a 5 pracovních uzlů.
- Cluster Kubernetes B má 1 uzel řídicí roviny a 3 pracovní uzly.
- 3 instance uživatelského rozhraní front-endu (port 443)
- 1 instance back-end databáze (port 80)

Podle výše uvedené tabulky bude muset vyhradit:

- 3 IP adresy pro svého hostitele AKS (1 IP pro uzel řídicí roviny a 2 adresy IP pro spuštění operací aktualizace)
- 3 IP adresy pro uzly řídicí plochy v clusteru A (1 IP adresa na uzel řídicí roviny)
- 5 IP adres pro své pracovní uzly v clusteru A (1 IP adresa na uzel řídicí roviny)
- 6 IP adres navíc pro cluster A (5 IP adres pro spouštění operací aktualizace a 1 IP pro nástroj pro vyrovnávání zatížení)
- 1 IP adresy pro uzly řídicí plochy v clusteru B (1 IP adresa na uzel řídicí roviny)
- 3 IP adresy pro své pracovní uzly v clusteru B (1 IP adresa na uzel řídicí roviny)
- 6 IP adres pro cluster B (5 IP adres pro spouštění operací aktualizace a 1 IP pro nástroj pro vyrovnávání zatížení)
- 2 IP adresy pro své servery API clusteru Kubernetes (1 IP adresa na cluster Kubernetes)
- 3 IP adresy pro službu Kubernetes (1 IP adresa na instanci uživatelského rozhraní front-endu, protože všechny používají stejný port. Back-end databáze může používat jednu ze tří IP adres, protože se nachází na jiném portu.)

Jak je vysvětleno výše, Jana vyžaduje celkem 32 IP adres. Jana by proto měl rezervovat podsíť/26 pro svou virtuální síť. 

### <a name="splitting-reserved-ip-addresses-based-on-a-static-ip-network-model"></a>Rozdělení rezervovaných IP adres na základě modelu sítě statických IP adres

I když celkový počet rezervovaných IP adres zůstane stejný, model nasazení určí, jak jsou tyto IP adresy rozdělené mezi skupiny IP adres. Jak jsme probrali dřív, statický síťový model IP má 2 fondy IP adres:

- Fond IP adres uzlu Kubernetes – pro virtuální počítače uzlu Kubernetes a virtuální počítač nástroje pro vyrovnávání zatížení. Tento fond IP adres zahrnuje také IP adresu nutnou pro spuštění operací aktualizace.
- Virtuální fond IP adres – pro Server rozhraní Kubernetes API a služby Kubernetes

Při práci s výše uvedeným příkladem musí Jana dále dělit tyto IP adresy mezi fondy VIP a fondy IP adres uzlu Kubernetes:
- 5 (2 pro Kubernetes cluster API Server a 3 pro služby Kubernetes) z IP adres 32 pro svůj fond VIP adres.
- 27 (všechny IP adresy pro své uzly Kubernetes a podkladové virtuální počítače, virtuální počítače a operace aktualizací nástroje Load Balancer) pro svůj fond IP adres uzlu Kubernetes.

### <a name="splitting-reserved-ip-addresses-based-on-a-dhcp-network-model"></a>Rozdělení rezervovaných IP adres na základě síťového modelu DHCP

I když celkový počet rezervovaných IP adres zůstane stejný, model nasazení určí, jak jsou tyto IP adresy rozdělené mezi skupiny IP adres. Jak jsme probrali dřív, model sítě DHCP má 1 fond IP adres:

- Virtuální fond IP adres – pro Server rozhraní Kubernetes API a služby Kubernetes

Práce s výše uvedeným příkladem:
- Jana musí vyhradit celkem 32 IP adres nebo podsíť/26 na jejím serveru DHCP. 
- Musí rezervovat 5 (2 pro Kubernetes cluster API Server a 3 pro Kubernetes služby) z IP adres 32 pro svůj fond VIP. 

## <a name="next-steps"></a>Další kroky

V tomto tématu jste zjistili základní koncepty, které poskytují sítě vašim aplikacím v AKS na Azure Stack HCI. V dalším kroku můžete:

- [Konfigurace nastavení proxy serveru na AKS při Azure Stack HCI](./proxy.md)
- [Požadavky na systém](./system-requirements.md)

