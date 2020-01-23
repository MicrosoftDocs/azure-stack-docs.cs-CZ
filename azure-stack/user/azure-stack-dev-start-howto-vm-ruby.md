---
title: Nasazení aplikace v Ruby do virtuálního počítače v centru Azure Stacke | Microsoft Docs
description: Nasaďte aplikaci Ruby do virtuálního počítače v Azure Stackovém centru.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: d815cbcd434bab4176afa3fc853b1c1fbfaf571a
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536177"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack-hub"></a>Nasazení webové aplikace v Ruby do virtuálního počítače v Azure Stackovém centru

Můžete vytvořit virtuální počítač pro hostování webové aplikace v Ruby v centru Azure Stack. V tomto článku jste nastavili server, nakonfigurujete server pro hostování webové aplikace Ruby a pak nasadíte aplikaci do centra Azure Stack.

V tomto článku se používají webové rozhraní Ruby a Ruby na železnici.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač v centru Azure Stack. Pokyny najdete v tématu [nasazení virtuálního počítače se systémem Linux pro hostování webové aplikace v centru Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně síť virtuálních počítačů zkontrolujte, že jsou dostupné tyto porty:

    | Port | Protocol (Protokol) | Popis |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) je protokol, který se používá k doručování webových stránek ze serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresou. |
    | 443 | HTTPS | Protokol HTTPS (Hypertext Transfer Protocol Secure) je zabezpečená verze protokolu HTTP, která vyžaduje certifikát zabezpečení a umožňuje šifrovaný přenos informací. |
    | 22 | SSH | Secure Shell (SSH) je zašifrovaný síťový protokol pro zabezpečenou komunikaci. Pomocí tohoto připojení s klientem SSH nakonfigurujete virtuální počítač a nasadíte aplikaci. |
    | 3389 | Protokol RDP | Nepovinný parametr. Protokol RDP (Remote Desktop Protocol) (RDP) umožňuje připojení ke vzdálené ploše pro použití grafického uživatelského rozhraní na vašem počítači.   |
    | 3000 | Vlastní | Port používaný webovým rozhraním Ruby on Kolejnices ve vývoji. V případě provozního serveru směrujete provoz mezi 80 a 443. |

## <a name="install-ruby"></a>Instalace Ruby

1. Připojte se k VIRTUÁLNÍmu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojení přes SSH pomocí výstupu](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Nainstalujte úložiště PPA. Na příkazovém řádku bash na svém VIRTUÁLNÍm počítači zadejte následující příkazy:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Nainstalujte do svého virtuálního počítače Ruby a Ruby na kolejnicích. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Nainstalujte závislosti Ruby na kolejnicích. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > Když instalujete závislosti Ruby na železnici, možná budete muset opakovaně spouštět `sudo gem install bundler`. Pokud se instalace nezdařila, zkontrolujte protokoly chyb a vyřešte problémy.

4. Ověřte instalaci. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
        ruby -v
    ```

3. [Nainstalujte Git](https://git-scm.com), široce distribuovanou správu verzí a systém správy zdrojového kódu (SCM). I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Vytvoření a spuštění aplikace

1. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkazy:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. Přejít na nový server. Měla by se zobrazit vaše spuštěná webová aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vývoji centra Azure Stack](azure-stack-dev-start.md).
- Přečtěte si o [běžných nasazeních centra Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Informace o programovacím jazyce Ruby a hledání dalších zdrojů pro Ruby najdete v tématu [Ruby-lang.org](https://www.ruby-lang.org).
