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
ms.openlocfilehash: 98baf8217bc245f7aa6ac7bc0e2b8f8a5284c6d6
ms.sourcegitcommit: 86e2b776383a9f761072199ec6b6b8a7d2e6b798
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69021832"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Hostované operační systémy podporované v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

## <a name="windows"></a>Windows

Azure Stack podporuje hostované operační systémy Windows, které jsou uvedené v následující tabulce:

| Operační systém | Popis | K dispozici na webu Marketplace |
| --- | --- | --- |
| Windows Server verze 1709 | 64 bitů | Jádro s kontejnery |
| Windows Server. 2019 | 64 bitů |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2016 | 64 bitů |  Datacenter, Datacenter Core, Datacenter s kontejnery |
| Windows Server 2012 R2 | 64 bitů |  Datové centrum |
| Windows Server 2012 | 64 bitů |  Datové centrum |
| Windows Server 2008 R2 SP1 | 64 bitů |  Datové centrum |
| Windows Server 2008 SP2 | 64 bitů |  Přineste si vlastní image |
| Windows 10 *(viz poznámka 1)* | 64-bit, pro a Enterprise | Přineste si vlastní image |

> [!NOTE]
> Chcete-li nasadit klientské operační systémy Windows 10 na Azure Stack, musíte mít [licenci na uživatele systému Windows](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) nebo zakoupit prostřednictvím kvalifikovaného hostitele s více klienty ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

K dispozici jsou image Marketplace pro licencování s průběžnými platbami nebo BYOL (EA/SPLA). Použití obou pro jednu instanci Azure Stack se nepodporuje. Během nasazení Azure Stack vloží do image vhodnou verzi agenta hosta.

Edice Datacenter jsou k dispozici na webu Marketplace ke stažení. zákazníci můžou přenášet vlastní serverové image, včetně jiných edicí. Image klientů Windows nejsou na webu Marketplace k dispozici.

## <a name="linux"></a>Linux

Distribuce systému Linux uvedená jako dostupná na webu Marketplace zahrnují potřebného agenta Windows Azure Linux (WALA). Pokud přenesete vlastní image Azure Stack, postupujte podle pokynů v části [Přidání imagí pro Linux do Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Vlastní image by se měly sestavit pomocí nejnovější veřejné verze WALA (na 1903 Azure Stack sestavení a výše nebo pomocí opravy hotfix 1901/1902) nebo verze 2.2.20. Verze před 2.2.20 a mezi 2.2.20 a 2.2.35 (exkluzivní) nemusí správně fungovat na Azure Stack.
>
> v tuto chvíli se Azure Stack nepodporuje [Cloud-init](https://cloud-init.io/) .

| Distribuce | Popis | Vydavatel | Marketplace |
| --- | --- | --- | --- |
| CentOS-based 6,9 | 64 bitů | Neautorizovaný Wave | Ano |
| CentOS-based 7,5 | 64 bitů | Neautorizovaný Wave | Ano |
| Distribuce založené na CentOS 7.3 | 64 bitů | Neautorizovaný Wave | Ano |
| ClearLinux | 64 bitů | ClearLinux.org | Ano |
| Kontejner Linux |  64 bitů | CoreOS | Stable |
| Debian 8 "Jessie" | 64 bitů | credativ |  Ano |
| Debian 9 "Stretch" | 64 bitů | credativ | Ano |
| Oracle Linux | 64 bitů | Oracle | Ano |
| Red Hat Enterprise Linux 7,1 (a novější) | 64 bitů | Red Hat | Přineste si vlastní image |
| SLES 11SP4 | 64 bitů | SUSE | Ano |
| SLES 12SP3 | 64 bitů | SUSE | Ano |
| Ubuntu 14,04 – LTS | 64 bitů | Canonical | Ano |
| Ubuntu 16,04 – LTS | 64 bitů | Canonical | Ano |
| Ubuntu 18,04 – LTS | 64 bitů | Canonical | Ano |

Informace o podpoře Red Hat Enterprise Linux najdete [na Red Hat a Azure Stack: Nejčastější dotazy](https://access.redhat.com/articles/3413531)

## <a name="next-steps"></a>Další postup

Další informace o Azure Stack Marketplace najdete v následujících článcích:

- [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md)
