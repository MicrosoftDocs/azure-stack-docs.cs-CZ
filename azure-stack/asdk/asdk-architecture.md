---
title: Architektura ASDK | Microsoft Docs
description: Přečtěte si o architektuře Azure Stack Development Kit (ASDK).
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
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 1d740b219b971f85b34ae821d82b97eff7d3d70d
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974590"
---
# <a name="asdk-architecture"></a>Architektura ASDK
Azure Stack Development Kit (ASDK) je nasazení Azure Stack v jednom uzlu, které běží na jednom hostitelském počítači. Komponenty směrování Edge jsou nainstalované na hostitelském počítači pro poskytování funkcí NAT a sítě VPN pro Azure Stack. Role Azure Stack infrastruktury se spouštějí ve vrstvě Hyper-V fyzického hostitelského počítače.


## <a name="virtual-machine-roles"></a>Role virtuálních počítačů
ASDK nabízí služby, které používají následující virtuální počítače hostované v hostitelském počítači vývojové sady:

| Name | Popis |
| ----- | ----- |
| **AzS-ACS01** | Služba Azure Stack Storage.|
| **AzS-ADFS01** | Active Directory Federation Services (AD FS) (ADFS).  |
| **AzS-CA01** | Služby certifikační autority pro Azure Stack služby rolí.|
| **AzS-DC01** | Služby Active Directory, DNS a DHCP pro Microsoft Azure Stack.|
| **AzS-ERCS01** | Virtuální počítač konzoly pro zotavení po nouzovém. |
| **AzS-GWY01** | Hraniční služby brány, jako jsou připojení VPN typu Site-to-site pro sítě klientů.|
| **AzS-NC01** | Síťový adaptér, který spravuje Azure Stack síťové služby.  |
| **AzS-SLB01** | Služby multiplexového vyrovnávání zatížení v Azure Stack pro klienty a služby Azure Stack infrastruktury.  |
| **AzS-SQL01** | Interní úložiště dat pro Azure Stack rolí infrastruktury  |
| **AzS-WAS01** | Azure Stack portálu pro správu a Azure Resource Manager služby.|
| **AzS-WASP01**| Azure Stack portál uživatele (tenant) a služby Azure Resource Manager.|
| **AzS-XRP01** | Řadič pro správu infrastruktury pro Microsoft Azure Stack, včetně zprostředkovatelů prostředků služby COMPUTE, síť a úložiště.|
| **AzS-SRNG01** | Podpora okruhu hosta hostujícího službu sběru protokolů pro Azure Stack. |

## <a name="next-steps"></a>Další kroky
[Přečtěte si o základních úlohách správce ASDK](asdk-admin-basics.md)
