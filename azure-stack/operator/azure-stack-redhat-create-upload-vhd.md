---
title: Příprava virtuálního počítače založeného na Red Hat pro Azure Stack hub
titleSuffix: Azure Stack Hub
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém Red Hat Linux.
author: sethmanheim
ms.topic: article
ms.date: 12/11/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: 8d6a94214d45fcf9d008f93d1ce726d7af536d77
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890234"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Příprava virtuálního počítače založeného na Red Hat pro Azure Stack hub

V tomto článku se dozvíte, jak připravit virtuální počítač s Red Hat Enterprise Linux (RHEL) pro použití v centru Azure Stack. Verze RHEL, které jsou pokryté v tomto článku, jsou 7.1 +. Hypervisory pro přípravu, které jsou pokryté v tomto článku, jsou Hyper-V, virtuální počítač založený na jádrech (KVM) a VMware.

Informace o podpoře Red Hat Enterprise Linux najdete v tématu [Red Hat a Azure Stack hub: nejčastější dotazy](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-vm-from-hyper-v-manager"></a>Příprava virtuálního počítače založeného na Red Hat pomocí Správce technologie Hyper-V

V této části se předpokládá, že už máte soubor ISO z webu Red Hat a máte nainstalovanou image RHEL na virtuální pevný disk (VHD). Další informace o tom, jak pomocí Správce technologie Hyper-V nainstalovat bitovou kopii operačního systému, najdete v tématu [instalace role Hyper-v a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="rhel-installation-notes"></a>Poznámky k instalaci RHEL

* Centrum Azure Stack nepodporuje formát VHDX. Azure podporuje jenom pevný virtuální pevný disk. Správce technologie Hyper-V můžete použít k převedení disku na formát VHD, nebo můžete použít rutinu Convert-VHD. Pokud používáte VirtualBox, při vytváření disku vyberte **pevnou velikost** na rozdíl od výchozí dynamicky přidělené možnosti.
* Centrum Azure Stack podporuje jenom virtuální počítače 1. generace. Virtuální počítač 1. generace můžete převést z VHDX na formát souboru VHD a z dynamického rozšiřování na disk s pevnou velikostí. Nemůžete změnit generaci virtuálního počítače. Další informace najdete v tématu [Vytvoření virtuálního počítače generace 1 nebo 2 v Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Maximální velikost povolená pro virtuální pevný disk je 1 023 GB.
* Při instalaci operačního systému Linux doporučujeme místo Správce logických svazků (LVM) používat standardní oddíly, což je často výchozí nastavení pro mnoho instalací. Tento postup zabrání konfliktu LVM názvů s klonovanými virtuálními počítači, zejména v případě, že někdy budete potřebovat k řešení potíží disk s operačním systémem připojit k jinému stejnému virtuálnímu počítači.
* Podpora jádra pro připojení systémů souborů formátu Universal Disk Format (UDF) je povinná. Při prvním spuštění předává médium ve formátu UDF připojené k hostu konfiguraci zřizování pro virtuální počítač Linux. Agent Azure Linux musí připojit systém souborů UDF a načíst jeho konfiguraci a zřídit virtuální počítač.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agent pro Linux se dá nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor. Další informace o najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na VHD je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další podrobnosti najdete v následujících krocích.
* Centrum Azure Stack podporuje Cloud-init. [Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Protože cloud-init je volána v průběhu procesu prvotního spuštění, nejsou žádné další kroky ani agenty vyžaduje použití vaší konfigurace. Pokyny k přidání Cloud-init do image najdete v tématu [Příprava existující image virtuálního počítače Azure pro Linux pro použití s Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/cloudinit-prepare-custom-image).

### <a name="prepare-an-rhel-7-vm-from-hyper-v-manager"></a>Příprava virtuálního počítače s RHEL 7 pomocí Správce technologie Hyper-V

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

1. Vyberte **připojit** a otevřete tak okno konzoly pro virtuální počítač.

1. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a podle potřeby přidejte následující text:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Spuštěním následujícího příkazu zajistěte spuštění síťové služby v době spuštění:

    ```bash
    sudo systemctl enable network
    ```

1. Zaregistrujte své předplatné Red Hat, abyste mohli povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Chcete-li provést tuto úpravu, otevřete `/etc/default/grub` v textovém editoru a upravte parametr `GRUB_CMDLINE_LINUX`. Příklad:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Tato změna zajistí, že se všechny zprávy konzoly odesílají na první sériový port, což může pomoct podpoře Azure s problémy s laděním. Tato konfigurace také vypne nové zásady vytváření názvů v RHEL 7 pro síťové karty.

   Grafické a tiché spouštění nejsou vhodné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. Možnost `crashkernel` můžete ponechat nakonfigurovanou v případě potřeby. Tento parametr snižuje množství dostupné paměti v virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů. Doporučujeme odebrat následující parametry:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. [Volitelné po 1910 vydání] Zastavení a odinstalace Cloud-Init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění, což je obvykle výchozí. Upravte `/etc/ssh/sshd_config` tak, aby obsahovala následující řádek:

    ```sh
    ClientAliveInterval 180
    ```

1. Při vytváření vlastního virtuálního pevného disku pro Azure Stack hub mějte na paměti, že WALinuxAgent verze mezi 2.2.20 a 2.2.35 (obojí) nefungují v prostředích Azure Stack hub před vydáním verze 1910. K přípravě image můžete použít verze 2.2.20/2.2.35. Pokud chcete pro přípravu vlastní image použít verze vyšší než 2.2.35, aktualizujte Azure Stack centra na verzi 1903 nebo použijte opravu hotfix 1901/1902.
    
    [Před 1910 verzí] Pro stažení kompatibilního WALinuxAgent postupujte podle těchto pokynů:
    
    1. Stáhněte si setuptools.
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
    
    1. Stáhněte si a rozbalte verzi 2.2.20 agenta z našeho GitHubu.

        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```

    1. Nainstalujte setup.py.

        ```bash
        sudo python setup.py install
        ```

    1. Restartujte waagent.
    
        ```bash
        sudo systemctl restart waagent
        ```

    1. Otestujte, jestli verze agenta odpovídá vašemu, který jste stáhli. V tomto příkladu by měl být 2.2.20.

        ```bash
        waagent -version
        ```

    [Po 1910 vydání] Pro stažení kompatibilního WALinuxAgent postupujte podle těchto pokynů:
    
    1. Balíček WALinuxAgent, `WALinuxAgent-<version>`, byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

    1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
    

1. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Disk místního prostředku je dočasný disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux v předchozím kroku upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Pokud chcete zrušit registraci předplatného, spusťte následující příkaz:

    ```bash
    sudo subscription-manager unregister
    ```

1. Pokud používáte systém, který byl nasazen pomocí certifikační autority rozlehlé sítě, virtuální počítač RHEL nebude důvěřovat kořenovému certifikátu centra Azure Stack. Je nutné umístit ho do důvěryhodného kořenového úložiště. Další informace najdete v tématu [Přidání důvěryhodných kořenových certifikátů na server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Vyberte **akci** > **vypnout** ve Správci technologie Hyper-V.

1. Převeďte virtuální pevný disk na virtuální pevný disk s pevnou velikostí pomocí funkce "upravit disk Správce technologie Hyper-V" nebo příkazu Convert-VHD PowerShell. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Příprava virtuálního počítače založeného na Red Hat z KVM

1. Stáhněte si obrázek RHEL 7 na webu Red Hat. Tento postup jako příklad používá RHEL 7.

1. Nastavte kořenové heslo.

    Vygenerujte šifrované heslo a zkopírujte výstup příkazu:

    ```bash
    openssl passwd -1 changeme
    ```

   Nastavte kořenové heslo pomocí guestfish:

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   Změnit druhé pole kořenového uživatele z "!!" do šifrovaného hesla.

1. Z image QCOW2 vytvořte virtuální počítač v KVM. Nastavte typ disku na **QCOW2**a nastavte model zařízení rozhraní virtuální sítě na **virtio**. Pak spusťte virtuální počítač a přihlaste se jako kořenový adresář.

1. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Spuštěním následujícího příkazu zajistěte spuštění síťové služby v době spuštění:

    ```bash
    sudo systemctl enable network
    ```

1. Zaregistrujte své předplatné Red Hat, abyste povolili instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Tuto konfiguraci provedete tak, že v textovém editoru otevřete `/etc/default/grub` a upravíte parametr `GRUB_CMDLINE_LINUX`. Příklad:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Tento příkaz také zajistí, aby byly všechny zprávy konzoly odesílány do prvního sériového portu, což může pomoct podpoře Azure s problémy ladění. Příkaz také vypne nové zásady vytváření názvů pro síťové adaptéry v RHEL 7.

   Grafické a tiché spouštění nejsou vhodné v cloudovém prostředí, kde se všechny protokoly odesílají do sériového portu. Možnost `crashkernel` můžete ponechat nakonfigurovanou v případě potřeby. Tento parametr snižuje množství dostupné paměti v virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů. Doporučujeme odebrat následující parametry:

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grub:

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Přidejte moduly technologie Hyper-V do initramfs.

    Upravit `/etc/dracut.conf` a přidat obsah:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Znovu sestavte initramfs:

    ```bash
    dracut -f -v
    ```

1. [Volitelné po 1910 vydání] Zastavení a odinstalace Cloud-Init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění:

    ```bash
    systemctl enable sshd
    ```

    Upravte/etc/ssh/sshd_config tak, aby obsahovala tyto řádky:

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. Při vytváření vlastního virtuálního pevného disku pro Azure Stack hub mějte na paměti, že WALinuxAgent verze mezi 2.2.20 a 2.2.35 (obojí) nefungují v prostředích Azure Stack hub před vydáním verze 1910. K přípravě image můžete použít verze 2.2.20/2.2.35. Pokud chcete pro přípravu vlastní image použít verze vyšší než 2.2.35, aktualizujte Azure Stack centra na verzi 1903 nebo použijte opravu hotfix 1901/1902.

    [Před 1910 verzí] Pro stažení kompatibilního WALinuxAgent postupujte podle těchto pokynů:

    1. Stáhněte si setuptools.
        
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Stáhněte si a rozbalte verzi 2.2.20 agenta z našeho GitHubu.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Nainstalujte setup.py.
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Restartujte waagent.
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Otestujte, jestli verze agenta odpovídá vašemu, který jste stáhli. V tomto příkladu by měl být 2.2.20.
        
        ```bash
        waagent -version
        ```
        
    [Po 1910 vydání] Pro stažení kompatibilního WALinuxAgent postupujte podle těchto pokynů:
    
    1. Balíček WALinuxAgent, `WALinuxAgent-<version>`, byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

        ```bash
        subscription-manager repos --enable=rhel-7-server-extras-rpms
        ```

        1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

            ```bash
            sudo yum install WALinuxAgent
            sudo systemctl enable waagent.service
            ```

1. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Disk místního prostředku je dočasný disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux v předchozím kroku upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

    ```bash
    subscription-manager unregister
    ```

1. Pokud používáte systém, který byl nasazen pomocí certifikační autority rozlehlé sítě, virtuální počítač RHEL nebude důvěřovat kořenovému certifikátu centra Azure Stack. Je nutné umístit ho do důvěryhodného kořenového úložiště. Další informace najdete v tématu [Přidání důvěryhodných kořenových certifikátů na server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Vypněte virtuální počítač v KVM.

1. Převeďte image QCOW2 na formát VHD.

    > [!NOTE]
    > Verze qemu-img obsahuje známou chybu > = 2.2.1, která má za následek nesprávně naformátovaný virtuální pevný disk. Tento problém byl opravený v QEMU 2,6. Doporučuje se použít buď qemu, img 2.2.0 nebo Lower, nebo aktualizovat na 2,6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.

    Nejprve převeďte obrázek do nezpracovaného formátu:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Ujistěte se, že velikost nezpracovaného obrázku je zarovnána s 1 MB. V opačném případě zarovnejte velikost podle velikosti 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Převeďte nezpracovaný disk na virtuální pevný disk s pevnou velikostí:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Nebo s qemu verze **2.6 +** , zahrňte možnost `force_size`:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-vmware"></a>Příprava virtuálního počítače založeného na Red Hat z VMware

V této části se předpokládá, že jste už nainstalovali virtuální počítač s RHEL ve VMware. Podrobnosti o tom, jak nainstalovat operační systém ve VMware, najdete v tématu [Instalační příručka k hostovanému operačnímu systému VMware](https://aka.ms/aa6z600).

* Při instalaci operačního systému Linux doporučujeme místo LVM použít standardní oddíly, což je často výchozí nastavení pro mnoho instalací. Tato metoda zabrání konfliktu LVM názvů s klonovanými virtuálními počítači, zejména pokud je potřeba disk s operačním systémem připojit k jinému virtuálnímu počítači pro řešení potíží. LVM nebo RAID se dá použít na datových discích, pokud jsou preferované.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agenta pro Linux můžete nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor. Další informace o této konfiguraci najdete v následujících krocích.
* Při vytváření virtuálního pevného disku vyberte možnost **Uložit virtuální disk jako jeden soubor**.

### <a name="prepare-an-rhel-7-vm-from-vmware"></a>Příprava virtuálního počítače s RHEL 7 z VMware

1. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

    ```bash
    sudo chkconfig network on
    ```

1. Zaregistrujte své předplatné Red Hat, abyste mohli povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Chcete-li provést tuto úpravu, otevřete `/etc/default/grub` v textovém editoru a upravte parametr `GRUB_CMDLINE_LINUX`. Příklad:

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    Tato konfigurace taky zajišťuje, že se všechny zprávy konzoly odesílají na první sériový port, což může pomoct podpoře Azure s problémy ladění. Také vypne nové zásady vytváření názvů pro síťové karty v RHEL 7. Doporučujeme odebrat následující parametry:

    ```sh
    rhgb quiet crashkernel=auto
    ```

    Grafické a tiché spouštění nejsou vhodné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. Možnost `crashkernel` můžete ponechat nakonfigurovanou v případě potřeby. Tento parametr snižuje množství dostupné paměti v virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. Přidejte moduly technologie Hyper-V do initramfs.

    Upravit `/etc/dracut.conf`, přidat obsah:

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    Znovu sestavte initramfs:

    ```bash
    dracut -f -v
    ```

1. [Volitelné po 1910 vydání] Zastavení a odinstalace Cloud-Init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění. Toto nastavení je obvykle výchozí. Upravte `/etc/ssh/sshd_config` tak, aby obsahovala následující řádek:

    ```sh
    ClientAliveInterval 180
    ```

1. Při vytváření vlastního virtuálního pevného disku pro Azure Stack hub mějte na paměti, že WALinuxAgent verze mezi 2.2.20 a 2.2.35 (obojí) nefungují v prostředích Azure Stack hub před vydáním verze 1910. K přípravě image můžete použít verze 2.2.20/2.2.35. Pokud chcete pro přípravu vlastní image použít verze vyšší než 2.2.35, aktualizujte Azure Stack centra na verzi 1903 nebo použijte opravu hotfix 1901/1902.

    [Před 1910 verzí] Pro stažení kompatibilního WALinuxAgent postupujte podle těchto pokynů:

    1. Stáhněte si setuptools.
    
        ```bash
        wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
        tar xzf setuptools-7.0.tar.gz
        cd setuptools-7.0
        ```
        
    1. Stáhněte si a rozbalte verzi 2.2.20 agenta z našeho GitHubu.
        
        ```bash
        wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
        unzip v2.2.20.zip
        cd WALinuxAgent-2.2.20
        ```
        
    1. Nainstalujte setup.py.
        
        ```bash
        sudo python setup.py install
        ```
        
    1. Restartujte waagent.
        
        ```bash
        sudo systemctl restart waagent
        ```
        
    1. Otestujte, jestli verze agenta odpovídá vašemu, který jste stáhli. V tomto příkladu by měl být 2.2.20.
        
        ```bash
        waagent -version
        ```
        
    [Po 1910 vydání] Pro stažení kompatibilního WALinuxAgent postupujte podle těchto pokynů:
    
    1. Balíček WALinuxAgent, `WALinuxAgent-<version>`, byl vložen do úložiště Red Hat Extras. Povolte úložiště Extras spuštěním následujícího příkazu:

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

    1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:
        
        ```bash
        sudo yum install WALinuxAgent
        sudo systemctl enable waagent.service
        ```
        
1. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že místní disk prostředků je dočasný disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux v předchozím kroku upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. Pokud chcete zrušit registraci předplatného, spusťte následující příkaz:

    ```bash
    sudo subscription-manager unregister
    ```

1. Pokud používáte systém, který byl nasazen pomocí certifikační autority rozlehlé sítě, virtuální počítač RHEL nebude důvěřovat kořenovému certifikátu centra Azure Stack. Je nutné umístit ho do důvěryhodného kořenového úložiště. Další informace najdete v tématu [Přidání důvěryhodných kořenových certifikátů na server](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html).

1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. Vypněte virtuální počítač a převeďte soubor VMDK na formát VHD.

    > [!NOTE]
    > Verze qemu-img obsahuje známou chybu > = 2.2.1, která má za následek nesprávně naformátovaný virtuální pevný disk. Tento problém byl opravený v QEMU 2,6. Doporučuje se použít buď qemu, img 2.2.0 nebo Lower, nebo aktualizovat na 2,6 nebo vyšší. Odkaz: <https://bugs.launchpad.net/qemu/+bug/1490611>.

    Nejprve převeďte obrázek do nezpracovaného formátu:

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    Ujistěte se, že velikost nezpracovaného obrázku je zarovnána s 1 MB. V opačném případě zarovnejte velikost podle velikosti 1 MB:

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    Převeďte nezpracovaný disk na virtuální pevný disk s pevnou velikostí:

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    Nebo s qemu verze **2.6 +** , zahrňte možnost `force_size`:

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-an-iso-by-using-a-kickstart-file-automatically"></a>Příprava virtuálního počítače založeného na Red Hat z ISO pomocí souboru Kickstart automaticky

1. Vytvořte soubor Kickstart, který obsahuje následující obsah, a uložte soubor. Zastavování a odinstalace Cloud-init je volitelná (Cloud-init se podporuje v Azure Stackém centru po 1910 vydání). Nainstalujte agenta z úložiště RedHat jenom po vydání 1910. Před 1910 použijte úložiště Azure, jak je to hotové v předchozí části. Podrobnosti o instalaci Kickstart najdete v [Průvodci instalací Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Stop and Uninstall cloud-init
    systemctl stop cloud-init
    yum remove cloud-init
    
    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. Umístěte soubor Kickstart, ke kterému má instalační systém přístup.

1. Ve Správci technologie Hyper-V vytvořte nový virtuální počítač. Na stránce **připojit virtuální pevný disk** vyberte možnost **připojit virtuální pevný disk později**a dokončete Průvodce novým virtuálním počítačem.

1. Otevřete nastavení virtuálního počítače:

    a. Připojte k virtuálnímu počítači nový virtuální pevný disk. Ujistěte se, že jste vybrali **formát VHD** a **pevnou velikost**.

    b. Připojte k jednotce DVD instalaci ISO.

    c. Nastavte systém BIOS na spouštění z disku CD-ROM.

1. Spusťte virtuální počítač. Po zobrazení Průvodce instalací stiskněte klávesu **TAB** a nakonfigurujte možnosti spuštění.

1. Na konci možností spuštění zadejte `inst.ks=<the location of the kickstart file>` a stiskněte klávesu **ENTER**.

1. Počkejte na dokončení instalace. Po dokončení se virtuální počítač automaticky vypne. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="known-issues"></a>Známé problémy

### <a name="the-hyper-v-driver-couldnt-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Při použití hypervisoru, který není Hyper-V, nejde ovladač Hyper-V zahrnout do počátečního disku RAM.

V některých případech nemusí instalační programy pro Linux zahrnovat ovladače pro Hyper-V na počátečním disku RAM (Image initrd nebo initramfs), pokud Linux nezjistí, že je spuštěný v prostředí Hyper-V.

Pokud používáte jiný systém virtualizace (například Oracle VM VirtualBox, projekt Xen atd.) k přípravě image pro Linux, možná budete muset znovu sestavit image initrd, aby se zajistilo, že v počáteční paměti RAM jsou k dispozici alespoň moduly jádra hv_vmbus a hv_storvsc. disk. Jedná se o známý problém minimálně v systémech, které jsou založené na nadřazené distribuci Red Hat.

Pokud chcete tento problém vyřešit, přidejte moduly technologie Hyper-V tak, aby se initramfs a znovu sestavily:

Upravte `/etc/dracut.conf`a přidejte následující obsah:

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

Znovu sestavte initramfs:

```bash
dracut -f -v
```

Další informace najdete v tématu [sestavování initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni k vytváření nových virtuálních počítačů v centru Azure Stack pomocí svého Red Hat Enterprise Linuxho virtuálního pevného disku. Pokud soubor VHD nahráváte poprvé do centra Azure Stack, přečtěte si téma [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Další informace o hypervisorech, které jsou certifikované pro spouštění Red Hat Enterprise Linux, najdete [na webu Red Hat](https://access.redhat.com/certified-hypervisors).
