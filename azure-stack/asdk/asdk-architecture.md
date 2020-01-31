---
title: Architektura ASDK
description: Přečtěte si o architektuře Azure Stack Development Kit (ASDK).
author: justinha
ms.topic: article
ms.date: 06/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 842dab690d8239ca0c4db7622fe7a0bb18347a0c
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874140"
---
# <a name="asdk-architecture"></a>Architektura ASDK
Azure Stack Development Kit (ASDK) je nasazení Azure Stack v jednom uzlu, které běží na jednom hostitelském počítači. Komponenty směrování Edge jsou nainstalované na hostitelském počítači pro poskytování funkcí NAT a sítě VPN pro Azure Stack. Role Azure Stack infrastruktury se spouštějí ve vrstvě Hyper-V fyzického hostitelského počítače.


## <a name="virtual-machine-roles"></a>Role virtuálních počítačů
ASDK nabízí služby, které používají následující virtuální počítače hostované v hostitelském počítači vývojové sady:

| Name (Název) | Popis |
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
