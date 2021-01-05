---
title: Přesunout zobecněný virtuální počítač z místního počítače do centra Azure Stack
description: Naučte se, jak přesunout zobecněný virtuální počítač z místního počítače do centra Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 105beaa0805fe0aea1aacfce8bd22f3bd01714b1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872870"
---
# <a name="move-a-generalized-vm-from-on-premises-to-azure-stack-hub"></a>Přesunout zobecněný virtuální počítač z místního počítače do centra Azure Stack

Můžete přidat image virtuálního počítače z místního prostředí. Image můžete vytvořit jako virtuální pevný disk (VHD) a nahrát image do účtu úložiště v instanci centra Azure Stack. Pak můžete vytvořit virtuální počítač z virtuálního pevného disku.

Zobecněná bitová kopie disku je ta, která byla připravena nástrojem **Sysprep** pro odebrání jedinečných informací (například uživatelských účtů), což umožňuje opakované použití pro vytvoření více virtuálních počítačů. Zobecněné virtuální pevné disky jsou vhodné při vytváření imagí, které používají operátory cloudu Azure Stack hub jako položky Marketplace.

## <a name="how-to-move-an-image"></a>Postup přesunutí obrázku

Najděte si část, která je specifická pro vaše potřeby při přípravě virtuálního pevného disku.

#### <a name="windows-vm"></a>[Virtuální počítač s Windows](#tab/port-win)

Postupujte podle kroků v části [Příprava virtuálního pevného disku (VHDX) systému Windows a nahrání do Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) , abyste mohli správně zobecnit virtuální pevný disk před odesláním. Pro Azure Stack hub je nutné použít VHD.

#### <a name="linux-vm"></a>[Virtuální počítači s Linuxem](#tab/port-linux)

Postupujte podle příslušných pokynů pro generalizaci virtuálního pevného disku pro operační systém Linux:

- [Distribuce založené na CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES nebo openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---

## <a name="verify-your-vhd"></a>Ověření virtuálního pevného disku

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]
## <a name="upload-to-a-storage-account"></a>Odeslat do účtu úložiště

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-image-in-azure-stack-hub"></a>Vytvoření bitové kopie v centru Azure Stack

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]

## <a name="next-steps"></a>Další kroky

[Přesunutí virtuálního počítače do centra Azure Stack – přehled](vm-move-overview.md)
