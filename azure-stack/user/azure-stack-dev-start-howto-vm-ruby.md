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
ms.openlocfilehash: 24774e122be0c4088c4d5841f71318d266bad78f
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782679"
---
# <a name="how-to-deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>Nasazení webové aplikace Ruby na virtuálním počítači v Azure stacku

Můžete vytvořit virtuální počítač pro hostování Ruby webové aplikace ve službě Azure Stack. Tento článek ukazuje postup popsaný v části Nastavení serveru, konfigurace serveru pro hostování vaší webové aplikace Ruby a potom nasazení vaší aplikace.



Tento článek používá na Rails webová architektura Ruby a Ruby.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač nastavit ve službě Azure Stack. Postupujte podle kroků v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V okně sítě virtuálních počítačů Ujistěte se, že jsou k dispozici následující porty:

    | Port | Protocol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol použitý k doručování webových stránek ze serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresu. |
    | 443 | HTTPS | Protokol zabezpečení HTTPS (Hypertext Transfer) je zabezpečený verzi protokolu HTTP, který vyžaduje certifikát zabezpečení a umožňuje šifrovaného přenosu informací.  |
    | 22 | SSH | Secure Shell (SSH) je protokol šifrovaných sítí pro zabezpečenou komunikaci. Toto připojení použijete s klientem SSH ke konfiguraci virtuálního počítače a nasazení aplikace. |
    | 3389 | Protokol RDP | Volitelné. Remote Desktop Protocol umožňuje připojení ke vzdálené ploše použít grafické uživatelské rozhraní vašeho počítače.   |
    | 3000 | Vlastní | Port 3000 používá rozhraní web Ruby na rails ve vývoji. Pro produkční server budete chtít směrovat provoz přes 80 a 443. |

## <a name="install-ruby"></a>Instalace Ruby

1. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Nainstalujte PPA úložiště. Na řádku prostředí bash ve virtuálním počítači zadejte následující příkazy:

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. Nainstalujte Ruby a Ruby rails na virtuálním počítači. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. Nainstalujte Ruby, rails závislosti. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > V instalaci, je nutné spouštět opakovaně `sudo gem install bundler`. Závislosti pokusu o instalaci a nezdaří, zkontrolujte protokoly chyby a vyřešit problémy.

4. Ověření instalace. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
        ruby -v
    ```

3. Instalace Gitu. [Git](https://git-scm.com) je odesílaných revize ovládacího prvku a zdrojového kódu (SCM) systému pro správu. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>Vytvoření a spuštění aplikace

1. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2.  Teď přejděte na nový server a měli byste vidět spuštěné webové aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md)
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Přečtěte si poznámky Ruby programovací jazyk a najít další zdroje informací pro Python najdete v tématu [Ruby lang.org](https://www.ruby-lang.org).