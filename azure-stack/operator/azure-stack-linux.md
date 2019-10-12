---
title: Přidání imagí pro Linux do webu Azure Stack Marketplace | Microsoft Docs
description: Přečtěte si, jak přidat image pro Linux do webu Azure Stack Marketplace.
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
ms.openlocfilehash: d7723dcdd755a926990ee52e96c3b75694651520
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277208"
---
# <a name="add-linux-images-to-azure-stack-marketplace"></a>Přidání imagí Linux do webu Azure Stack Marketplace

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Virtuální počítače se systémem Linux můžete nasadit na Azure Stack přidáním image založené na systému Linux do webu Azure Stack Marketplace. Nejjednodušší způsob, jak přidat image pro Linux do Azure Stack, je prostřednictvím správy Marketplace. Tyto Image byly připraveny a testovány na kompatibilitu s Azure Stack.

## <a name="marketplace-management"></a>Správa Marketplace

Pokud chcete stáhnout image ze systému Linux z Azure Marketplace, přečtěte si téma [stažení položek Marketplace z Azure do Azure Stack](azure-stack-download-azure-marketplace-item.md). Vyberte image pro Linux, které chcete uživatelům nabídnout na svém Azure Stack.

Tyto image se často aktualizují, takže je Správa Marketplace často pořád aktuální.

## <a name="prepare-your-own-image"></a>Příprava vlastní image

Pokud je to možné, Stáhněte si image dostupné prostřednictvím správy Marketplace. Tyto Image byly připraveny a testovány pro Azure Stack.

### <a name="azure-linux-agent"></a>Agent Azure Linux

Je vyžadován agent Azure Linux (obvykle označovaný jako **WALinuxAgent** nebo **WALinuxAgent**) a ne všechny verze agenta fungují na Azure Stack. Verze mezi 2.2.20 a 2.2.35 nejsou v Azure Stack podporovány. Chcete-li použít nejnovější verze agenta výše než 2.2.35, použijte opravu hotfix 1901 hotfix/1902 nebo aktualizujte Azure Stack na verzi 1903 (nebo vyšší). Všimněte si, že v tuto chvíli není v Azure Stack podporována [Cloud-init](https://cloud-init.io/) .

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

* [Distribuce na základě CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES & openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Server Ubuntu](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-marketplace"></a>Přidání image do Marketplace

Postupujte podle pokynů [Přidat obrázek na Marketplace](azure-stack-add-vm-image.md). Zajistěte, aby byl parametr `OSType` nastaven na hodnotu `Linux`.

Po přidání image na Marketplace se vytvoří položka Marketplace a uživatelé můžou nasadit virtuální počítač se systémem Linux.

## <a name="next-steps"></a>Další kroky

* [Stažení položek z webu Marketplace z Azure do Azure Stack](azure-stack-download-azure-marketplace-item.md)
* [Přehled Azure Stack Marketplace](azure-stack-marketplace.md)
