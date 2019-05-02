---
title: Přidat Linuxové Image do služby Azure Stack
description: Zjistěte, jak přidat Linuxové Image do služby Azure Stack.
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
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 2f7f0c55f02fd99a419619d878be8300d7326303
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64296784"
---
# <a name="add-linux-images-to-azure-stack"></a>Přidat Linuxové Image do služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Virtuální počítače s Linuxem můžete do služby Azure Stack nasadit přidáním image založené na Linuxu na Marketplace služby Azure Stack. Nejjednodušší způsob, jak do služby Azure Stack přidat image Linuxu, představuje Správa Marketplace. Tyto image jsou připravené a otestované z hlediska kompatibility se službou Azure Stack.

## <a name="marketplace-management"></a>Správa webu Marketplace

Stažení imagí Linuxu na Azure Marketplace, pomocí postupů na portále [stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md) článku. Vyberte Image Linuxu, které mají uživatelé ve vaší službě Azure Stack. 

Všimněte si, že jsou časté aktualizace do těchto bitových kopií, tak správu Marketplace často se má aktualizovat.

## <a name="prepare-your-own-image"></a>Příprava svoji vlastní image

Kdykoli je to možné, stáhněte si image dostupné prostřednictvím Správy Marketplace, které jsou připravené a otestované pro Azure Stack.

### <a name="azure-linux-agent"></a>Azure Linux Agent
Azure Linux Agent (obvykle nazývá `WALinuxAgent` nebo `walinuxagent`) je nutné použít, a ne všechny verze agenta bude fungovat ve službě Azure Stack. Verze mezi 2.2.20 a 2.2.35 nejsou podporovány ve službě Azure Stack. Používat nejnovější verze agenta nad 2.2.35, použijte rychlou záplatu opravu hotfix/1902 1901 nebo aktualizace služby Azure Stack na verzi 1903 (nebo vyšší). Všimněte si, že [cloud-init](https://cloud-init.io/) není v tuto chvíli nepodporuje v Azure stacku.

| Azure Stack sestavení | Azure Linux Agent sestavení |
| ------------- | ------------- |
| 1.1901.0.99 nebo dřívější | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 nebo novější |
| 1.1902.2.73  | 2.2.35 nebo novější |
| 1.1903.0.35  | 2.2.35 nebo novější |
| Nepodporuje se | 2.2.21-2.2.34 |

Můžete připravit své vlastní image s Linuxem podle následujících pokynů:

* [Distribuce založené na CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES a openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Přidání bitové kopie na webu Marketplace

Postupujte podle [Přidání bitové kopie na webu Marketplace](azure-stack-add-vm-image.md). Ujistěte se, že `OSType` parametr je nastaven na `Linux`.

Po přidání obrázku na webu Marketplace, je vytvořena položka Marketplace a uživatelé můžou nasazovat virtuální počítače s Linuxem.

## <a name="next-steps"></a>Další postup

Zobrazit další informace v následujících článcích:

- [Stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Přehled služby Azure Stack Marketplace](azure-stack-marketplace.md)
