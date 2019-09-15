---
title: Požadavky a doporučení pro ASDK | Microsoft Docs
description: Seznamte se s požadavky na hardware, software a prostředí pro Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 05/13/2019
ms.openlocfilehash: 0e5a4e41e4650de8e3cebe7d33b9638890d5bd99
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974571"
---
# <a name="asdk-requirements-and-considerations"></a>Požadavky a předpoklady pro ASDK

Před nasazením Azure Stack Development Kit (ASDK) se ujistěte, že váš hostitelský počítač ASDK splňuje požadavky popsané v tomto článku.

## <a name="hardware"></a>Hardware

| Komponenta | Minimální | Doporučené |
| --- | --- | --- |
| Diskové jednotky: Operační systém |1 disk s operačním systémem s minimálně 200 GB, který je k dispozici pro systémový oddíl (SSD nebo HDD). |1 disk s operačním systémem, který má minimálně 200 GB, je k dispozici pro systémový oddíl (SSD nebo HDD). |
| Diskové jednotky: Data pro obecné vývojové sady<sup>*</sup>  |Čtyři disky. Každý disk poskytuje minimálně 240 GB kapacity (SSD nebo HDD). Používají se všechny dostupné disky. |Čtyři disky. Každý disk poskytuje minimálně 400 GB kapacity (SSD nebo HDD). Používají se všechny dostupné disky. |
| Výpočetní Procesor |Duální soket: 16 fyzických jader (celkem). |Duální soket: 20 fyzických jader (celkem). |
| Výpočetní Memory (Paměť) |192 – GB RAM. |256 – GB RAM. |
| Výpočetní SYSTÉMU BIOS |Je zapnutá technologie Hyper-V (s podporou SLAT). |Je zapnutá technologie Hyper-V (s podporou SLAT). |
| Sítě NIC |Certifikace systému Windows Server 2012 R2. Nejsou vyžadovány žádné specializované funkce. | Certifikace systému Windows Server 2012 R2. Nejsou vyžadovány žádné specializované funkce. |
| Hardwarová certifikace loga |[Certifikováno pro Windows Server 2012 R2](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0). |[Certifikováno pro Windows Server 2016](https://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0). |

<sup>*</sup>Pokud plánujete přidat spoustu [položek z webu Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) z Azure, budete potřebovat víc než tuto doporučenou kapacitu.

### <a name="hardware-notes"></a>Poznámky k hardwaru

**Konfigurace datové jednotky na disku:** Všechny datové jednotky musí být stejného typu (All SAS, All SATA nebo All NVMe) a kapacitu. Pokud použijete disky SAS, musí být diskové jednotky připojené pomocí jedné cesty (žádné funkce MPIO, podpora více cest je zajištěna).

**Možnosti konfigurace adaptéru HBA**

* Doporučeno Jednoduchý adaptér HBA.
* V režimu "průchozího" musí být nakonfigurován adaptér HBA RAID.
* Jednotky HBA RAID – disky by měly být nakonfigurované jako jednotky RAID-0 na jednom disku.

**Podporované kombinace typu sběrnice a média**

* Pevný disk SATA
* Pevný disk SAS
* Pevný disk RAID
* RAID SSD (Pokud je typ média Neurčen/neznámý<sup>*</sup>)
* SATA SSD + pevný disk SATA
* SAS SSD + pevný disk SAS
* NVMe

<sup>*</sup>Řadiče RAID bez průchozí funkce nerozpoznají typ média. Tato řadiče označují pevný disk i SSD jako neurčený. V takovém případě se jednotka SSD používá jako trvalé úložiště namísto ukládání zařízení do mezipaměti. Proto můžete ASDK nasadit na tyto SSD.

**Příklad adaptérů HBA**: Adaptér LSI 9207-8i, LSI-9300-8i nebo LSI-9265-8i v předávacím režimu.

Dostupné jsou ukázkové OEM konfigurace.

### <a name="storage-resiliency-for-the-asdk"></a>Odolnost úložiště pro ASDK

ASDK jako jeden uzel není navržený pro ověřování provozní redundance Azure Stack integrovaného systému. Úroveň základní redundance úložiště ASDK ale můžete zvýšit optimální kombinací jednotek HDD a SSD. Můžete nasadit konfiguraci se dvěma způsoby zrcadlení, podobně jako RAID1, a ne jednoduchou konfiguraci odolnou proti chybám, která je podobná RAID0. Pro základní konfiguraci Prostory úložiště s přímým přístupem použijte dostatek kapacity, typ a počet jednotek.

Postup použití konfigurace se dvěma způsoby zrcadlení pro odolnost úložiště:

- Potřebujete kapacitu HDD v systému o více než 2 terabajty.
- Pokud ve svém ASDK nemáte SSD, budete pro konfiguraci obousměrného zrcadlení potřebovat aspoň osm HDD.
- Pokud jste SSD ve své ASDK spolu s HDD, budete potřebovat aspoň pět HDD. Nicméně se doporučuje šest HHDs. Pro šest HDD se také doporučuje mít alespoň tři odpovídající SSD v systému, abyste měli k dispozici jeden disk mezipaměti (SSD), který bude obsluhovat dvě jednotky kapacity (HDD).

Příklad konfigurace se dvěma způsoby zrcadlení:

- Osm HDD
- 3 jednotky SSD/šest HDD
- Čtyři pevný disk SSD/osm

## <a name="operating-system"></a>Operační systém
|  | **Požadavky** |
| --- | --- |
| **Verze operačního systému** |Windows Server 2016 nebo novější. Verze operačního systému není před zahájením nasazení kritická, protože se hostitelský počítač spouští do virtuálního pevného disku, který je součástí instalace Azure Stack. Operační systém a všechny požadované opravy jsou již do bitové kopie integrovány. Nepoužívejte žádné klíče k aktivaci žádné instance Windows serveru používané v ASDK. |

> [!TIP]
> Po instalaci operačního systému můžete pomocí [Nástroje pro kontrolu nasazení Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) ověřit, že hardware splňuje všechny požadavky.

## <a name="account-requirements"></a>Požadavky na účet
Obvykle nasadíte ASDK s připojením k Internetu, kde se můžete připojit k Microsoft Azure. V takovém případě musíte nakonfigurovat účet Azure Active Directory (Azure AD) pro nasazení ASDK.

Pokud vaše prostředí není připojené k Internetu, nebo nechcete používat Azure AD, můžete Azure Stack nasadit pomocí Active Directory Federation Services (AD FS) (AD FS). ASDK zahrnuje vlastní instance AD FS a Active Directory Domain Services. Pokud nasadíte pomocí této možnosti, nemusíte nastavovat účty před časem.

> [!NOTE]
> Pokud nasadíte pomocí možnosti AD FS, je nutné znovu nasadit Azure Stack a přepnout do Azure AD.

### <a name="azure-active-directory-accounts"></a>Účty Azure Active Directory
Pokud chcete nasadit Azure Stack pomocí účtu Azure AD, musíte před spuštěním skriptu PowerShellu pro nasazení připravit účet Azure AD. Tento účet se stal globálním správcem pro tenanta Azure AD. Slouží ke zřízení a delegování aplikací a instančních objektů pro všechny Azure Stack služby, které komunikují se službou Azure AD a Graph API. Používá se také jako vlastník předplatného výchozího poskytovatele (který můžete později změnit). Pomocí tohoto účtu se můžete přihlásit k portálu správce systému Azure Stack.

1. Vytvořte účet Azure AD, který je správcem adresáře pro aspoň jednu službu Azure AD. Pokud už účet máte, můžete ho použít. V opačném případě můžete vytvořit zdarma na [https://azure.microsoft.com/free/](https://azure.microsoft.com/free/) <https://go.microsoft.com/fwlink/?LinkID=717821> místo (v Číně). Pokud plánujete pozdější [registraci Azure Stack v Azure](asdk-register.md), musíte mít také předplatné v tomto nově vytvořeném účtu.
   
    Uložte tyto přihlašovací údaje pro použití jako správce služby. Tento účet může konfigurovat a spravovat cloudy prostředků, uživatelské účty, plány tenantů, kvóty a ceny. Na portálu můžou vytvářet cloudy webů, privátní cloudy virtuálních počítačů, vytvářet plány a spravovat předplatná uživatelů.
1. Ve službě Azure AD vytvořte aspoň jeden testovací uživatelský účet, abyste se mohli k ASDK přihlašovat jako tenant.
   
   | **Účet Azure Active Directory** | **Doložen?** |
   | --- | --- |
   | Pracovní nebo školní účet s platným globálním předplatným Azure |Ano |
   | Účet Microsoft s platným globálním předplatným Azure |Ano |
   | Pracovní nebo školní účet s platným Čínou – předplatné Azure |Ano |
   | Pracovní nebo školní účet s platnými předplatné Azure pro státní správu USA |Ano |

Po nasazení se oprávnění globálního správce služby Azure AD nevyžadují. Některé operace ale můžou vyžadovat přihlašovací údaje globálního správce. Příklady takových operací zahrnují skript instalačního programu poskytovatele prostředků nebo novou funkci, která vyžaduje udělení oprávnění. Můžete buď dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatný účet globálního správce, který je vlastníkem *výchozího předplatného poskytovatele*.

## <a name="network"></a>Síť
### <a name="switch"></a>Přepínač
Jeden dostupný port na přepínači pro ASDK počítač.  

Počítač ASDK podporuje připojení k portu pro přístup k přepínači nebo k portu kmene. Přepínač nevyžaduje žádné specializované funkce. Pokud používáte port kmene nebo pokud potřebujete nakonfigurovat ID sítě VLAN, musíte zadat ID sítě VLAN jako parametr nasazení.

### <a name="subnet"></a>Subnet
Nepřipojujte počítač ASDK k těmto podsítím:

* 192.168.200.0/24
* 192.168.100.0/27
* 192.168.101.0/26
* 192.168.102.0/24
* 192.168.103.0/25
* 192.168.104.0/25

Tyto podsítě jsou vyhrazené pro interní sítě v rámci prostředí ASDK.

### <a name="ipv4ipv6"></a>IPv4/IPv6
Podporovaný je jenom protokol IPv4. Nemůžete vytvářet sítě IPv6.

### <a name="dhcp"></a>DHCP
Ujistěte se, že je v síti k dispozici server DHCP, ke kterému se síťová karta připojuje. Pokud není služba DHCP k dispozici, musíte připravit další statickou síť IPv4 kromě těch, kterou používá hostitel. Tuto IP adresu a bránu zadejte jako parametr nasazení.

### <a name="internet-access"></a>Přístup k internetu
Azure Stack vyžaduje přístup k Internetu, a to buď přímo, nebo prostřednictvím transparentního proxy serveru. Azure Stack nepodporuje konfiguraci webového proxy serveru, aby bylo možné povolit přístup k Internetu. IP adresa hostitele i nová IP adresa přiřazená k AzS-BGPNAT01 (pomocí protokolu DHCP nebo statické IP adresy) musí mít přístup k Internetu. Porty 80 a 443 se používají v doménách graph.windows.net a login.microsoftonline.com.


## <a name="next-steps"></a>Další kroky

- [Stáhněte si balíček pro nasazení ASDK](asdk-download.md).
- Další informace o Prostory úložiště s přímým přístupem najdete v tématu [prostory úložiště s přímým přístupem Overview](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

