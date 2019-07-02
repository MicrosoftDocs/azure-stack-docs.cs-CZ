---
title: Azure Stack Development Kit architektura | Dokumentace Microsoftu
description: Popisuje architekturu Azure Stack Development Kit (ASDK).
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
ms.openlocfilehash: 4cd20ed4c76b062ea2fcfdf3d314ef2bcdecaff0
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492352"
---
# <a name="microsoft-azure-stack-development-kit-architecture"></a>Architektura Microsoft Azure Stack Development Kit
Azure Stack Development Kit (ASDK) je jedním uzlem nasazení služby Azure Stack na jednom hostiteli počítači. Součástí směrování Edge jsou nainstalovány na hostitelském počítači a poskytují schopnosti NAT a síť VPN pro službu Azure Stack. Role služby infrastruktury Azure Stack spustit ve vrstvě Hyper-V fyzickém hostitelském počítači.


## <a name="virtual-machine-roles"></a>Role virtuálních počítačů
ASDK nabízí služby pomocí následující virtuální počítače hostované na hostitelském počítači development kit:

| Název | Popis |
| ----- | ----- |
| **AzS-ACS01** | Úložiště služby Azure Stack.|
| **AzS-ADFS01** | Active Directory Federation Services (ADFS).  |
| **AzS-CA01** | Certifikační autority služby role služby Azure Stack.|
| **AzS-DC01** | Active Directory, DNS a DHCP services pro Microsoft Azure Stack.|
| **AzS-ERCS01** | Konzola pro nouzový zotavení virtuálního počítače. |
| **AzS-GWY01** | Hraniční brána službami, jako je připojení site-to-site VPN sítí klientů.|
| **AzS-NC01** | Síťový adaptér, který spravuje síťové služby Azure Stack.  |
| **AzS-SLB01** | Vyrovnávání zatížení multiplexor služby ve službě Azure Stack pro klienty a služby infrastruktury Azure stacku.  |
| **AzS-SQL01** | Vnitřní datové úložiště pro infrastrukturu rolích Azure stacku.  |
| **AzS-WAS01** | Služby Azure Resource Manageru a portálu pro správu Azure Stack.|
| **AzS-WASP01**| Služby Azure Resource Manageru a portálu Azure Stack uživatele (tenant).|
| **AzS-XRP01** | Řadič pro správu infrastruktury pro Microsoft Azure Stack, včetně poskytovatelé prostředků Compute, Network a Storage.|
| **AzS-SRNG01** | Podpora aktualizačního kanálu virtuálního počítače hostující službu shromažďování protokolů pro službu Azure Stack. |

## <a name="next-steps"></a>Další postup
[Další informace o základní úlohy správy ASDK](asdk-admin-basics.md)
