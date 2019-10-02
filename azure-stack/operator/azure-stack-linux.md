---
title: Přidání imagí Linux do Azure Stack
description: Naučte se přidávat image Linux do Azure Stack.
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
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 309d3d7185bd225f58691d4996ba649e8df7b97a
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714616"
---
# <a name="add-linux-images-to-azure-stack"></a>Přidání imagí Linux do Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Virtuální počítače s Linuxem můžete do služby Azure Stack nasadit přidáním image založené na Linuxu na Marketplace služby Azure Stack. Nejjednodušší způsob, jak do služby Azure Stack přidat image Linuxu, představuje Správa Marketplace. Tyto image jsou připravené a otestované z hlediska kompatibility se službou Azure Stack.

## <a name="marketplace-management"></a>Správa Marketplace

Pokud chcete stáhnout image ze systému Linux z Azure Marketplace, použijte postup v tématu [stažení položek Marketplace z Azure do Azure Stack](azure-stack-download-azure-marketplace-item.md) článku. Vyberte image pro Linux, které chcete uživatelům nabídnout na svém Azure Stack.

Tyto image se často aktualizují, takže je Správa Marketplace často pořád aktuální.

## <a name="prepare-your-own-image"></a>Příprava vlastní image

Kdykoli je to možné, stáhněte si image dostupné prostřednictvím Správy Marketplace. Tyto image jsou připravené a otestované pro službu Azure Stack.

### <a name="azure-linux-agent"></a>Agent Azure Linux

Je vyžadován agent Azure Linux (obvykle označovaný jako **WALinuxAgent** nebo **WALinuxAgent**) a ne všechny verze agenta fungují na Azure Stack. Verze mezi 2.2.20 a 2.2.35 nejsou v Azure Stack podporovány. Chcete-li použít nejnovější verze agenta výše než 2.2.35, použijte opravu hotfix 1901 hotfix/1902 nebo aktualizujte Azure Stack na verzi 1903 (nebo vyšší). Upozorňujeme, že v tuto chvíli není v Azure Stack podporována [Cloud-init](https://cloud-init.io/) .

| Sestavení Azure Stack | Sestavení agenta Azure Linux |
| ------------- | ------------- |
| 1.1901.0.99 nebo starší | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 nebo novější |
| 1.1902.2.73  | 2.2.35 nebo novější |
| 1.1903.0.35  | 2.2.35 nebo novější |
| Build po 1903 | 2.2.35 nebo novější |
| Není podporováno | 2.2.21-2.2.34 |

Vlastní image pro Linux můžete připravit pomocí následujících pokynů:

* [Distribuce založené na CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES a openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Přidání image na Marketplace

Postupujte podle pokynů [Přidat obrázek na Marketplace](azure-stack-add-vm-image.md). Ujistěte se, že `OSType` je parametr nastaven na `Linux`hodnotu.

Po přidání image na Marketplace se vytvoří položka Marketplace a uživatelé budou moct nasadit virtuální počítač se systémem Linux.

## <a name="next-steps"></a>Další kroky

* [Stažení položek z marketplace z Azure do služby Azure Stack](azure-stack-download-azure-marketplace-item.md)
* [Přehled Azure Stack Marketplace](azure-stack-marketplace.md)
