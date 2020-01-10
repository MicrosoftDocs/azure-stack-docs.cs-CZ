---
title: Nasazení webové aplikace v Pythonu na virtuální počítač v centru Azure Stacke | Microsoft Docs
description: Nasaďte webovou aplikaci v Pythonu na virtuální počítač v Azure Stackovém centru.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 6beefec2f912d69e6772980f54c84e93f615de02
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820555"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack-hub"></a>Nasazení webové aplikace v Pythonu do virtuálního počítače v Azure Stackovém centru

Můžete vytvořit virtuální počítač pro hostování webové aplikace v Pythonu v centru Azure Stack. V tomto článku jste nastavili server, nakonfigurujete server pro hostování webové aplikace v Pythonu a pak nasadíte aplikaci do centra Azure Stack.

V tomto článku se používá Python 3. x, ve kterém je ve virtuálním prostředí na serveru Nginx.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač v centru Azure Stack podle pokynů v tématu [nasazení virtuálního počítače se systémem Linux pro hostování webové aplikace v centru Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně síť virtuálních počítačů se ujistěte, že jsou dostupné tyto porty:

    | Port | Protocol (Protokol) | Popis |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) je protokol, který se používá k doručování webových stránek ze serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresou. |
    | 443 | HTTPS | Protokol HTTPS (Hypertext Transfer Protocol Secure) je zabezpečená verze protokolu HTTP, která vyžaduje certifikát zabezpečení a umožňuje šifrovaný přenos informací. |
    | 22 | SSH | Secure Shell (SSH) je zašifrovaný síťový protokol pro zabezpečenou komunikaci. Pomocí tohoto připojení s klientem SSH nakonfigurujete virtuální počítač a nasadíte aplikaci. |
    | 3389 | Protokol RDP | Nepovinný parametr. Protokol RDP (Remote Desktop Protocol) (RDP) umožňuje připojení ke vzdálené ploše pro použití grafického uživatelského rozhraní na vašem počítači.   |
    | 5000, 8000 | Vlastní | Porty používané webovým rozhraním v baňce pro vývoj. V případě provozního serveru směrujete provoz mezi 80 a 443. |

## <a name="install-python"></a>Instalace Pythonu

1. Připojte se k VIRTUÁLNÍmu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojení přes SSH pomocí výstupu](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).
2. Na příkazovém řádku bash na svém VIRTUÁLNÍm počítači zadejte následující příkaz:

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. Ověřte instalaci. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
        python -version
    ```

3. [Nainstalujte Nginx](https://www.nginx.com/resources/wiki/), jednoduchý webový server. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [Nainstalujte Git](https://git-scm.com), široce distribuovanou správu verzí a systém správy zdrojového kódu (SCM). I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Nastavte úložiště Git na VIRTUÁLNÍm počítači. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. Vytvořte virtuální prostředí a naplňte ho všemi závislostmi balíčku. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. Přejít na nový server. Měla by se zobrazit vaše spuštěná webová aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>Aktualizace serveru

1. Připojte se k VIRTUÁLNÍmu počítači v relaci SSH. Zastavte Server zadáním kombinace kláves CTRL + C.

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

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vývoji centra Azure Stack](azure-stack-dev-start.md).
- Přečtěte si o [běžných nasazeních centra Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Informace o programovacím jazyce Pythonu a o dalších prostředcích pro Python najdete v tématu [Python.org](https://www.python.org).
