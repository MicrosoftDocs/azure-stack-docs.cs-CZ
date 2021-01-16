---
title: Přehled síťového adaptéru v Azure Stack HCI
description: V tomto tématu se dozvíte víc o síťovém adaptéru pro softwarově definované sítě v Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: f8c559ff409cb6344f20ffa85a60983724ae5b17
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255040"
---
# <a name="what-is-network-controller"></a>Co je síťový adaptér?

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019

Síťový adaptér je základem správy softwarově definovaných sítí (SDN). Je to vysoce škálovatelná role serveru, která poskytuje centralizovaný, programovatelný bod automatizace pro správu, konfiguraci, monitorování a odstraňování potíží infrastruktury virtuální sítě.

Pomocí síťového adaptéru můžete automatizovat konfiguraci a správu síťové infrastruktury namísto provádění ruční konfigurace síťových zařízení a služeb.

## <a name="how-network-controller-works"></a>Jak funguje síťový adaptér

Síťový adaptér poskytuje jedno aplikační programovací rozhraní (API), které umožňuje síťovému adaptéru komunikovat a spravovat síťová zařízení, služby a součásti (Southbound API), a druhé rozhraní API, které umožňuje aplikacím pro správu sdělit síťovému adaptéru, která nastavení sítě a služby potřebují (Northbound API).

Pomocí rozhraní Southbound API může síťový adaptér Spravovat síťová zařízení a síťové služby a shromažďovat všechny informace, které o síti potřebujete. Síťový adaptér nepřetržitě monitoruje stav síťových zařízení a služeb a zajišťuje, že dojde k nápravě veškerého posunu konfigurace z požadovaného stavu.

Rozhraní Northbound API funkce síťového adaptéru je implementováno jako rozhraní REST. Poskytuje možnost spravovat síť datového centra z aplikací pro správu. Pro správu mohou uživatelé použít REST API přímo nebo používat prostředí Windows PowerShell postavené na REST API nebo aplikace pro správu s grafickým uživatelským rozhraním, jako je například centrum pro správu systému Windows nebo System Center Virtual Machine Manager.

## <a name="network-controller-features"></a>Funkce síťového adaptéru

Síťový adaptér umožňuje spravovat funkce SDN, jako jsou virtuální sítě, brány firewall, Load Balancer softwaru a brána služby RAS. Níže jsou uvedeny některé z mnoha funkcí.

### <a name="virtual-network-management"></a>Správa virtuálních sítí

Funkce síťový adaptér umožňuje nasazení a konfiguraci virtualizace sítě Hyper-V, konfiguraci virtuálních síťových adaptérů na jednotlivých virtuálních počítačích a ukládání a distribuci zásad virtuální sítě. Pomocí této funkce můžete vytvořit virtuální sítě a podsítě, připojit virtuální počítače k těmto sítím a povolit komunikaci mezi virtuálními počítači ve stejné virtuální síti.

Síťový adaptér podporuje sítě založené na síti VLAN (Virtual místní síť), protokol NVGRE (Network Virtualization Generic Routing Encapsulation) a virtuální rozšiřitelnou místní síť (VXLAN).

## <a name="firewall-management"></a>Správa brány firewall

Funkce síťový adaptér umožňuje konfigurovat a spravovat Access Control pravidla brány firewall pro povolení a správu pro vaše virtuální počítače s úlohami pro interní (východ a západní) a externí (sever/Jižní) síťové přenosy ve vašem datovém centru. Pravidla brány firewall se šíří na virtuálním počítači virtuálních počítačů úloh, které jsou distribuovány napříč vašimi úlohami v datovém centru a pohybují spolu s vašimi úlohami.

Pomocí rozhraní Northbound API můžete definovat pravidla brány firewall pro příchozí i odchozí provoz z virtuálních počítačů úloh. Můžete také konfigurovat jednotlivá pravidla brány firewall a ukládat provoz, který byl povolen nebo odepřen pravidlem do protokolu.

## <a name="software-load-balancer-management"></a>Správa Load Balancer softwaru

[Software Load Balancer](software-load-balancer.md) umožňuje, aby bylo možné hostovat více serverů pro hostování stejné úlohy a zajistit vysokou dostupnost a škálovatelnost. Pomocí Load Balancer softwaru můžete nakonfigurovat a spravovat vyrovnávání zatížení, překlad adres (NAT) a odchozí přístup k Internetu pro úlohy připojené k tradičním sítím VLAN a virtuálním sítím.

## <a name="gateway-management"></a>Správa brány

[Brána RAS (Remote Access Service)](gateway-overview.md) umožňuje nasazovat, konfigurovat a spravovat virtuální počítače, které jsou členy fondu bran, a poskytuje tak externí síťové připojení k vašim zákaznickým úlohám. U bran se mezi virtuálními a vzdálenými sítěmi podporují tyto typy připojení:

- Připojení brány virtuální privátní sítě (VPN) typu Site-to-site pomocí protokolu IPsec
- Připojení brány VPN typu Site-to-site pomocí protokolu GRE (Generic Routing Encapsulation)
- Schopnost předávání vrstvy 3
 
Připojení brány podporují Border Gateway Protocol (BGP) pro dynamickou správu směrování.

## <a name="virtual-appliance-chaining"></a>Řetězení virtuálních zařízení

Funkce síťový adaptér umožňuje připojit virtuální síťová zařízení k virtuálním sítím. Tato zařízení je možné použít pro pokročilou bránu firewall, Vyrovnávání zatížení, zjišťování neoprávněných vniknutí a prevence a mnoho dalších síťových služeb. Můžete přidat virtuální zařízení, která provádějí uživatelsky definované funkce směrování a zrcadlení portů. Pomocí uživatelsky definovaného směrování se virtuální zařízení použije jako směrovač mezi virtuálními podsítěmi ve virtuální síti. Při zrcadlení portů se veškerý síťový provoz, který se zadává nebo opouští monitorovaný port, duplikuje a pošle na virtuální zařízení pro účely analýzy.

Další informace o trasách definovaných uživatelem najdete v tématu [použití síťových virtuálních zařízení na Virtual Network](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn).

## <a name="network-controller-deployment-considerations"></a>Požadavky na nasazení síťového adaptéru

- Nesaďte roli serveru síťového adaptéru na fyzických hostitelích. Síťový adaptér by měl být nasazený na svých vlastních vyhrazených virtuálních počítačích.

- Síťový adaptér můžete nasadit v prostředích domény i mimo doménu. V prostředích domén síťový adaptér ověřuje uživatele a síťová zařízení pomocí protokolu Kerberos. v prostředích, která nejsou doménová, je nutné nasadit certifikáty pro ověřování.

- Pro nasazení síťového adaptéru je důležité zajistit vysokou dostupnost a možnost snadného horizontálního navýšení nebo snížení kapacity podle potřeb datového centra. K zajištění vysoké dostupnosti pro aplikaci síťového adaptéru používejte aspoň tři virtuální počítače.

- Síťový adaptér při zajištění vysoké dostupnosti a škálovatelnosti spoléhá na Service Fabric. Service Fabric poskytuje platformu distribuovaných systémů pro vytváření škálovatelných, spolehlivých a snadno spravovaných aplikací. [Přečtěte si další informace o síťovém adaptéru jako Service Fabric aplikaci](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability#network-controller-as-a-service-fabric-application).


## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Plánování nasazení síťového adaptéru](network-controller.md)
- [Nasazení síťového adaptéru pomocí Windows PowerShellu](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts)
- [SDN ve Azure Stack HCI](software-defined-networking.md)