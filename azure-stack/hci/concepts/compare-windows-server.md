---
title: Porovnání Azure Stack HCL pro Azure Stack hub a Windows Server
description: Toto téma vám pomůže určit, jestli je pro vaši organizaci nejvhodnější Azure Stack HCL, Azure Stack hub nebo Windows Server.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/22/2020
ms.openlocfilehash: a4429113088497fd6a7c887a65f524a7495c6b74
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737806"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub-and-windows-server"></a>Porovnání Azure Stack HCL pro Azure Stack hub a Windows Server

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019; Centrum Azure Stack

Mnoho zákazníků si může zajímat, jestli jsou Windows Server, Azure Stack HCI nebo centrum Azure Stack lépe vyhovující jejich potřebám. Toto téma vám pomůže určit, který z nich je pro vaši organizaci nejvhodnější. 

## <a name="compare-azure-stack-hci-to-windows-server"></a>Porovnání Azure Stack HCl k Windows serveru

Systémy Windows Server a Azure Stack HCI poskytují stejné vysoce kvalitní uživatelské prostředí s velkou mapou nových verzí.

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Nejlepší Host a tradiční Server | Nejlepší Hostitel virtualizace pro datové centrum definované softwarem, včetně Prostory úložiště s přímým přístupem |
| Spuštění kdekoli a pomocí tradičního modelu licencování softwaru | Spouští se na hardwaru od preferovaného dodavatele, ale dodává se jako služba Azure a účtuje se na účet Azure. |
| Dvě možnosti instalace: Server s desktopovým prostředím nebo jádro serveru | Založený na lehce přizpůsobeném jádru serveru |

### <a name="when-to-use-windows-server"></a>Kdy použít Windows Server

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server je vysoce univerzálním a víceúčelovým operačním systémem s spoustou rolí a stovek funkcí, včetně oprávnění hostů. | Azure Stack HCI nezahrnuje práva hostů a je určený k použití pro moderní, prokonvergované architektury. |
| Použijte Windows Server ke spouštění virtuálních počítačů nebo k instalaci holého systému zahrnující všechny tradiční role serveru, včetně služeb Active Directory, souborové služby, DNS, DHCP, Internetová informační služba (IIS), hosta kontejneru/hosta, SQL Server, Exchange serveru, služby strážce hostitele (HGS) a spousty dalších. | Azure Stack HCL jako hostitel virtualizace technologie Hyper-V, mají licenci jenom na spouštění malého počtu rolí serveru. všechny ostatní role musí běžet v rámci virtuálních počítačů. |

### <a name="when-to-use-azure-stack-hci"></a>Kdy použít Azure Stack HCL

| Windows Server | Azure Stack HCI |
| --------------- | --------------- |
| Windows Server může běžet místně nebo v cloudu, ale nenabídne nejnovější funkce, které se v ní přesouvají.| Azure Stack HCL je špičkovou infrastrukturou platformy Microsoftu pro spouštění virtuálních počítačů v místním prostředí, případně roztažená na dvě lokality a s připojeními k Azure Hybrid Services. Je to snadný způsob, jak modernizovat a zabezpečit vaše datová centra a firemní pobočky, dosáhnout nejlepšího výkonu pro SQL Serveré databáze a místní spuštění virtuálních počítačů nebo virtuálních ploch pro účely nízké latence a suverenity dat.|
| Windows Server je skvělým "švýcarským" všestranným nůžm pro všechny role Windows serveru, které jsou virtualizované nebo ne. | Použijte Azure Stack HCI k virtualizaci klasických podnikových aplikací, jako je Exchange, SharePoint a SQL Server, a k virtualizaci rolí Windows serveru, jako jsou souborové servery, DNS, DHCP, IIS a AD. Zahrnuje neomezený přístup ke všem funkcím Hyper-V, jako jsou stíněné virtuální počítače.|
| Mnoho nasazení Windows serveru běží na základě stárnutí hardwaru. | Použijte Azure Stack HCI k použití softwarově definované infrastruktury místo polí úložišť nebo síťových zařízení, bez hlavní opětovné architektury. Integrované technologie Hyper-V, Prostory úložiště s přímým přístupem a Software-Defined Networking (SDN) jsou přímo přístupné a spravovatelné. Spouštějte aplikace v rámci virtuálních počítačů se systémem Windows nebo Linux.|

## <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Porovnat Azure Stack HCI s Azure Stack hub

V případě, že vaše organizace bude digitálně transformovat, můžete se s využitím veřejných cloudových služeb rychleji pohybovat a obnovovat starší verze aplikací. Z důvodů, které zahrnují technologické a regulativní překážky, ale mnoho úloh musí zůstat v místním prostředí. Tato tabulka vám pomůže určit, kterou strategii hybridního cloudu od Microsoftu nabízí, co potřebujete, a doručovat cloudové inovace na úlohy bez ohledu na to, kde jsou.

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Nové dovednosti, inovativní procesy | Stejné dovednosti, známé procesy |
| Služby Azure ve vašem datovém centru | Připojení datacentra ke službám Azure |

### <a name="when-to-use-azure-stack-hub"></a>Kdy použít centrum Azure Stack

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Služba Azure Stack hub slouží k samoobslužné infrastruktuře jako služby (IaaS) se silným izolací a přesným sledováním využití a vrácení peněz pro více společně umístěných klientů. Ideální pro poskytovatele služeb a privátní cloudy v podniku. Šablony z Azure Marketplace. | Azure Stack HCI není nativně vynutila nebo neposkytuje pro víceklientské architektury. |
| Pomocí centra Azure Stack můžete vyvíjet a spouštět aplikace, které spoléhají na služby typu platforma jako služba (PaaS), jako jsou Web Apps, funkce nebo Event Hubs v místním prostředí. Tyto služby běží na Azure Stackovém rozbočovači přesně stejně jako v Azure a poskytují konzistentní prostředí pro hybridní vývoj a běhové prostředí. | Azure Stack HCI neběží v místním prostředí PaaS Services. |
| Pomocí centra Azure Stack můžete modernizovat nasazení a provoz aplikací s postupy DevOps, jako je infrastruktura jako kód, průběžná integrace a průběžné nasazování (CI/CD) a pohodlné funkce, jako jsou třeba rozšíření virtuálních počítačů konzistentní s Azure. Ideální pro vývojové a DevOps týmy. | Azure Stack HCI nezahrnuje nativně žádné nástroje DevOps. |

### <a name="when-to-use-azure-stack-hci"></a>Kdy použít Azure Stack HCL

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack hub vyžaduje minimálně 4 uzly a vlastní síťové přepínače. | Pro minimální nároky na vzdálené pobočky a větve použijte Azure Stack HCL. Začněte s využitím pouhých 2 uzlů serveru a bez přepínat zpět na zpětnou síť pro zjednodušení a dostupnost. V nabídce hardware se spouští 4 jednotky, 64 GB paměti, a to i v poli $10 000/uzel. |
| Rozbočovač Azure Stack omezuje možnost využití technologie Hyper-V a sadu funkcí pro zajištění konzistence s Azure. | K virtualizaci Frills technologie Hyper-V pro klasické podnikové aplikace, jako je Exchange, SharePoint a SQL Server a Virtualizujte role Windows serveru jako souborové servery, DNS, DHCP, IIS a AD, použijte Azure Stack HCI. Neomezený přístup ke všem funkcím technologie Hyper-V, jako jsou stíněné virtuální počítače.|
| Azure Stack hub nezveřejňuje tyto technologie infrastruktury. | Použijte Azure Stack HCI k použití softwarově definované infrastruktury místo polí úložišť nebo síťových zařízení, bez hlavní opětovné architektury. Integrované technologie Hyper-V, Prostory úložiště s přímým přístupem a Software-Defined Networking (SDN) jsou přímo přístupné a spravovatelné. |

## <a name="next-steps"></a>Další kroky

- [Co je Azure Stack HCI?](../overview.md)
