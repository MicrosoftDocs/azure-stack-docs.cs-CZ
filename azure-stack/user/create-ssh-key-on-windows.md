---
title: Nasazení clusteru Kubernetes do vlastní virtuální sítě v centru Azure Stack
description: Naučte se, jak nasadit cluster Kubernetes do vlastní virtuální sítě v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: ff66cdaffd4ffc7a6896edf0191871669ac84b16
ms.sourcegitcommit: 4301e8dee16b4db32b392f5979dfec01ab6566c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79318023"
---
# <a name="create-an-ssh-key-for-linux-on-azure-stack-hub"></a>Vytvoření klíče SSH pro Linux v centru Azure Stack

Můžete vytvořit klíč SSH (Secure Shell) pro počítač se systémem Linux na počítači s Windows. Použijte veřejný klíč vygenerovaný kroky v tomto článku pro ověřování SSH pomocí virtuálních počítačů. Pokud používáte Windows Machine Ubuntu v systému Windows k získání terminálu s nástroji, jako jsou bash, SSH, Git a Ubuntu ve Windows. Vytvořte klíč spuštěním **ssh-keygen** .

## <a name="open-bash-on-windows"></a>Otevřít bash ve Windows

1. Pokud na svém počítači nemáte nainstalovaný podsystém Windows pro Linux, nainstalujte v[systému Windows Ubuntu](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab).  
    Další informace o používání subsystému Windows pro Linux najdete v dokumentaci k subsystému [Windows pro Linux](https://docs.microsoft.com/windows/wsl/about).

2. Na panelu nástrojů zadejte **Ubuntu** a vyberte **otevřít**.

## <a name="create-a-key-with-ssh-keygen"></a>Vytvoření klíče pomocí ssh-keygen

1. Do příkazového řádku bash zadejte následující příkaz:

    ```bash  
    ssh-keygen -t rsa
    ```

    Bash zobrazí následující dotaz:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

2. Zadejte název souboru a přístupové heslo. Znovu zadejte heslo.

    Bash zobrazí následující:

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa): key.txt
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in key.txt.
    Your public key has been saved in key.txt.pub.
    The key fingerprint is:
    SHA256:xanotrealoN6z1/KChqeah0CYVeyhL50/0rq37qgy6Ik username@machine
    The key's randomart image is:
    +---[RSA 2048]----+
    |   o.     .      |
    |  . o.   +       |
    | + o .+ o o      |
    |o o .  O +       |
    | . o .o S .      |
    |  o +. .         |
    |.  o +..o. .     |
    |= . ooB +o+ .    |
    |E=..*X=*.. +.    |
    +----[SHA256]-----+
    ```

3. Zobrazení a veřejný klíč SSH:

    ```bash
    cat /home/<username>/<filename>
    ```

    Bash zobrazí něco podobného jako následující:

    ```bash
    ssh-rsa AAAAB3NzaC1ycTHISISANEXAMPLEDITqEJRNrf6tXy9c0vKnMhiol1BFzHFV3
    +suXk6NDeFcA9uI58VdD/CuvG826R+3OPnXutDdl2MLyH3DGG1fJAHObUWQxmDWluhSGb
    JMHiw2L9Wnf9klG6+qWLuZgjB3TQdus8sZI8YdB4EOIuftpMQ1zkAJRAilY0p4QxHhKbU
    IkvWqBNR+rd5FcQx33apIrB4LMkjd+RpDKOTuSL2qIM2+szhdL5Vp5Y6Z1Ut1EpOrkbg1
    cVw7oW0eP3ROPdyNqnbi9m1UVzB99aoNXaepmYviwJGMzXsTkiMmi8Qq+F8/qy7i4Jxl0
    aignia880qOtQrvNEvyhgZOM5oDhgE3IJ username@machine
    ```

4. Zkopírujte text `ssh-rsa [...]` až do `username@machinename`. Ujistěte se, že text neobsahuje žádné návratové znaky. Tento text můžete použít při vytváření virtuálního počítače nebo clusteru Kubernetes pomocí modulu AKS.

5. Pokud jste na počítači s Windows, můžete získat přístup k souborům Linux pomocí **\\\\WSL $** .

    1. Do panelu nástrojů zadejte `\\wsl$`. Výchozí okno, ve kterém je vaše distribuce otevřená

    2. Přejděte na: `\\wsl$\Ubuntu\home\<username>` a vyhledejte veřejný a privátní klíč a uložte je do zabezpečeného umístění.

## <a name="next-steps"></a>Další kroky

- [Nasazení clusteru Kubernetes s modulem AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
- [Rychlý Start: Vytvoření virtuálního počítače s Linux serverem pomocí portálu Azure Stack hub](azure-stack-quick-linux-portal.md)