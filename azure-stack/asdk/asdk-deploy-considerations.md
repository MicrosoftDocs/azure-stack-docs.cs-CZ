---
title: Požadavky Azure Stack Development Kit (ASDK) nasazení | Dokumentace Microsoftu
description: Projděte si prostředí a hardwarové požadavky pro Azure Stack Development Kit (ASDK).
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
ms.date: 05/13/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 05/13/2019
ms.openlocfilehash: 9cb349ec19edd493ca994b406b9311fe27bed242
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712230"
---
# <a name="azure-stack-deployment-planning-considerations"></a>Co zvážit při plánování nasazení Azure Stack

Než nasadíte Azure Stack Development Kit (ASDK), zkontrolujte, zda že splňuje požadavky popsané v tomto článku hostitelského počítače development kit.

## <a name="hardware"></a>Hardware

| Komponenta | Minimální | Doporučené |
| --- | --- | --- |
| Diskové jednotky: Operační systém |1 disk s operačním systémem s alespoň 200 GB místa pro systémový oddíl (SSD nebo HDD) |Jeden disk s operačním systémem a alespoň 200 GB místa pro systémový oddíl (SSD nebo pevný disk) |
| Diskové jednotky: Obecný vývoj sady dat<sup>*</sup>  |Čtyři disky. Každý disk nabízí minimálně 240 GB místa (SSD nebo HDD). Všechny dostupné disky se používají. |Čtyři disky. Každý disk nabízí minimálně 400 GB místa (SSD nebo HDD). Všechny dostupné disky se používají. |
| COMPUTE: Procesor |Duální soket: 16 fyzických jader (celkem) |Duální soket: 20 fyzických jader (celkem) |
| COMPUTE: Memory (Paměť) |192 GB PAMĚTI RAM |256 GB PAMĚTI RAM |
| COMPUTE: SYSTÉMU BIOS |Povolená technologie Hyper-V (s podporou SLAT) |Povolená technologie Hyper-V (s podporou SLAT) |
| Síť: NIC |Certifikace pro Windows Server 2012 R2. Specializované funkce se nepožadují |Certifikace pro Windows Server 2012 R2. Specializované funkce se nepožadují |
| Hardwarová certifikace loga |[Certifikované pro systém Windows Server 2012 R2](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certifikované pro systém Windows Server 2016](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |

<sup>*</sup> Potřebujete víc než to doporučuje kapacitu, když plánujete přidat řadu [položky marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) z Azure.

### <a name="hardware-notes"></a>Poznámky k hardwaru

**Konfigurace datových disků:** Všechny datové jednotky musí být stejného typu (všechny SAS, všechny SATA nebo všechna NVMe) a kapacitu. Pokud použijete disky SAS, musí být diskové jednotky připojené pomocí jedné cesty (žádné funkce MPIO, podpora více cest je zajištěna).

**Možnosti konfigurace adaptéru HBA**

* (Upřednostňované) Jednoduchý adaptér HBA
* RAID HBA – adaptér musí být nakonfigurovány v režimu "předávání"
* RAID HBA – disky musí být konfigurované jako jeden Disk, RAID-0

**Podporované sběrnice a typů médií kombinace**

* Pevný disk SATA
* Pevný disk SAS
* Pevný disk RAID
* RAID SSD (Pokud je typ média Neurčeno/neznámé<sup>*</sup>)
* SATA SSD + pevný disk SATA
* SAS SSD + pevný disk SAS
* NVMe

<sup>*</sup> Řadiče RAID bez možnosti průchodu nerozpoznají typ média. Tyto řadiče označit pevný disk i SSD jako Neurčeno. V takovém případě se používá SSD jako trvalé úložiště místo mezipaměťových zařízení. Proto můžete nasadit sada na tyto disky SSD.

**Příklad HBA**: Adaptér LSI 9207-8i, LSI-9300-8i nebo LSI-9265-8i v režimu průchodu

Dostupné jsou ukázkové OEM konfigurace.

### <a name="storage-resiliency-for-the-asdk"></a>Odolnost proti chybám úložiště pro ASDK

Jako jeden uzel systému ASDK není určená pro produkční redundance systémech pro Azure Stack integrované ověřování. Však můžete zvýšit úroveň základní redundance úložiště ASDK prostřednictvím ideální kombinaci disků HDD a SDD jednotky. Můžete nasadit konfigurace Dvoucestný zrcadlový svazek, podobně jako RAID1, nikoli jednoduchý odolnost proti chybám konfigurace, která se podobá 0. Pomocí základní konfigurace prostorů úložiště s přímým dostatek kapacity, typ a počet jednotek.

Pro účely konfigurace Dvoucestný zrcadlový svazek úložiště odolnost proti chybám:

- Kapacita HDD v systému větší než dvě terabajtů.
- Pokud nemáte k dispozici vlastnosti jednotek SSD ve vašich ASDK, musíte alespoň osm pevné disky pro dvoucestné zrcadlové svazky konfiguraci.
- Pokud máte disky SSD v ASDK, spolu s pevné disky, budete potřebovat aspoň pět HDD. Šest HHDs se však doporučuje. Šest pevné disky by také se doporučuje mít odpovídající aspoň tři jednotky SSD v systému, abyste měli jeden mezipaměti (SSD Solid-State Drive) slouží dvě kapacitu disků (HDD).

Příklad konfigurace Dvoucestný zrcadlový svazek:

- Osm HDD
- Tři SSD / šest pevný disk
- Čtyři SSD / osm pevný disk

## <a name="operating-system"></a>Operační systém
|  | **Požadavky** |
| --- | --- |
| **Verze operačního systému** |Windows Server 2016 nebo novější. Verze operačního systému není tak důležitý před zahájením nasazení, jak bude spouštěcí hostitelského počítače na virtuální pevný disk, který je součástí instalace služby Azure Stack. Operační systém a všechny požadované opravy jsou již integrovaná image. Nepoužívejte žádné klíče k aktivaci všechny instance Windows serveru použít development Kit. |

> [!TIP]
> Po instalaci operačního systému, můžete použít [kontrola nasazení Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) potvrďte, že váš hardware splňuje všechny požadavky.

## <a name="account-requirements"></a>Požadavky na účet
Vývojová sada zpravidla nasazujete s připojením k Internetu, kde se můžete připojit k Microsoft Azure. V takovém případě musíte nakonfigurovat účet služby Azure Active Directory (Azure AD) k nasazení vývojové sady.

Pokud vaše prostředí není připojený k Internetu, nebo nechcete používat Azure AD, můžete nasadit Azure Stack pomocí služby Active Directory Federation Services (AD FS). Vývojová sada obsahuje vlastní instance služby AD FS a Active Directory Domain Services. Pokud provádíte nasazení s použitím tuto možnost, není nutné nastavení účtů předem.

> [!NOTE]
> Pokud nasadíte pomocí služby AD FS, je nutné znovu nasadit Azure Stack pro přepnutí do služby Azure AD.

### <a name="azure-active-directory-accounts"></a>Účty služby Azure Active Directory
Nasazení Azure Stack pomocí účtu Azure AD, musíte připravit účet Azure AD, před spuštěním Powershellový skript nasazení. Tento účet stane globálním správcem tenanta Azure AD. Používá se ke zřízení a delegování aplikace a instanční objekty pro všechny služby Azure Stack, které pracují s Azure Active Directory a rozhraní Graph API. Používá se také jako vlastník výchozí předplatné poskytovatele (který můžete později změnit). Na portálu správce systému služby Azure Stack se můžete přihlásit pomocí tohoto účtu.

1. Vytvoření účtu služby Azure AD, který je správcem adresáře nejméně jedné služby Azure AD. Pokud už účet máte, můžete ho použít. Jinak můžete jej vytvořit zdarma na [ https://azure.microsoft.com/free/ ](https://azure.microsoft.com/free/) (v Číně, navštivte <https://go.microsoft.com/fwlink/?LinkID=717821> místo). Pokud budete chtít později [registraci Azure Stack v Azure](asdk-register.md), také musíte mít předplatné v tomto nově vytvořeném účtu.
   
    Uložte tyto přihlašovací údaje pro použití jako správce služeb. Tento účet můžete konfigurovat a spravovat cloudy prostředků, uživatelské účty, plány tenantů, kvóty a ceny. Na portálu může vytvářet webové cloudy, soukromé cloudy pro virtuální počítače a plány a spravovat předplatné uživatelů.
1. Vytvořte aspoň jeden testovací uživatelský účet ve službě Azure AD tak, aby se můžete přihlásit jako tenant development Kit.
   
   | **Účet Azure Active Directory** | **Podporuje?** |
   | --- | --- |
   | Pracovní nebo školní účet s platným globální předplatným Azure |Ano |
   | Account Microsoft s platným globální předplatným Azure |Ano |
   | Pracovní nebo školní účet s platným čínským předplatným Azure |Ano |
   | Pracovní nebo školní účet s platným US Government předplatným Azure |Ano |

Po nasazení není potřeba oprávnění globálního správce Azure Active Directory. Některé operace však může vyžadovat přihlašovací údaje globálního správce. Například skript instalační program zprostředkovatele prostředků nebo nová funkce vyžaduje oprávnění bylo uděleno. Můžete dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatné globální správce účtu, který je vlastníkem *výchozí předplatné poskytovatele*.

## <a name="network"></a>Síť
### <a name="switch"></a>Přepínač
Jeden dostupný port na přepínači počítače development kit.  

Development kit machine podporuje připojení k portu přepínače přístup nebo kmenovému portu. Přepínač nevyžaduje žádné specializované funkce. Pokud používáte kmenový port nebo pokud potřebujete nakonfigurovat ID sítě VLAN, musíte ID sítě VLAN zadat jako parametr nasazení.

### <a name="subnet"></a>Podsíť
Nepřipojujte počítač development kit pro tyto podsítě:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Tyto podsítě jsou vyhrazené pro interní sítě v rámci vývojového prostředí sady.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Podporovaný je jenom protokol IPv4. Nemůžete vytvořit sítě IPv6.

### <a name="dhcp"></a>DHCP
Ověřte si dostupnost serveru DHCP v síti, do které se síťová karta připojuje. Pokud server DHCP není dostupný, připravte další statickou síť IPv4 (kromě té, kterou používá hostitel). Tuto IP adresu a bránu zadejte jako parametr nasazení.

### <a name="internet-access"></a>Přístup k internetu
Azure Stack vyžaduje přístup k Internetu, buď přímo nebo prostřednictvím transparentní proxy server. Azure Stack nepodporuje konfigurace webového proxy serveru na povolení přístupu k Internetu. IP adresa hostitele i nová IP adresa přiřazená AzS-BGPNAT01 (pomocí DHCP nebo statická IP adresa), musí mít přístup k Internetu. V rámci domén graph.windows.net a login.microsoftonline.com se používají porty 80 a 443.


## <a name="next-steps"></a>Další postup

- [Stáhněte si balíček pro nasazení ASDK](asdk-download.md)
- Další informace o prostory úložiště – přímé, naleznete v tématu [přehled prostorů úložiště s přímým](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

