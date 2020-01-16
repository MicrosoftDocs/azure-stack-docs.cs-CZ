---
title: Hostované operační systémy podporované v centru Azure Stack
titleSuffix: Azure Stack
description: Seznamte se s hostovanými operačními systémy, které se dají použít v Azure Stack hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: 8d437425db15fa078c00c973fe50824d0ada58bd
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023312"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Hostované operační systémy podporované v centru Azure Stack

## <a name="windows"></a>Windows

Centrum Azure Stack podporuje hostované operační systémy Windows, které jsou uvedené v následující tabulce:

| Operační systém | Popis | K dispozici v tržišti Azure Stack hub |
| --- | --- | --- |
| Windows Server, verze 1709 | 64 bitů | Jádro s kontejnery |
| Windows Server 2019 | 64 bitů |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2016 | 64 bitů |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2012 R2 | 64 bitů |  Datacentrum |
| Windows Server 2012 | 64 bitů |  Datacentrum |
| Windows Server 2008 R2 SP1 | 64 bitů |  Datacentrum |
| Windows Server 2008 SP2 | 64 bitů |  Přineste si vlastní image |
| Windows 10 *(viz poznámka 1)* | 64-bit, pro a Enterprise | Přineste si vlastní image |

> [!NOTE]
> Pokud chcete nasadit klientské operační systémy Windows 10 v Azure Stackovém centru, musíte mít [licenci pro Windows pro jednotlivé uživatele](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) nebo si koupit prostřednictvím kvalifikovaného hostitele s více klienty ([QMTH](https://www.microsoft.com/CloudandHosting/licensing_sca.aspx)).

K dispozici jsou image Marketplace pro licencování s průběžnými platbami nebo BYOL (EA/SPLA). Použití obou v rámci jedné instance centra Azure Stack se nepodporuje. Při nasazení Azure Stack centrum vloží do image vhodnou verzi agenta hosta.

Edice Datacenter jsou k dispozici na webu centra Azure Stack pro stahování. zákazníci můžou přenášet vlastní serverové image, včetně jiných edicí. Image klientů Windows nejsou k dispozici v tržišti Azure Stack hub.

## <a name="linux"></a>Linux

Distribuce systému Linux uvedené jako dostupné na webu centra Azure Stack zahrnují potřebného agenta Windows Azure Linux (WALA). Pokud přenesete vlastní image Azure Stack, postupujte podle pokynů v části [Přidání imagí pro Linux do Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Vlastní image by se měly sestavovat pomocí nejnovější veřejné verze WALA (na buildu centra 1903 Azure Stack a vyšší nebo s opravou hotfix 1901/1902) nebo s verzí 2.2.20. Verze před 2.2.20 a mezi 2.2.21 a 2.2.34 (včetně) nemusí v Azure Stackovém centru správně fungovat. V centru Azure Stack 1910 a novějších pracují všechny verze agenta Azure WALA s centrem Azure Stack.
>
> [Cloud-init](https://cloud-init.io/) se podporuje v Azure Stack hub 1910 a vyšší.

| Distribuce | Popis | Vydavatel | Tržiště centra Azure Stack |
| --- | --- | --- | --- |
| CentOS-based 6,9 | 64 bitů | Neautorizovaný Wave | Ano |
| CentOS-based 7,5 | 64 bitů | Neautorizovaný Wave | Ano |
| Distribuce založené na CentOS 7.3 | 64 bitů | Neautorizovaný Wave | Ano |
| ClearLinux | 64 bitů | ClearLinux.org | Ano |
| CoreOS Linux (stabilní) |  64 bitů | CoreOS | Ano |
| Debian 8 "Jessie" | 64 bitů | credativ |  Ano |
| Debian 9 "Stretch" | 64 bitů | credativ | Ano |
| Oracle Linux | 64 bitů | Oracle | Ano |
| Red Hat Enterprise Linux 7,1 (a novější) | 64 bitů | Red Hat | Přineste si vlastní image |
| SLES 11SP4 | 64 bitů | SUSE | Ano |
| SLES 12SP3 | 64 bitů | SUSE | Ano |
| Ubuntu 14,04 – LTS | 64 bitů | Canonical | Ano |
| Ubuntu 16,04 – LTS | 64 bitů | Canonical | Ano |
| Ubuntu 18,04 – LTS | 64 bitů | Canonical | Ano |

Informace o podpoře Red Hat Enterprise Linux najdete v tématu [Red Hat a Azure Stack hub: nejčastější dotazy](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Stack hub Marketplace najdete v následujících článcích:

- [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
