---
title: Nasazení webové aplikace přejděte do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Jak nasadit webovou aplikaci přejděte do virtuálního počítače ve službě Azure Stack
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 8b1f207929e018b27bb3f20db8d2b7d5abe46088
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838348"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>Nasazení webové aplikace přejděte do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač (VM) pro hostování webových aplikací Go ve službě Azure Stack. V tomto článku se nastavení serveru, konfigurace serveru pro hostování webové aplikace Go a pak nasadíte aplikaci do služby Azure Stack.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavení virtuálního počítače ve službě Azure Stack podle pokynů v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně sítě virtuálních počítačů Ujistěte se, že jsou dostupné následující porty:

    | Port | Protocol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol, který slouží k doručování webových stránkách od serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresu. |
    | 443 | HTTPS | Protokol zabezpečení HTTPS (Hypertext Transfer) je zabezpečený verzi protokolu HTTP, který vyžaduje certifikát zabezpečení a umožňuje šifrovaného přenosu informací. |
    | 22 | SSH | Secure Shell (SSH) je protokol šifrovaných sítí pro zabezpečenou komunikaci. Pomocí tohoto připojení klienta SSH pro konfiguraci virtuálního počítače a nasaďte aplikaci. |
    | 3389 | Protokol RDP | Volitelné. Protokol RDP (Remote Desktop) umožňuje připojení ke vzdálené ploše na pomocí grafického uživatelského rozhraní na svém počítači.   |
    | 3000 | Vlastní | Port 3000 používají webové rozhraní Go ve vývoji. Pro produkční server směrovat provoz přes 80 a 443. |

## <a name="install-go"></a>Nainstalujte jazyk Go

1. Připojení k vašemu virtuálnímu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTY ](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Na příkazovém řádku bash ve virtuálním počítači zadejte následující příkazy:

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Nastavení prostředí cestách na vašem virtuálním počítači. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. Ověření instalace. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
        go version
    ```

3. [Instalace Gitu](https://git-scm.com), široce distribuovanou správu verzí a zdrojový kód systému pro správu (SCM). Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Nastavení úložiště Git na virtuálním počítači. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Spusťte aplikaci. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       go run hello-world.go
    ```

3. Přejdete na nový server. Měli byste vidět spuštěné webové aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md).
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Další programovací jazyk Go a najít další zdroje informací pro Go, přečtěte si téma [Golang.org](https://golang.org).
