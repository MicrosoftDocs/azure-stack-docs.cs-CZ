---
title: Plánování integrace sítě pro centrum Azure Stack
description: Naučte se plánovat integraci sítě Datacenter pomocí integrovaných systémů centra Azure Stack.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 121bbc5ff081a6a7773d69294175f979b89bcfc5
ms.sourcegitcommit: b65952127f39c263b162aad990e4d5b265570a7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80402841"
---
# <a name="network-integration-planning-for-azure-stack"></a>Plánování integrace sítě pro Azure Stack

Tento článek poskytuje Azure Stack informace o infrastruktuře sítě, které vám pomůžou rozhodnout, jak nejlépe integrovat Azure Stack do stávajícího síťového prostředí. 

> [!NOTE]
> K překladu externích názvů DNS z Azure Stack (například www\.bing.com) je potřeba poskytnout servery DNS pro přeposílání požadavků DNS. Další informace o Azure Stack požadavcích DNS najdete v tématu věnovaném [integraci Azure Stack Datacenter – DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Návrh fyzické sítě

Řešení Azure Stack pro zajištění podpory svého provozu a služeb vyžaduje odolnou a vysoce dostupnou fyzickou infrastrukturu. Aby bylo možné integrovat Azure Stack do sítě, vyžaduje odchozí připojení od přepínačů Top-of-rack (rozhraní) k nejbližšímu přepínači nebo směrovači, který je v této dokumentaci označován jako ohraničení. Tory může být odchozí připojení k jedné nebo druhé dvojici ohraničení. Modul pro automatizaci je předem nakonfigurovaný pomocí našeho nástroje pro automatizaci, očekává se minimálně jedno spojení mezi systémem a hranicí při použití směrování protokolu BGP a minimálně dvou připojení (jedna za službu) mezi systémem a hranicí při použití statického směrování, s maximálně čtyřmi připojeními. buď možnosti směrování. Tato připojení jsou omezená na více než SFP28 média a na jednu GB, 10 GB nebo rychlosti 25 GB. Obraťte se na dodavatele hardwaru OEM (Original Equipment Manufacturer) pro dostupnost. Následující diagram znázorňuje doporučený návrh:

![Doporučený Azure Stack návrh sítě](media/azure-stack-network/physical-network.svg)


## <a name="logical-networks"></a>Logické sítě

Logické sítě představuje abstrakci základní fyzické síťové infrastruktury. Slouží k organizování a zjednodušení přiřazení sítě pro hostitele, virtuální počítače a služby. V rámci vytváření logických sítí se vytvoří síťové lokality pro definování virtuálních místních sítí (VLAN), podsítí protokolu IP a párů podsítě protokolu IP/sítě VLAN, které jsou přidružené k logické síti v každém fyzickém umístění.

Následující tabulka uvádí logické sítě a přidružené rozsahy podsítí IPv4, které je nutné naplánovat:

| Logické sítě | Popis | Velikost | 
| -------- | ------------- | ------------ | 
| Veřejná virtuální IP adresa | Azure Stack používá celkem 31 adres z této sítě. Osm veřejných IP adres se používá pro malou sadu Azure Stack služeb a zbývající jsou používány virtuálními počítači klienta. Pokud plánujete použít App Service a poskytovatele prostředků SQL, použijí se 7 dalších adres. Zbývajících 15 IP adres se rezervuje pro budoucí služby Azure. | /26 (62 hostitelů)-/22 (1022 hostitelů)<br><br>Doporučené =/24 (254 hostitelů) | 
| Přepnout infrastrukturu | IP adresy Point-to-Point pro účely směrování, rozhraní pro správu vyhrazených přepínačů a adresy zpětné smyčky přiřazené přepínači. | /26 | 
| Infrastruktura | Slouží k Azure Stack interní součásti pro komunikaci. | /24 |
| Privátní | Používá se pro síť úložiště, privátní virtuální IP adresy, kontejnery infrastruktury a další interní funkce. Od 1910 se velikost této podsítě mění na/20. Další podrobnosti najdete v části [privátní síť](#private-network) v tomto článku. | /20 | 
| BMC | Slouží ke komunikaci s BMC na fyzických hostitelích. | /26 | 
| | | |

> [!NOTE]
> Když je systém aktualizován na verzi 1910, výstraha na portálu přihlásí operátorovi ke spuštění nové rutiny PEP **set-AzsPrivateNetwork** pro přidání nového/20 PRIVÁTNÍho adresního prostoru. Pokyny ke spuštění této rutiny najdete v [poznámkách k verzi 1910](release-notes.md) . Další informace a pokyny k výběru privátního ADRESního prostoru/20 najdete v části [privátní síť](#private-network) v tomto článku.

## <a name="network-infrastructure"></a>Síťová infrastruktura

Síťová infrastruktura pro Azure Stack se skládá z několika logických sítí, které jsou nakonfigurované na přepínačích. Následující diagram znázorňuje tyto logické sítě a způsob jejich integrace s přepínači "rozvaděče", řadiče pro správu základní desky (BMC) a hraniční sítě (síť zákazníka).

![Diagram logické sítě a přepínač připojení](media/azure-stack-network/networkdiagram.svg)

### <a name="bmc-network"></a>Síť řadiče pro správu základní desky

Tato síť je vyhrazena pro připojení všech řadičů pro správu základní desky (označovaných také jako BMC nebo procesory služeb) k síti pro správu. Mezi příklady patří: iDRAC, MOP, iBMC a tak dále. Ke komunikaci s jakýmkoli uzlem BMC se používá jenom jeden účet řadiče pro správu základní desky. Je-li k dispozici, je hostitel životního cyklu hardwaru (HLH) umístěn v této síti a může poskytovat software pro správu a údržbu hardwaru určený výrobcem OEM.

HLH také hostuje virtuální počítač nasazení (DVM). DVM se používá během nasazení Azure Stack a po dokončení nasazení se odebere. DVM vyžaduje přístup k internetu ve scénářích propojeného nasazení, aby bylo možné testovat, ověřovat a přistupovat k několika komponentám. Tyto součásti můžou být uvnitř firemní sítě i mimo ni (například NTP, DNS a Azure). Další informace o požadavcích na připojení najdete [v části věnované překladu adres (NAT) v tématu Azure Stack Integrace brány firewall](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Privátní síť

Tato/20 IP adres (4096) je privátní pro Azure Stackou oblast (nejedná se o trasu nad rámec hraničních zařízení Azure Stack systému) a je rozdělená do několika podsítí, tady je několik příkladů:

- **Síť úložiště**: a/25 (128 IP adres), která se používá k podpoře použití prostorových přímých a přenosů úložiště protokolu SMB (Server Message Block) a migrace za provozu virtuálních počítačů.
- **Interní virtuální IP síť**: a/25 síť vyhrazenou pouze pro interní VIP pro nástroj pro vyrovnávání zatížení softwaru.
- **Síť kontejneru**: a/23 (512 IP adres), které jsou vyhrazené jenom pro interní přenosy mezi kontejnery, na kterých běží služby infrastruktury.

Počínaje verzí 1910 **vyžaduje** systém Azure Stack hub další/20 privátní interní IP místo. Tato síť bude privátním systémem Azure Stack (netrasuje se nad rámec hraničních zařízení Azure Stack systému) a je možné ji znovu použít ve více systémech Azure Stack v rámci vašeho datového centra. I když je síť soukromá, aby Azure Stack, nesmí se překrývat s ostatními sítěmi v datacentru. Privátní IP adresa/20 je rozdělená do několika sítí, které umožňují provozování infrastruktury centra Azure Stack v kontejnerech (jak je uvedeno výše v [poznámkách k verzi 1905](release-notes.md?view=azs-1905)). Kromě toho tato nová privátní IP adresa umožňuje nepřetržité úsilí snížit před nasazením požadované IP místo pro směrování. Cílem provozování infrastruktury centra Azure Stack v kontejnerech je optimalizace využití a zvýšení výkonu. Kromě toho privátní IP místo/20 slouží také k povolení průběžného úsilí, které před nasazením omezí požadovaný adresní prostor IP adres. Pokyny k privátnímu adresnímu prostoru IP adres doporučujeme následující: [RFC 1918](https://tools.ietf.org/html/rfc1918).

Pro systémy nasazené před 1910 bude tato/20 podsíť další síť, která se má po aktualizaci na 1910 zadat do systémů. Další síť bude nutné poskytnout systému pomocí rutiny **set-AzsPrivateNetwork** PEP.

> [!NOTE]
> Vstup/20 slouží jako předpoklad pro příští aktualizaci centra Azure Stack po 1910. Když se další aktualizace centra Azure Stack po 1910 vydání a pokusíte se ji nainstalovat, aktualizace se nezdaří, pokud jste nedokončili vstup/20, jak je popsáno v následujících krocích oprav. V portálu pro správu bude k dispozici výstraha, dokud výše uvedené kroky nápravy nejsou dokončeny. V článku věnovaném [integraci sítě Datacenter](azure-stack-network.md#private-network) se dozvíte, jak bude tento nový privátní prostor použit.

**Postup nápravy**: Pokud chcete problém vyřešit, postupujte podle pokynů a [otevřete relaci PEP](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Připravte velikost [privátního interního rozsahu IP adres](azure-stack-network.md#logical-networks) /20 a v relaci PEP spusťte následující rutinu (k dispozici pouze počínaje 1910) pomocí následujícího příkladu: `Set-AzsPrivateNetwork -UserSubnet 100.87.0.0/20`. Pokud se operace provede úspěšně, obdržíte do **Konfigurace přidaný rozsah interní sítě AZS**. Po úspěšném dokončení se výstraha zavře na portálu pro správu. Systém centra Azure Stack se teď může aktualizovat na další verzi.

### <a name="azure-stack-infrastructure-network"></a>Síť Azure Stack infrastruktury
Tato síť/24 je vyhrazená pro interní Azure Stack komponenty, aby mohly komunikovat a vyměňovat data mezi sebou. Tuto podsíť je možné směrovat externě Azure Stack řešení do vašeho datového centra, ale nedoporučujeme používat v této podsíti veřejné nebo internetové IP adresy směrování. Tato síť se inzeruje na hranici, ale většina IP adres je chráněná pomocí seznamů Access Control (ACL). IP adresy povolené pro přístup jsou v malém rozsahu, který je ekvivalentní velikosti až/27 sítě a hostitelských služeb, jako je například [privilegovaný koncový bod (PEP)](azure-stack-privileged-endpoint.md) a [Zálohování Azure Stack](azure-stack-backup-reference.md).

### <a name="public-vip-network"></a>Síť veřejných virtuálních IP adres

Veřejná VIP síť je přiřazena k síťovému adaptéru v Azure Stack. Nejedná se o logickou síť na přepínači. SLB používá fond adres a přiřazuje sítě/32 pro zatížení klientů. V tabulce směrování přepínače jsou tyto/32 IP adresy inzerovány jako dostupná trasa prostřednictvím protokolu BGP. Tato síť obsahuje přístup k externím nebo veřejným IP adresám. Infrastruktura Azure Stack si vyhrazuje prvních 31 adres z této veřejné sítě VIP, zatímco zbytek je využíván virtuálními počítači klienta. Velikost sítě v této podsíti může být v rozsahu od minimálně/26 (64 hostitelů) až do maximálního počtu/22 (1022 hostitelů). Doporučujeme, abyste naplánovali síť/24.

### <a name="switch-infrastructure-network"></a>Přepnout síť infrastruktury

Tato síť/26 je podsíť, která obsahuje podsítě IP adres Point-to-Point/30 (dvě hostitele) a zpětná smyčka, které jsou vyhrazené/32 pro místní správu přepínačů a ID směrovače protokolu BGP. Tento rozsah IP adres musí být směrovatelné mimo Azure Stack řešení do vašeho datového centra. Můžou to být privátní nebo veřejné IP adresy.

### <a name="switch-management-network"></a>Přepnout síť pro správu

Tato síť/29 (šest hostitelských IP adres) je vyhrazená pro připojení portů pro správu přepínačů. Umožňuje vzdálený přístup pro nasazení, správu a řešení potíží. Počítá se ze sítě infrastruktury přepínače uvedené výše.

## <a name="permitted-networks"></a>Povolené sítě

Od 1910 bude mít list nasazení toto nové pole, které umožňuje operátorovi změnit některý seznam řízení přístupu (ACL) s tím, aby povoloval přístup k rozhraním pro správu síťových zařízení a k hostiteli životního cyklu hardwaru (HLH) z rozsahu důvěryhodné sítě Datacenter. . Se změnou seznamu řízení přístupu může operátor dovolit, aby JumpBox virtuální počítače pro správu v rámci určitého rozsahu sítě pro přístup k rozhraní pro správu přepínačů, HLH operačním systému a HLH BMC. Operátor může do tohoto seznamu zadat jednu nebo více podsítí, pokud pole necháte prázdné, bude ve výchozím nastavení odepřen přístup. Tato nová funkce nahrazuje ruční zásah po nasazení, který se používá k popisu na základě [nastavení změnit konkrétní v konfiguraci přepínače Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-customer-defined#access-control-list-updates).

## <a name="next-steps"></a>Další kroky

Seznamte se s plánováním sítě: [ohraničení připojení](azure-stack-border-connectivity.md).