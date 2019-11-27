---
title: Podporované hostované operační systémy pro Azure Stack | Microsoft Docs
description: Tyto hostované operační systémy je možné používat na Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: fc3eecb8f4e06ce02737e513c48d946f2a667c21
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298772"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Hostované operační systémy podporované v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

## <a name="windows"></a>Windows

Azure Stack podporuje hostované operační systémy Windows, které jsou uvedené v následující tabulce:

| Operační systém | Popis | K dispozici na webu Marketplace |
| --- | --- | --- |
| Windows Server verze 1709 | 64 – bit | Jádro s kontejnery |
| Windows Server. 2019 | 64 – bit |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2016 | 64 – bit |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2012 R2 | 64 – bit |  Datacentrum |
| Windows Server 2012 | 64 – bit |  Datacentrum |
| Windows Server 2008 R2 SP1 | 64 – bit |  Datacentrum |
| Windows Server 2008 SP2 | 64 – bit |  Přineste si vlastní image |
| Windows 10 *(viz poznámka 1)* | 64-bit, pro a Enterprise | Přineste si vlastní image |

> [!NOTE]
> Chcete-li nasadit klientské operační systémy Windows 10 na Azure Stack, musíte mít [licenci na uživatele systému Windows](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) nebo zakoupit prostřednictvím kvalifikovaného hostitele s více klienty ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

K dispozici jsou image Marketplace pro licencování s průběžnými platbami nebo BYOL (EA/SPLA). Použití obou pro jednu instanci Azure Stack se nepodporuje. Během nasazení Azure Stack vloží do image vhodnou verzi agenta hosta.

Edice Datacenter jsou k dispozici na webu Marketplace ke stažení. zákazníci můžou přenášet vlastní serverové image, včetně jiných edicí. Image klientů Windows nejsou na webu Marketplace k dispozici.

## <a name="linux"></a>Linux

Distribuce systému Linux uvedená jako dostupná na webu Marketplace zahrnují potřebného agenta Windows Azure Linux (WALA). Pokud přenesete vlastní image Azure Stack, postupujte podle pokynů v části [Přidání imagí pro Linux do Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Vlastní image by se měly sestavit pomocí nejnovější veřejné verze WALA (na 1903 Azure Stack sestavení a výše nebo pomocí opravy hotfix 1901/1902) nebo verze 2.2.20. Verze před 2.2.20 a mezi 2.2.21 a 2.2.34 (včetně) nemusí správně fungovat na Azure Stack. Ve verzi Azure Stack 1910 a novějších budou všechny verze agenta Azure WALA pracovat s Azure Stack.
>
> v tuto chvíli se podporuje [Cloud-init](https://cloud-init.io/) v Azure Stack 1910 a novějších verzích.

| Distribuce | Popis | Vydavatel | Marketplace |
| --- | --- | --- | --- |
| CentOS-based 6,9 | 64 – bit | Neautorizovaný Wave | Ano |
| CentOS-based 7,5 | 64 – bit | Neautorizovaný Wave | Ano |
| Distribuce založené na CentOS 7.3 | 64 – bit | Neautorizovaný Wave | Ano |
| ClearLinux | 64 – bit | ClearLinux.org | Ano |
| CoreOS Linux (stabilní) |  64 – bit | CoreOS | Ano |
| Debian 8 "Jessie" | 64 – bit | credativ |  Ano |
| Debian 9 "Stretch" | 64 – bit | credativ | Ano |
| Oracle Linux | 64 – bit | Oracle | Ano |
| Red Hat Enterprise Linux 7,1 (a novější) | 64 – bit | Red Hat | Přineste si vlastní image |
| SLES 11SP4 | 64 – bit | SUSE | Ano |
| SLES 12SP3 | 64 – bit | SUSE | Ano |
| Ubuntu 14,04 – LTS | 64 – bit | Canonical | Ano |
| Ubuntu 16,04 – LTS | 64 – bit | Canonical | Ano |
| Ubuntu 18,04 – LTS | 64 – bit | Canonical | Ano |

Informace o podpoře Red Hat Enterprise Linux najdete na [Red Hat a Azure Stack: nejčastější dotazy](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Další kroky

Další informace o Azure Stack Marketplace najdete v následujících článcích:

- [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
