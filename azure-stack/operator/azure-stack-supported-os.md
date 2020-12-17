---
title: Podporované hostované operační systémy ve službě Azure Stack Hub
titleSuffix: Azure Stack
description: Seznamte se s hostovanými operačními systémy, které se dají použít v Azure Stack hub.
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: 3d535893b75cdaf49b4003fce2382ab092b328fe
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620581"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Podporované hostované operační systémy ve službě Azure Stack Hub

## <a name="windows"></a>Windows

Centrum Azure Stack podporuje hostované operační systémy Windows, které jsou uvedené v následující tabulce:

| Operační systém | Popis | K dispozici v tržišti Azure Stack hub |
| --- | --- | --- |
| Windows Server verze 1709 | 64bitová | Jádro s kontejnery |
| Windows Server 2019 | 64bitová |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2016 | 64bitová |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2012 R2 | 64bitová |  Datové centrum |
| Windows Server 2012 | 64bitová |  Datové centrum |
| Windows Server 2008 R2 SP1 | 64bitová |  Datové centrum |
| Windows Server 2008 SP2 | 64bitová |  Přineste si vlastní image |
| Windows 10 *(viz poznámka 1)* | 64-bit, pro a Enterprise | Přineste si vlastní image |

> [!NOTE]
> Pokud chcete nasadit klientské operační systémy Windows 10 v Azure Stackovém centru, musíte mít [licenci pro Windows pro jednotlivé uživatele](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) nebo si koupit prostřednictvím [kvalifikovaného hostitele s více klienty (QMTH)](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

K dispozici jsou image Marketplace pro licencování s průběžnými platbami nebo BYOL (EA/SPLA). Použití obou v rámci jedné instance centra Azure Stack se nepodporuje. Při nasazení Azure Stack centrum vloží do image vhodnou verzi agenta hosta.

Edice Datacenter jsou k dispozici na webu centra Azure Stack pro stahování. zákazníci můžou přenášet vlastní serverové image, včetně jiných edicí. Image klientů Windows nejsou k dispozici v tržišti Azure Stack hub.

## <a name="linux"></a>Linux

Distribuce systému Linux uvedené jako dostupné na webu centra Azure Stack zahrnují potřebného agenta Windows Azure Linux (WALA). Pokud přenesete vlastní image Azure Stack, postupujte podle pokynů v části [Přidání imagí pro Linux do Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Vlastní image by se měly sestavovat pomocí nejnovější veřejné verze WALA (na buildu centra 1903 Azure Stack a vyšší nebo s opravou hotfix 1901/1902) nebo s verzí 2.2.20. Verze před 2.2.20 a mezi 2.2.21 a 2.2.34 (včetně) nemusí v Azure Stackovém centru správně fungovat. V centru Azure Stack 1910 a novějších pracují všechny verze agenta Azure WALA s centrem Azure Stack.
>
> [Cloud-init](https://cloud-init.io/) se podporuje v Azure Stack hub 1910 a vyšší.

| Distribuce | Popis | Publisher | Tržiště centra Azure Stack |
| --- | --- | --- | --- |
| CentOS-based 6,9 | 64bitová | Neautorizovaný Wave | Ano |
| CentOS-based 7,5 | 64bitová | Neautorizovaný Wave | Ano |
| Distribuce založené na CentOS 7.3 | 64bitová | Neautorizovaný Wave | Ano |
| ClearLinux | 64bitová | ClearLinux.org | Ano |
| CoreOS Linux (stabilní) |  64bitová | CoreOS | Ano |
| Debian 8 "Jessie" | 64bitová | credativ |  Ano |
| Debian 9 "Stretch" | 64bitová | credativ | Ano |
| Oracle Linux | 64bitová | Oracle | Ano |
| Red Hat Enterprise Linux 7,1 (a novější) | 64bitová | Red Hat | Přineste si vlastní image |
| SLES 11SP4 | 64bitová | SUSE | Ano |
| SLES 12SP3 | 64bitová | SUSE | Ano |
| Ubuntu 14,04 – LTS | 64bitová | Canonical | Ano |
| Ubuntu 16,04 – LTS | 64bitová | Canonical | Ano |
| Ubuntu 18,04 – LTS | 64bitová | Canonical | Ano |

Informace o podpoře Red Hat Enterprise Linux najdete v tématu [Red Hat a Azure Stack hub: nejčastější dotazy](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Stack hub Marketplace najdete v následujících článcích:

- [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
