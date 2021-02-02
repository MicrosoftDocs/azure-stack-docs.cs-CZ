---
title: Přesunutí specializovaného virtuálního počítače z místního počítače do centra Azure Stack
description: Naučte se, jak přesunout specializovaný virtuální počítač z místního počítače do centra Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: b5d46af0ebb5a1ae059ff7f778c8177e0ea4a04d
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247842"
---
# <a name="move-a-specialized-vm-from-on-premises-to-azure-stack-hub"></a>Přesunutí specializovaného virtuálního počítače z místního počítače do centra Azure Stack

Můžete přidat image virtuálního počítače z místního prostředí. Image můžete vytvořit jako virtuální pevný disk (VHD) a nahrát image do účtu úložiště v instanci centra Azure Stack. Pak můžete vytvořit virtuální počítač z virtuálního pevného disku.

Specializovaná image disku je kopie virtuálního pevného disku (VHD) ze stávajícího virtuálního počítače, který obsahuje uživatelské účty, aplikace a další údaje o stavu z původního virtuálního počítače. Obvykle se jedná o formát, ve kterém jsou virtuální počítače migrovány do centra Azure Stack. Specializované virtuální pevné disky jsou vhodné pro potřebu migrace virtuálních počítačů z místního prostředí do centra Azure Stack.

## <a name="how-to-move-an-image"></a>Postup přesunutí obrázku

Najděte si část, která je specifická pro vaše potřeby při přípravě virtuálního pevného disku.

#### <a name="windows-vm"></a>[Virtuální počítač s Windows](#tab/port-win)

- Postupujte podle kroků v části [Příprava virtuálního pevného disku (VHD) Windows a nahrání do Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) , aby se virtuální pevný disk správně připravil. Pro Azure Stack hub je nutné použít VHD.
   > [!NOTE]  
   > **Neprovádějte generalizaci** virtuálního počítače pomocí nástroje Sysprep.
- Odeberte všechny virtualizační nástroje a agenty hosta, které jsou nainstalované na virtuálním počítači (například nástroje VMware).
- Ujistěte se, že je virtuální počítač nakonfigurovaný tak, aby získal IP adresu a nastavení DNS z protokolu DHCP. Tím se zajistí, že při spuštění serveru získá IP adresu v rámci virtuální sítě.
- Ujistěte se, že je povolený protokol RDP/SSH a že brána firewall umožňuje komunikaci.
- Pokud chcete nasadit rozšíření virtuálních počítačů, ujistěte se, že je `.msi` k dispozici agent virtuálního počítače. Pokyny najdete v tématu [Přehled agenta virtuálních počítačů Azure](/azure/virtual-machines/extensions/agent-windows). Pokud se agent virtuálního počítače nenachází v VHD, nasazení rozšíření se nezdaří. Při zřizování není nutné nastavovat profil operačního systému, nebo ho nastavit `$vm.OSProfile.AllowExtensionOperations = $true` .

#### <a name="linux-vm"></a>[Virtuální počítači s Linuxem](#tab/port-linux)

#### <a name="generalize-the-vhd"></a>Generalizace virtuálního pevného disku

Použijte příslušné pokyny k přípravě virtuálního pevného disku pro operační systém Linux:

- [Distribuce založené na CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES nebo openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> Nespouštějte poslední krok: ( `sudo waagent -force -deprovision` ), protože se tím provede generalizace VHD.

#### <a name="identify-the-version-of-the-linux-agent"></a>Identifikace verze agenta pro Linux

Určete, jaká verze agenta pro Linux je nainstalována na zdrojové imagi virtuálního počítače, a spusťte následující příkazy. Číslo verze, které popisuje zřizovací kód `WALinuxAgent-` , není `Goal state agent` :

   ```bash  
   waagent -version
   ```
    
   Příklad:
    
   ```bash  
   waagent -version
   WALinuxAgent-2.2.45 running on centos 7.7.1908
   Python: 2.7.5
   Goal state agent: 2.2.46
   ```

#### <a name="linux-agent-224-and-earlier-disable-the-linux-agent-provisioning"></a>Pro Linux agent 2.2.4 a starší zakažte zřizování agenta pro Linux. 

Zakažte zřizování agenta pro Linux s agentem pro Linux nižší než 2.2.4, nastavte v **/etc/waagent.conf** tyto parametry: `Provisioning.Enabled=n, and Provisioning.UseCloudInit=n` .

#### <a name="linux-agent-2245-and-later-disable-the-linux-agent-provisioning"></a>2.2.45 agenta pro Linux a novějším, zakažte zřizování agenta pro Linux.

Pokud chcete zakázat zřizování pomocí agenta pro Linux 2.2.45 a novějších verzí, proveďte následující změny možností konfigurace:

`Provisioning.Enabled` a `Provisioning.UseCloudInit` jsou nyní ignorovány.

V této verzi momentálně není k dispozici žádný `Provisioning.Agent` způsob, jak zcela zakázat zřizování. můžete však přidat soubor značky zřizování a s následujícím nastavením se zřizování ignoruje:

1. V **/etc/waagent.conf** přidejte tuto možnost konfigurace: `Provisioning.Agent=Auto` .
2. Aby se zajistilo, že zřizování walinuxagent je zakázané, spusťte: `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned` .
3. Zakažte instalaci Cloud-init spuštěním následujícího:

   ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Odhlaste se.

#### <a name="run-an-extension"></a>Spustit rozšíření

1. Nastavte v **/etc/waagent.conf** následující parametr:

   - `Provisioning.Enabled=n`
   - `Provisioning.UseCloudInit=n`

2. Aby se zajistilo, že zřizování walinuxagent je zakázané, spusťte: `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`

3. Pokud máte bitovou kopii Cloud-init, zakažte inicializaci cloudu:

    ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Provede odhlášení.

---

## <a name="verify-your-vhd"></a>Ověření virtuálního pevného disku

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Odeslat do účtu úložiště

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-disk-in-azure-stack-hub"></a>Vytvoření disku v centru Azure Stack

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

## <a name="next-steps"></a>Další kroky

[Přesunutí virtuálního počítače do centra Azure Stack – přehled](vm-move-overview.md)
