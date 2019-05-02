---
title: Jak nasadit webové aplikace v Pythonu do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení webové aplikace v Pythonu do virtuálního počítače ve službě Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b1a588dd084962c095a534f6569333b34e694bc6
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481838"
---
# <a name="how-to-deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Jak nasadit webovou aplikaci Python k virtuálnímu počítači v Azure stacku

Můžete vytvořit virtuální počítač pro hostování vaší aplikace webového Pythonu ve službě Azure Stack. Tento článek ukazuje postup popsaný v části Nastavení serveru, konfigurace serveru pro hostování vaší webové aplikace v Pythonu a potom nasazení vaší aplikace.

Python je programovací jazyk interpretované, vysoké úrovně, pro obecné účely. Vytvořené Michal van Rossum a poprvé zavedené ve 1991, má Python filosofie návrhu tříd, s důrazem na čitelnost kódu, zejména díky významný prázdný prostor. Poskytuje konstruktory, které umožňují jasné programování na malé i velké škály. Přečtěte si programovacího jazyka Python a najít další zdroje informací pro Python najdete v tématu [Python.org](https://www.python.org).

Tento článek bude používat Python Flask běží ve virtuálním prostředí na serveru pro Ngnix 3.x.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač nastavit ve službě Azure Stack. Postupujte podle kroků v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V okně sítě virtuálních počítačů Ujistěte se, že jsou k dispozici následující porty:

    | Port | Protocol (Protokol) | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol aplikací pro distribuované, spolupráci, hypermédia informačních systémů. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 443 | HTTPS | Přenos protokolu HTTPS (Hypertext Secure) je rozšířením sady protokol HTTP (Hypertext Transfer). Používá se pro zabezpečenou komunikaci v počítačové síti. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 22 | SSH | Secure Shell (SSH) je kryptografický síťový protokol pro bezpečné provozování síťové služby přes nezabezpečenou síť. Toto připojení použijete s klientem SSH ke konfiguraci virtuálního počítače a nasazení aplikace. |
    | 3389 | Protokol RDP | Volitelné. Remote Desktop Protocol umožňuje připojení ke vzdálené ploše použít grafické uživatelské rozhraní vašeho počítače.   |
    | 5000, 8000 | Vlastní | Porty 5000 8000 využívají webové rozhraní Flask ve vývoji. Pro produkční server budete chtít směrovat provoz přes 80 a 443. |

## <a name="install-python"></a>Instalace Pythonu

1. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. Na řádku prostředí bash ve virtuálním počítači zadejte následující příkazy:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Ověření instalace. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
        python -version
    ```


3. Nainstalujete server Nginx. [Server Nginx](https://www.nginx.com/resources/wiki/) je webový server, který může také sloužit jako reverzní proxy server, načítání nástroje pro vyrovnávání, poštovní server proxy a mezipaměť HTTP. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. Instalace Gitu. [Git](https://git-scm.com) je odesílaných revize ovládacího prvku a zdrojového kódu (SCM) systému pro správu. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Nastavení úložiště Git na virtuálním počítači. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Vytvořit virtuální prostředí a přidejte do ní všechny závislosti balíčků.  Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3.  Teď přejděte na nový server a měli byste vidět spuštěné webové aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Aktualizovat server

1. Připojte se ke svému virtuálnímu počítači v relaci SSH. Zastavení serveru tak, že zadáte `CTRL+C`.
2. Zadejte následující příkazy:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Při aktivaci virtuálního prostředí a aplikace

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md)
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
