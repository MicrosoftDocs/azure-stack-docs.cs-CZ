---
title: Podporované hostované operační systémy pro Azure Stack | Dokumentace Microsoftu
description: Tyto hostované operační systémy je možné ve službě Azure Stack.
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
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2018
ms.openlocfilehash: 53acfa3a2d85e73b5d8136c7a1f42ee00d8ed1be
ms.sourcegitcommit: 07c51a03f07a6a3ee2721aa942d31a7a4c6a339b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/12/2019
ms.locfileid: "67028260"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Hostované operační systémy podporované ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

## <a name="windows"></a>Windows

Azure Stack podporuje Windows hostovaných operačních systémů uvedených v následující tabulce:

| Operační systém | Popis | K dispozici na webu Marketplace |
| --- | --- | --- |
| Windows Server verze 1709 | 64 bitů | Jádra s kontejnery |
| Windows Server 2019 | 64 bitů |  Datové centrum, core Datacenter, Datacenter s kontejnery |
| Windows Server 2016 | 64 bitů |  Datové centrum, core Datacenter, Datacenter s kontejnery |
| Windows Server 2012 R2 | 64 bitů |  Datové centrum |
| Windows Server 2012 | 64 bitů |  Datové centrum |
| Windows Server 2008 R2 SP1 | 64 bitů |  Datové centrum |
| Windows Server 2008 SP2 | 64 bitů |  Používání vlastní image |
| Windows 10 *(viz poznámka 1)* | 64-bit, Pro a Enterprise | Používání vlastní image |

> [!NOTE]
> Chcete-li nasazovat operační systémy klienta Windows 10 ve službě Azure Stack, musíte mít [Windows licence vázané na uživatele](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx) nebo zakoupit prostřednictvím kvalifikovaný hostitel Multitenant ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)).

Image z Marketplace jsou k dispozici pro placené jako využití nebo licencování BYOL (EA/SPLA). Použití na jednu instanci služby Azure Stack není podporováno. Během nasazování služby Azure Stack vkládá vhodnou verzi agenta hosta do bitové kopie.

Edice Datacenter jsou dostupné na webu Marketplace pro stahování. Zákazníci můžou začít přenášet své vlastní Image serveru, včetně jiných edicích. Imagí klienta Windows nejsou k dispozici na webu Marketplace.

## <a name="linux"></a>Linux

Linuxové distribuce, které jsou uvedené jako dostupné na webu Marketplace zahrnují nezbytné Windows Azure Linux Agent (WALA). Používání vlastní image do služby Azure Stack, postupujte podle pokynů v [přidat Linuxové Image do služby Azure Stack](azure-stack-linux.md).

> [!NOTE]
> Vlastní Image by měly být sestaveny pomocí nejnovější veřejné WALA verzi (při sestavování 1903 Azure Stack a nad nebo s 1901/1902 opravu hotfix) nebo s verzí 2.2.20. Verze před 2.2.20 a mezi 2.2.20 a 2.2.35 (výhradní) nebude fungovat správně v Azure stacku.
>
> [cloud-init](https://cloud-init.io/) není v tuto chvíli nepodporuje v Azure stacku.

| Distribuce | Popis | Vydavatel | Marketplace |
| --- | --- | --- | --- |
| Založené na centOS 6.9 | 64 bitů | Neautorizovaný Wave | Ano |
| Založené na centOS 7.5 | 64 bitů | Neautorizovaný Wave | Ano |
| Distribuce založené na CentOS 7.3 | 64 bitů | Neautorizovaný Wave | Ano |
| ClearLinux | 64 bitů | ClearLinux.org | Ano |
| Kontejner Linuxu |  64 bitů | CoreOS | Stable |
| Debian 8 "Jessie" | 64 bitů | credativ |  Ano |
| Debian 9 "Stretch" | 64 bitů | credativ | Ano |
| Red Hat Enterprise Linux 7.1 (a novější) | 64 bitů | Red Hat | Používání vlastní image |
| SLES 11SP4 | 64 bitů | SUSE | Ano |
| SLES 12SP3 | 64 bitů | SUSE | Ano |
| Ubuntu 14.04-LTS | 64 bitů | Canonical | Ano |
| Ubuntu 16.04-LTS | 64 bitů | Canonical | Ano |
| Ubuntu 18.04-LTS | 64 bitů | Canonical | Ano |

Informace o podpoře Red Hat Enterprise Linux, najdete v části [Red Hat a Azure Stack: Nejčastější dotazy](https://access.redhat.com/articles/3413531).

## <a name="next-steps"></a>Další postup

Další informace o marketplace služby Azure Stack najdete v následujících článcích:

- [Stažení položek z Marketplace](azure-stack-download-azure-marketplace-item.md)  
- [Vytvoření a publikování položky marketplace](azure-stack-create-and-publish-marketplace-item.md)
