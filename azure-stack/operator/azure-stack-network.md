---
title: Plánování integrace sítě pro Azure Stack | Microsoft Docs
description: Naučte se plánovat integraci sítě Datacenter pomocí Azure Stack integrovaných systémů.
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
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 4894fb7184944095d968d08e2d668912a78119d4
ms.sourcegitcommit: ef7efcde76d1d7875ca1c882afebfd6a27f1c686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72888036"
---
# <a name="network-integration-planning-for-azure-stack"></a>Plánování integrace sítě pro Azure Stack

Tento článek poskytuje Azure Stack informace o infrastruktuře sítě, které vám pomůžou rozhodnout, jak nejlépe integrovat Azure Stack do stávajícího síťového prostředí. 

> [!NOTE]
> K překladu externích názvů DNS z Azure Stack (například www\.bing.com) je potřeba poskytnout servery DNS pro přeposílání požadavků DNS. Další informace o Azure Stack požadavcích DNS najdete v tématu věnovaném [integraci Azure Stack Datacenter – DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Návrh fyzické sítě

Řešení Azure Stack pro zajištění podpory svého provozu a služeb vyžaduje odolnou a vysoce dostupnou fyzickou infrastrukturu. Odchozí připojení z paměti k přepínačům ohraničení jsou omezená na SFP28a SFP + nebo na více než 1 GB, 10 GB nebo rychlosti 25 GB. Obraťte se na dodavatele hardwaru OEM (Original Equipment Manufacturer) pro dostupnost. Následující diagram znázorňuje náš doporučený návrh:

![Doporučený Azure Stack návrh sítě](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logické sítě

Logické sítě představuje abstrakci základní fyzické síťové infrastruktury. Slouží k organizování a zjednodušení přiřazení sítě pro hostitele, virtuální počítače a služby. V rámci vytváření logických sítí se vytvoří síťové lokality pro definování virtuálních místních sítí (VLAN), podsítí protokolu IP a párů podsítě protokolu IP/sítě VLAN, které jsou přidružené k logické síti v každém fyzickém umístění.

Následující tabulka uvádí logické sítě a přidružené rozsahy podsítí IPv4, které je nutné naplánovat:

| Logická síť | Popis | Velikost | 
| -------- | ------------- | ------------ | 
| Veřejná virtuální IP adresa | Azure Stack používá celkem 31 adres z této sítě. Osm veřejných IP adres se používá pro malou sadu Azure Stack služeb a zbývající jsou používány virtuálními počítači klienta. Pokud plánujete použít App Service a poskytovatele prostředků SQL, použijí se 7 dalších adres. Zbývajících 15 IP adres se rezervuje pro budoucí služby Azure. | /26 (62 hostitelů)-/22 (1022 hostitelů)<br><br>Doporučené =/24 (254 hostitelů) | 
| Přepnout infrastrukturu | IP adresy Point-to-Point pro účely směrování, rozhraní pro správu vyhrazených přepínačů a adresy zpětné smyčky přiřazené přepínači. | za 26 | 
| Infrastruktura | Slouží k Azure Stack interní součásti pro komunikaci. | za 24 |
| Privátní | Používá se pro síť úložiště a privátní virtuální IP adresy. | za 24 | 
| ŘADIČI | Slouží ke komunikaci s BMC na fyzických hostitelích. | za 26 | 
| | | |

## <a name="network-infrastructure"></a>Síťová infrastruktura

Síťová infrastruktura pro Azure Stack se skládá z několika logických sítí, které jsou nakonfigurované na přepínačích. Následující diagram znázorňuje tyto logické sítě a způsob jejich integrace s přepínači "rozvaděče", řadiče pro správu základní desky (BMC) a hraniční sítě (síť zákazníka).

![Diagram logické sítě a přepínač připojení](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Síť řadiče pro správu základní desky

Tato síť je vyhrazena pro připojení všech řadičů pro správu základní desky (označovaných také jako BMC nebo procesory služeb) k síti pro správu. Mezi příklady patří: iDRAC, MOP, iBMC a tak dále. Ke komunikaci s jakýmkoli uzlem BMC se používá jenom jeden účet řadiče pro správu základní desky. Je-li k dispozici, je hostitel životního cyklu hardwaru (HLH) umístěn v této síti a může poskytovat software pro správu a údržbu hardwaru určený výrobcem OEM.

HLH také hostuje virtuální počítač nasazení (DVM). DVM se používá během nasazení Azure Stack a po dokončení nasazení se odebere. DVM vyžaduje přístup k internetu ve scénářích propojeného nasazení, aby bylo možné testovat, ověřovat a přistupovat k několika komponentám. Tyto součásti můžou být uvnitř firemní sítě i mimo ni (například NTP, DNS a Azure). Další informace o požadavcích na připojení najdete [v části věnované překladu adres (NAT) v tématu Azure Stack Integrace brány firewall](azure-stack-firewall.md#network-address-translation).

### <a name="private-network"></a>Privátní síť

Tato síť/24 (254 IP adres hostitele) je privátní pro Azure Stack oblasti (nerozšiřuje se nad rámec hraničních zařízení Azure Stack oblasti) a je rozdělená do dvou podsítí:

- **Síť úložiště**: a/25 (126 IP adres hostitele), která se používá k podpoře použití prostorových přímých a přenosů úložiště protokolu SMB (Server Message Block) a migrace virtuálního počítače za provozu.
- **Interní virtuální IP síť**: a/25 síť vyhrazenou pouze pro interní VIP pro nástroj pro vyrovnávání zatížení softwaru.

### <a name="azure-stack-infrastructure-network"></a>Síť Azure Stack infrastruktury
Tato síť/24 je vyhrazená pro interní Azure Stack komponenty, aby mohly komunikovat a vyměňovat data mezi sebou. Tuto podsíť je možné směrovat externě Azure Stack řešení do vašeho datového centra, ale nedoporučujeme používat v této podsíti veřejné nebo internetové IP adresy směrování. Tato síť se inzeruje na hranici, ale většina IP adres je chráněná pomocí seznamů Access Control (ACL). IP adresy povolené pro přístup jsou v malém rozsahu, který je ekvivalentní velikosti až/27 sítě a hostitelských služeb, jako je například [privilegovaný koncový bod (PEP)](azure-stack-privileged-endpoint.md) a [Zálohování Azure Stack](azure-stack-backup-reference.md).

### <a name="public-vip-network"></a>Síť veřejných virtuálních IP adres

Veřejná VIP síť je přiřazena k síťovému adaptéru v Azure Stack. Nejedná se o logickou síť na přepínači. SLB používá fond adres a přiřazuje sítě/32 pro zatížení klientů. V tabulce směrování přepínače jsou tyto/32 IP adresy inzerovány jako dostupná trasa prostřednictvím protokolu BGP. Tato síť obsahuje přístup k externím nebo veřejným IP adresám. Infrastruktura Azure Stack si vyhrazuje prvních 31 adres z této veřejné sítě VIP, zatímco zbytek je využíván virtuálními počítači klienta. Velikost sítě v této podsíti může být v rozsahu od minimálně/26 (64 hostitelů) až do maximálního počtu/22 (1022 hostitelů). Doporučujeme, abyste naplánovali síť/24.

### <a name="switch-infrastructure-network"></a>Přepnout síť infrastruktury

Tato síť/26 je podsíť, která obsahuje podsítě IP adres Point-to-Point/30 (dvě hostitele) a zpětná smyčka, které jsou vyhrazené/32 pro místní správu přepínačů a ID směrovače protokolu BGP. Tento rozsah IP adres musí být směrovatelné mimo Azure Stack řešení do vašeho datového centra. Můžou to být privátní nebo veřejné IP adresy.

### <a name="switch-management-network"></a>Přepnout síť pro správu

Tato síť/29 (šest hostitelských IP adres) je vyhrazená pro připojení portů pro správu přepínačů. Umožňuje vzdálený přístup pro nasazení, správu a řešení potíží. Počítá se ze sítě infrastruktury přepínače uvedené výše.

## <a name="next-steps"></a>Další kroky

Seznamte se s plánováním sítě: [ohraničení připojení](azure-stack-border-connectivity.md).
