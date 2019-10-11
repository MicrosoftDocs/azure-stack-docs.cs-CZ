---
title: Předpoklady pro integraci sítě pro integrované systémy Azure Stack | Microsoft Docs
description: Přečtěte si, co můžete udělat k naplánování integrace sítě Datacenter pomocí Azure Stack s více uzly.
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
ms.openlocfilehash: dca5d863a046ec225b4d34c8cf5917153a3a5785
ms.sourcegitcommit: 12034a1190d52ca2c7d3f05c8c096416120d8392
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037966"
---
# <a name="network-connectivity"></a>Připojení k síti
Tento článek poskytuje Azure Stack informace o infrastruktuře sítě, které vám pomůžou rozhodnout, jak nejlépe integrovat Azure Stack do stávajícího síťového prostředí. 

> [!NOTE]
> Chcete-li přeložit externí názvy DNS z Azure Stack (například www\.bing.com), je nutné poskytnout servery DNS pro přeposílání požadavků DNS. Další informace o Azure Stack požadavcích DNS najdete v tématu věnovaném [integraci Azure Stack Datacenter – DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Návrh fyzické sítě
Řešení Azure Stack pro zajištění podpory svého provozu a služeb vyžaduje odolnou a vysoce dostupnou fyzickou infrastrukturu. Odchozí připojení z paměti k přepínačům ohraničení jsou omezená na SFP28a SFP + nebo na více než 1 GB, 10 GB nebo rychlosti 25 GB. Obraťte se na dodavatele hardwaru OEM (Original Equipment Manufacturer) pro dostupnost. Následující diagram znázorňuje náš doporučený návrh:

![Doporučený Azure Stack návrh sítě](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Logické sítě
Logické sítě představuje abstrakci základní fyzické síťové infrastruktury. Slouží k organizování a zjednodušení přiřazení sítě pro hostitele, virtuální počítače a služby. V rámci vytváření logických sítí se vytvoří síťové lokality pro definování virtuálních místních sítí (VLAN), podsítí protokolu IP a párů podsítě protokolu IP/sítě VLAN, které jsou přidružené k logické síti v každém fyzickém umístění.

Následující tabulka uvádí logické sítě a přidružené rozsahy podsítí IPv4, které je nutné naplánovat:

| Logické sítě | Popis | Size | 
| -------- | ------------- | ------------ | 
| Veřejná virtuální IP adresa | Azure Stack používá celkem 31 adres z této sítě. Osm veřejných IP adres se používá pro malou sadu Azure Stack služeb a zbývající jsou používány virtuálními počítači klienta. Pokud plánujete použít App Service a poskytovatele prostředků SQL, použijí se 7 dalších adres. Zbývajících 15 IP adres se rezervuje pro budoucí služby Azure. | /26 (62 hostitelů)-/22 (1022 hostitelů)<br><br>Doporučené =/24 (254 hostitelů) | 
| Přepnout infrastrukturu | IP adresy Point-to-Point pro účely směrování, rozhraní pro správu vyhrazených přepínačů a adresy zpětné smyčky přiřazené přepínači. | /26 | 
| Infrastruktura | Slouží k Azure Stack interní součásti pro komunikaci. | /24 |
| Soukromé | Používá se pro síť úložiště a privátní virtuální IP adresy. | /24 | 
| BMC | Slouží ke komunikaci s BMC na fyzických hostitelích. | /26 | 
| | | |

## <a name="network-infrastructure"></a>Síťová infrastruktura
Síťová infrastruktura pro Azure Stack se skládá z několika logických sítí, které jsou nakonfigurované na přepínačích. Následující diagram znázorňuje tyto logické sítě a způsob jejich integrace s přepínači "rozvaděče", řadiče pro správu základní desky (BMC) a hraniční sítě (síť zákazníka).

![Diagram logické sítě a přepínač připojení](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Síť řadiče pro správu základní desky
Tato síť je vyhrazena pro připojení všech řadičů pro správu základní desky (označovaných také jako procesory služeb, například iDRAC, MOP, iBMC atd.), do sítě pro správu. Ke komunikaci s jakýmkoli uzlem BMC se používá jenom jeden účet řadiče pro správu základní desky. Je-li k dispozici, je hostitel životního cyklu hardwaru (HLH) umístěn v této síti a může poskytovat software pro správu a údržbu hardwaru určený výrobcem OEM. 

HLH také hostuje virtuální počítač nasazení (DVM). DVM se používá během nasazení Azure Stack a po dokončení nasazení se odebere. DVM vyžaduje přístup k internetu ve scénářích propojeného nasazení, aby bylo možné testovat, ověřovat a přistupovat k několika komponentám. Tyto součásti můžou být uvnitř firemní sítě i mimo ni. například NTP, DNS a Azure. Další informace o požadavcích na připojení najdete [v části věnované překladu adres (NAT) v tématu Azure Stack Integrace brány firewall](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Privátní síť
Tato síť/24 (254 IP adres hostitele) je privátní pro Azure Stack oblasti (nerozšiřuje se nad rámec hraničních zařízení Azure Stack oblasti) a je rozdělená do dvou podsítí:

- **Síť úložiště**. Síť a/25 (126 IP adres hostitele), která se používá k podpoře použití prostorových přímých a přenosů úložiště protokolu SMB (Server Message Block) a migrace virtuálních počítačů za provozu. 
- **Interní virtuální IP síť**. Síť a/25 vyhrazená pouze pro interní VIP pro nástroj pro vyrovnávání zatížení softwaru.

### <a name="azure-stack-infrastructure-network"></a>Síť Azure Stack infrastruktury
Tato síť/24 je vyhrazená pro interní Azure Stack komponenty, aby mohly komunikovat a vyměňovat data mezi sebou. Tato podsíť vyžaduje IP adresy směrování, ale je v rámci řešení soukromá, a to pomocí seznamů Access Control (ACL). Neočekává se, že bude směrován nad rámec přepínačů s výjimkou malých rozsahů, které jsou v rozsahu velikosti až/27 sítě využívané některými těmito službami, když vyžadují přístup k externím prostředkům a/nebo Internetu. 

### <a name="public-vip-network"></a>Síť veřejných virtuálních IP adres
Veřejná VIP síť je přiřazena k síťovému adaptéru v Azure Stack. Nejedná se o logickou síť na přepínači. SLB používá fond adres a přiřazuje sítě/32 pro zatížení klientů. V tabulce směrování přepínače jsou tyto/32 IP adresy inzerovány jako dostupná trasa prostřednictvím protokolu BGP. Tato síť obsahuje přístup k externím nebo veřejným IP adresám. Infrastruktura Azure Stack si vyhrazuje prvních 31 adres z této veřejné sítě VIP, zatímco zbytek je využíván virtuálními počítači klienta. Velikost sítě v této podsíti může být v rozsahu od minimálně/26 (64 hostitelů) až do maximálního počtu/22 (1022 hostitelů), doporučujeme, abyste naplánovali síť/24.

### <a name="switch-infrastructure-network"></a>Přepnout síť infrastruktury
Tato síť/26 je podsíť, která obsahuje podsítě IP adres Point-to-Point IP/30 (2 hostitele) a zpětnou smyčku, které jsou vyhrazené/32 pro místní správu přepínačů a ID směrovače protokolu BGP. Tento rozsah IP adres musí být směrovat externě Azure Stack řešení do vašeho datového centra, můžou to být privátní nebo veřejné IP adresy.

### <a name="switch-management-network"></a>Přepnout síť pro správu
Tato síť/29 (6 hostitelských IP adres) je vyhrazená pro připojení portů pro správu přepínačů. Umožňuje vzdálený přístup pro nasazení, správu a řešení potíží. Počítá se ze sítě infrastruktury přepínače uvedené výše.




## <a name="next-steps"></a>Další kroky
[Připojení k okraji](azure-stack-border-connectivity.md)
