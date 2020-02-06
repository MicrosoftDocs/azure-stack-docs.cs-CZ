---
title: Hostované operační systémy podporované v centru Azure Stack
titleSuffix: Azure Stack
description: Seznamte se s hostovanými operačními systémy, které se dají použít v Azure Stack hub.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: c590aed504df1549e3c4a43ae282a304e579195a
ms.sourcegitcommit: 74ce7c12a93d47315d70427b02bcacbd3b44f854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2020
ms.locfileid: "77037195"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Hostované operační systémy podporované v centru Azure Stack

## <a name="windows"></a>Windows

Centrum Azure Stack podporuje hostované operační systémy Windows, které jsou uvedené v následující tabulce:

| Operační systém | Popis | K dispozici v tržišti Azure Stack hub |
| --- | --- | --- |
| Windows Server verze 1709 | 64bitový | Jádro s kontejnery |
| Windows Server 2019 | 64bitový |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2016 | 64bitový |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2012 R2 | 64bitový |  Datové centrum |
| Windows Server 2012 | 64bitový |  Datové centrum |
| Windows Server 2008 R2 SP1 | 64bitový |  Datové centrum |
| Windows Server 2008 SP2 | 64bitový |  Přineste si vlastní image |
| Windows 10 *(viz poznámka 1)* | 64-bit, pro a Enterprise | Přineste si vlastní image |

> [!NOTE]
> Pokud chcete nasadit klientské operační systémy Windows 10 v Azure Stackovém centru, musíte mít [licenci pro Windows pro jednotlivé uživatele](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) nebo si koupit prostřednictvím kvalifikovaného hostitele s více klienty ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

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
| CentOS-based 6,9 | 64bitový | Neautorizovaný Wave | Ano |
| CentOS-based 7,5 | 64bitový | Neautorizovaný Wave | Ano |
| Distribuce založené na CentOS 7.3 | 64bitový | Neautorizovaný Wave | Ano |
| ClearLinux | 64bitový | ClearLinux.org | Ano |
| CoreOS Linux (stabilní) |  64bitový | CoreOS | Ano |
| Debian 8 "Jessie" | 64bitový | credativ |  Ano |
| Debian 9 "Stretch" | 64bitový | credativ | Ano |
| Oracle Linux | 64bitový | Oracle | Ano |
| Red Hat Enterprise Linux 7,1 (a novější) | 64bitový | Red Hat | Přineste si vlastní image |
| SLES 11SP4 | 64bitový | SUSE | Ano |
| SLES 12SP3 | 64bitový | SUSE | Ano |
| Ubuntu 14,04 – LTS | 64bitový | Canonical | Ano |
| Ubuntu 16,04 – LTS | 64bitový | Canonical | Ano |
| Ubuntu 18,04 – LTS | 64bitový | Canonical | Ano |

Informace o podpoře Red Hat Enterprise Linux najdete v tématu [Red Hat a Azure Stack hub: nejčastější dotazy](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Stack hub Marketplace najdete v následujících článcích:

- [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
