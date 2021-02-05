---
title: Porovnat Azure Stack HCI s Azure Stack hub
description: Toto téma vám pomůže určit, jestli Azure Stack má pro vaši organizaci právo na rozbočovače HCI nebo Azure Stack.
ms.topic: conceptual
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/04/2021
ms.openlocfilehash: 7c501650d5c9a4d2d6df66269f689d80ffd3f8e0
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570680"
---
# <a name="compare-azure-stack-hci-to-azure-stack-hub"></a>Porovnat Azure Stack HCI s Azure Stack hub

> Platí pro: Azure Stack HCI, verze 20H2; Centrum Azure Stack

V případě, že vaše organizace bude digitálně transformovat, můžete se s využitím veřejných cloudových služeb rychleji pohybovat a obnovovat starší verze aplikací. Z důvodů, které zahrnují technologické a regulativní překážky, ale mnoho úloh musí zůstat v místním prostředí. Tato tabulka vám pomůže určit, kterou strategii hybridního cloudu od Microsoftu nabízí, co potřebujete, a doručovat cloudové inovace na úlohy bez ohledu na to, kde jsou.

| Azure Stack HCI | Azure Stack Hub |
| --------------- | --------------- |
| Stejné dovednosti, známé procesy | Nové dovednosti, inovativní procesy |
| Připojení datacentra ke službám Azure | Služby Azure ve vašem datovém centru |

## <a name="when-to-use-azure-stack-hci"></a>Kdy použít Azure Stack HCL

V následující tabulce jsou porovnávány případy použití, pro které je Azure Stack HCI lepší, než centrum Azure Stack.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                         |
| ------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Pro minimální nároky na vzdálené pobočky a větve použijte Azure Stack HCL. Začněte s pouhými dvěma servery a přepínat zpátky na pozadí, abyste měli nejvyšší jednoduchost a cenovou dostupnost. | Azure Stack hub vyžaduje minimálně čtyři servery a vlastní síťové přepínače. |
| Použijte Azure Stack HCI k virtualizaci klasických podnikových aplikací, jako je Exchange, SharePoint a SQL Server, a k virtualizaci rolí Windows serveru, jako jsou souborové servery, DNS, DHCP, IIS a Active Directory. Poskytuje neomezený přístup k funkcím technologie Hyper-V.| Rozbočovač Azure Stack omezuje možnost technologie Hyper-V a sadu funkcí pro zajištění konzistence s Azure. | 
| Použijte Azure Stack HCI k použití softwarově definované infrastruktury místo polí úložišť nebo síťových zařízení, bez hlavní opětovné architektury. Integrované technologie Hyper-V, Prostory úložiště s přímým přístupem a Software-Defined Networking (SDN) jsou přímo přístupné a spravovatelné. | Azure Stack hub nezveřejňuje tyto technologie infrastruktury. |

## <a name="when-to-use-azure-stack-hub"></a>Kdy použít centrum Azure Stack

V následující tabulce jsou porovnávány případy použití, pro které je Azure Stack centrum lepší, než Azure Stack HCL.

| Azure Stack HCI                                                                 | Azure Stack Hub                                                                          |
| ------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------- |
| Azure Stack HCI není nativně vynutila nebo neposkytuje pro víceklientské architektury. | Služba Azure Stack hub slouží k samoobslužné infrastruktuře jako služby (IaaS) se silným izolací a přesným sledováním využití a vrácení peněz pro více společně umístěných klientů. Ideální pro poskytovatele služeb a privátní cloudy v podniku. Šablony z Azure Marketplace. | 
| Azure Stack HCI není určené k tomu, aby spouštěla služby PaaS (Platform as-a-Service) místně, i když zahrnuje schopnost hostovat [službu Azure Kubernetes](../../aks-hci/overview.md). | Pomocí centra Azure Stack můžete vyvíjet a spouštět aplikace, které spoléhají na služby PaaS, jako jsou Web Apps, funkce nebo Event Hubs místní. Tyto služby běží na Azure Stackovém rozbočovači přesně stejně jako v Azure a poskytují konzistentní prostředí pro hybridní vývoj a běhové prostředí. |
| Azure Stack HCI nezahrnuje nativně žádné nástroje DevOps. | Pomocí centra Azure Stack můžete modernizovat nasazení a provoz aplikací s postupy DevOps, jako je infrastruktura jako kód, průběžná integrace a průběžné nasazování (CI/CD) a pohodlné funkce, jako jsou třeba rozšíření virtuálních počítačů konzistentní s Azure. Ideální pro vývojové a DevOps týmy. |

## <a name="next-steps"></a>Další kroky

- [Porovnat Azure Stack HCI a Windows Server 2019](compare-windows-server.md)
