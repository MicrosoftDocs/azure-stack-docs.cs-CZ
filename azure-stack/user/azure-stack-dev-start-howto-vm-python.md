---
title: Nasazení webové aplikace v Pythonu do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení webové aplikace v Pythonu do virtuálního počítače ve službě Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: f37e963ad73a361f9d4cd5a6e68ec4213d5f32fb
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838311"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>Nasazení webové aplikace v Pythonu do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač pro hostování vaší webové aplikace v Pythonu ve službě Azure Stack. V tomto článku se nastavení serveru, nakonfigurujte server k hostování vaší webové aplikace v Pythonu a pak nasadíte aplikaci do služby Azure Stack.

Tento článek používá Python 3.x spuštěných Flask na serveru Nginx ve virtuálním prostředí.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavení virtuálního počítače ve službě Azure Stack podle pokynů v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně sítě virtuálních počítačů Ujistěte se, že jsou dostupné následující porty:

    | Port | Protocol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol, který slouží k doručování webových stránkách od serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresu. |
    | 443 | HTTPS | Protokol zabezpečení HTTPS (Hypertext Transfer) je zabezpečený verzi protokolu HTTP, který vyžaduje certifikát zabezpečení a umožňuje šifrovaného přenosu informací. |
    | 22 | SSH | Secure Shell (SSH) je protokol šifrovaných sítí pro zabezpečenou komunikaci. Pomocí tohoto připojení klienta SSH pro konfiguraci virtuálního počítače a nasaďte aplikaci. |
    | 3389 | Protokol RDP | Volitelné. Protokol RDP (Remote Desktop) umožňuje připojení ke vzdálené ploše na pomocí grafického uživatelského rozhraní na svém počítači.   |
    | 5000, 8000 | Vlastní | Porty, které jsou používány architektury Flask webů ve vývoji. Pro produkční server směrovat provoz přes 80 a 443. |

## <a name="install-python"></a>Instalace Pythonu

1. Připojení k vašemu virtuálnímu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).
2. Na řádku prostředí bash ve virtuálním počítači zadejte následující příkaz:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Ověření instalace. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
        python -version
    ```

3. [Instalaci serveru Nginx](https://www.nginx.com/resources/wiki/), odlehčeného webového serveru. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [Instalace Gitu](https://git-scm.com), široce distribuovanou správu verzí a zdrojový kód systému pro správu (SCM). Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Nastavení úložiště Git na virtuálním počítači. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Vytvořit virtuální prostředí a přidejte do ní všechny závislosti balíčků. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. Přejdete na nový server. Měli byste vidět spuštěné webové aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Aktualizovat server

1. Připojte se ke svému virtuálnímu počítači v relaci SSH. Zastavte službu zadáním kombinace kláves Ctrl + C.

2. Zadejte následující příkazy:

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. Aktivujte virtuální prostředí a spusťte aplikaci:

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md).
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Přečtěte si programovacího jazyka Python a najít další zdroje informací pro Python najdete v tématu [Python.org](https://www.python.org).
