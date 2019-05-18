---
title: Nasazení aplikace v Ruby do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Nasaďte aplikaci Ruby k virtuálnímu počítači v Azure stacku.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: f7867e963551d04336ac4092e8b1b2f033c29e94
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838336"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Nasazení webové aplikace Ruby k virtuálnímu počítači v Azure stacku

Můžete vytvořit virtuální počítač pro hostování vaší webové aplikace Ruby ve službě Azure Stack. V tomto článku se nastavení serveru, nakonfigurujte server k hostování vaší webové aplikace Ruby a pak nasadíte aplikaci do služby Azure Stack.

Tento článek používá Ruby a Ruby v Rails webovou architekturu.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavení virtuálního počítače ve službě Azure Stack. Pokyny najdete v tématu [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně sítě virtuálních počítačů Ujistěte se, že jsou k dispozici následující porty:

    | Port | Protocol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol, který slouží k doručování webových stránkách od serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresu. |
    | 443 | HTTPS | Protokol zabezpečení HTTPS (Hypertext Transfer) je zabezpečený verzi protokolu HTTP, který vyžaduje certifikát zabezpečení a umožňuje šifrovaného přenosu informací. |
    | 22 | SSH | Secure Shell (SSH) je protokol šifrovaných sítí pro zabezpečenou komunikaci. Pomocí tohoto připojení klienta SSH pro konfiguraci virtuálního počítače a nasaďte aplikaci. |
    | 3389 | Protokol RDP | Volitelné. Protokol RDP (Remote Desktop) umožňuje připojení ke vzdálené ploše na pomocí grafického uživatelského rozhraní na svém počítači.   |
    | 3000 | Vlastní | Port, který se používá Ruby on Rails webovou architekturu ve vývoji. Pro produkční server směrovat provoz přes 80 a 443. |

## <a name="install-ruby"></a>Instalace Ruby

1. Připojení k vašemu virtuálnímu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Nainstalujte PPA úložiště. Na příkazovém řádku bash ve virtuálním počítači zadejte následující příkazy:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Nainstalujte Ruby a Ruby on Rails na virtuálním počítači. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Nainstalujte rámec Ruby on Rails závislosti. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > Když instalujete Ruby na závislosti Rails, možná budete muset spouštět opakovaně `sudo gem install bundler`. Pokud se instalace nezdaří, zkontrolujte protokoly chyb a vyřešit problémy.

4. Ověření instalace. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
        ruby -v
    ```

3. [Instalace Gitu](https://git-scm.com), široce distribuovanou správu verzí a zdrojový kód systému pro správu (SCM). Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Vytvoření a spuštění aplikace

1. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. Přejdete na nový server. Měli byste vidět spuštěné webové aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md).
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Přečtěte si poznámky Ruby programovací jazyk a najít další zdroje informací pro Python najdete v tématu [Ruby lang.org](https://www.ruby-lang.org).
