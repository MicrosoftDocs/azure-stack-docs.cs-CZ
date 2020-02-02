---
title: Plánování integrace sítě pro centrum Azure Stack
description: Naučte se plánovat integraci sítě Datacenter pomocí integrovaných systémů centra Azure Stack.
author: ihenkel
ms.topic: article
ms.date: 10/23/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: cd1e5e66b5cdb893591fc4116b335ff399ace736
ms.sourcegitcommit: bcd2c6cd08526723f4b770b149c5e9a4fd5ef0ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76922641"
---
# <a name="network-integration-planning-for-azure-stack-hub"></a>Plánování integrace sítě pro centrum Azure Stack

Tento článek poskytuje Azure Stack informace o infrastruktuře sítě centra, které vám pomůžou rozhodnout, jak nejlépe integrovat Azure Stack hub do stávajícího síťového prostředí.

> [!NOTE]
> K překladu externích názvů DNS z centra Azure Stack (například www\.bing.com) je potřeba poskytnout servery DNS pro přeposílání požadavků DNS. Další informace o požadavcích na službu DNS centra Azure Stack najdete v tématu [integrace služby Azure Stack hub Datacenter – DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Návrh fyzické sítě

Řešení centra Azure Stack vyžaduje odolnou a vysokou dostupnou fyzickou infrastrukturu pro podporu jeho provozu a služeb. Odchozí připojení z paměti k přepínačům ohraničení jsou omezená na SFP28a SFP + nebo na více než 1 GB, 10 GB nebo rychlosti 25 GB. Obraťte se na dodavatele hardwaru OEM (Original Equipment Manufacturer) pro dostupnost. Následující diagram znázorňuje náš doporučený návrh:

![Doporučený návrh sítě centra Azure Stack](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logické sítě

Logické sítě představuje abstrakci základní fyzické síťové infrastruktury. Slouží k organizování a zjednodušení přiřazení sítě pro hostitele, virtuální počítače a služby. V rámci vytváření logických sítí se vytvoří síťové lokality pro definování virtuálních místních sítí (VLAN), podsítí protokolu IP a párů podsítě protokolu IP/sítě VLAN, které jsou přidružené k logické síti v každém fyzickém umístění.

Následující tabulka uvádí logické sítě a přidružené rozsahy podsítí IPv4, které je nutné naplánovat:

| Logické sítě | Popis | Velikost | 
| -------- | ------------- | ------------ | 
| Veřejná virtuální IP adresa | Rozbočovač Azure Stack používá celkem 31 adres z této sítě. Osm veřejných IP adres se používá pro malou sadu služby Azure Stack hub a ostatní se používají pro virtuální počítače klienta. Pokud plánujete použít App Service a poskytovatele prostředků SQL, použijí se 7 dalších adres. Zbývajících 15 IP adres se rezervuje pro budoucí služby Azure. | /26 (62 hostitelů)-/22 (1022 hostitelů)<br><br>Doporučené =/24 (254 hostitelů) | 
| Přepnout infrastrukturu | IP adresy Point-to-Point pro účely směrování, rozhraní pro správu vyhrazených přepínačů a adresy zpětné smyčky přiřazené přepínači. | /26 | 
| Infrastruktura | Používá se pro komunikaci mezi interními komponentami centra Azure Stack. | /24 |
| Privátní | Používá se pro síť úložiště, privátní virtuální IP adresy, kontejnery infrastruktury a další interní funkce. Od 1910 se velikost této podsítě mění na/20. Další informace najdete v části [privátní síť](#private-network) v tomto článku. | /20 | 
| BMC | Slouží ke komunikaci s BMC na fyzických hostitelích. | /26 | 
| | | |

> [!NOTE]
> Když je systém aktualizován na verzi 1910, výstraha na portálu přihlásí operátorovi ke spuštění nové rutiny PEP **set-AzsPrivateNetwork** pro přidání nového/20 PRIVÁTNÍho adresního prostoru. Pokyny ke spuštění rutiny najdete v [poznámkách k verzi 1910](release-notes.md) . Další informace a pokyny k výběru privátního ADRESního prostoru/20 najdete v části [privátní síť](#private-network) v tomto článku.

## <a name="network-infrastructure"></a>Síťová infrastruktura

Síťová infrastruktura pro centrum Azure Stack se skládá z několika logických sítí, které jsou nakonfigurované na přepínačích. Následující diagram znázorňuje tyto logické sítě a způsob jejich integrace s přepínači "rozvaděče", řadiče pro správu základní desky (BMC) a hraniční sítě (síť zákazníka).

![Diagram logické sítě a přepínač připojení](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Síť řadiče pro správu základní desky

Tato síť je vyhrazena pro připojení všech řadičů pro správu základní desky (označovaných také jako BMC nebo procesory služeb) k síti pro správu. Mezi příklady patří: iDRAC, MOP, iBMC a tak dále. Ke komunikaci s jakýmkoli uzlem BMC se používá jenom jeden účet řadiče pro správu základní desky. Je-li k dispozici, je hostitel životního cyklu hardwaru (HLH) umístěn v této síti a může poskytovat software pro správu a údržbu hardwaru určený výrobcem OEM.

HLH také hostuje virtuální počítač nasazení (DVM). DVM se používá během nasazení centra Azure Stack a po dokončení nasazení se odebere. DVM vyžaduje přístup k internetu ve scénářích propojeného nasazení, aby bylo možné testovat, ověřovat a přistupovat k několika komponentám. Tyto součásti můžou být uvnitř firemní sítě i mimo ni (například NTP, DNS a Azure). Další informace o požadavcích na připojení najdete [v části věnované překladu adres (NAT) v tématu Integrace brány firewall centra Azure Stack](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Privátní síť

Tato/20 IP adres (4096) je privátní pro Azure Stack oblasti centra (nejedná se o trasu mezi zařízeními přepínače hraničního systému Azure Stack hub) a rozdělená do několika podsítí, tady je několik příkladů:

- **Síť úložiště**: a/25 (128 IP adres), která se používá k podpoře použití prostorových přímých a přenosů úložiště protokolu SMB (Server Message Block) a migrace za provozu virtuálních počítačů.
- **Interní virtuální IP síť**: a/25 síť vyhrazenou pouze pro interní VIP pro nástroj pro vyrovnávání zatížení softwaru.
- **Síť kontejneru**: a/23 (512 IP adres), které jsou vyhrazené jenom pro interní přenosy mezi kontejnery, na kterých běží služby infrastruktury.

Od 1910 se velikost privátní sítě změní na/20 (4096 IP adres) privátního ADRESního prostoru. Tato síť bude soukromá pro systém Azure Stack hub (netrasuje se nad rámec hraničních zařízení Azure Stack systému rozbočovače) a dá se znovu použít v několika systémech Azure Stack hub v rámci vašeho datového centra. I když je síť soukromá pro Azure Stack hub, nesmí se překrývat s ostatními sítěmi v datacentru. Pokud dojde k překrytí, je možné, že Azure Stack hub nemůže směrovat síťový provoz v podnikové síti externě. Pokyny k privátnímu adresnímu prostoru IP adres vám doporučujeme postupovat podle [dokumentu RFC 1918](https://tools.ietf.org/html/rfc1918).

Tento/20 privátních IP adres se rozdělí do několika sítí, které umožní provozovat interní infrastrukturu Azure Stackho centra na kontejnerech v budoucích verzích. Další informace najdete v [poznámkách k verzi 1910](release-notes.md). Kromě toho tato nová privátní IP adresa umožňuje nepřetržité úsilí snížit před nasazením požadované IP místo pro směrování.

Pro systémy nasazené před 1910 bude tato/20 podsíť další síť, která se má po aktualizaci na 1910 zadat do systémů. Další síť bude nutné poskytnout systému pomocí rutiny **set-AzsPrivateNetwork** PEP. Pokyny k této rutině najdete v [poznámkách k verzi 1910](release-notes.md).

### <a name="azure-stack-hub-infrastructure-network"></a>Síť infrastruktury centra Azure Stack

Tato síť/24 je vyhrazená pro interní Azure Stack komponenty centra, aby mohly komunikovat a vyměňovat data mezi sebou. Tato podsíť může být externě směrovatelné do vašeho datacentra pro řešení Azure Stack hub. V této podsíti nedoporučujeme používat veřejné nebo internetové IP adresy směrovatelný. Tato síť se inzeruje na hranici, ale většina IP adres je chráněná pomocí seznamů Access Control (ACL). IP adresy povolené pro přístup jsou v malém rozsahu, který je ekvivalentní velikosti až/27 sítě a hostitelských služeb, jako je například [privilegovaný koncový bod (PEP)](azure-stack-privileged-endpoint.md) a [zálohování centra Azure Stack](azure-stack-backup-reference.md).

### <a name="public-vip-network"></a>Síť veřejných virtuálních IP adres

Veřejná VIP síť je přiřazena k síťovému adaptéru v centru Azure Stack. Nejedná se o logickou síť na přepínači. SLB používá fond adres a přiřazuje sítě/32 pro zatížení klientů. V tabulce směrování přepínače jsou tyto/32 IP adresy inzerovány jako dostupná trasa prostřednictvím protokolu BGP. Tato síť obsahuje přístup k externím nebo veřejným IP adresám. Infrastruktura centra Azure Stack si vyhrazuje prvních 31 adres z této veřejné sítě VIP, zatímco ostatní jsou používány virtuálními počítači klienta. Velikost sítě v této podsíti může být v rozsahu od minimálně/26 (64 hostitelů) až do maximálního počtu/22 (1022 hostitelů). Doporučujeme, abyste naplánovali síť/24.

### <a name="switch-infrastructure-network"></a>Přepnout síť infrastruktury

Tato síť/26 je podsíť, která obsahuje podsítě IP adres Point-to-Point/30 (dvě hostitele) a zpětná smyčka, které jsou vyhrazené/32 pro místní správu přepínačů a ID směrovače protokolu BGP. Tento rozsah IP adres musí být směrovatelný z řešení Azure Stack hub do vašeho datacentra. Můžou to být privátní nebo veřejné IP adresy.

### <a name="switch-management-network"></a>Přepnout síť pro správu

Tato síť/29 (šest hostitelských IP adres) je vyhrazená pro připojení portů pro správu přepínačů. Umožňuje vzdálený přístup pro nasazení, správu a řešení potíží. Počítá se ze sítě infrastruktury přepínače uvedené výše.

## <a name="permitted-networks"></a>Povolené sítě

Počínaje 1910 bude mít list nasazení nové pole, které operátorovi umožní změnit některé seznamy řízení přístupu (ACL), aby povoloval přístup k rozhraním pro správu síťových zařízení a k hostiteli životního cyklu hardwaru (HLH) z rozsahu důvěryhodné sítě Datacenter. Se změnou seznamu řízení přístupu může operátor dovolit, aby JumpBox virtuální počítače pro správu v rámci určitého rozsahu sítě pro přístup k rozhraní pro správu přepínačů, HLH operačním systému a HLH BMC. Operátor může do tohoto seznamu zadat jednu nebo více podsítí, pokud pole necháte prázdné, bude ve výchozím nastavení odepřen přístup. Tato nová funkce nahrazuje ruční zásah po nasazení, který se používá k popsání v [nastavení změnit konkrétní v konfiguraci přepínače centra Azure Stack](azure-stack-customer-defined.md#access-control-list-updates).

## <a name="next-steps"></a>Další kroky

Seznamte se s plánováním sítě: [ohraničení připojení](azure-stack-border-connectivity.md).
