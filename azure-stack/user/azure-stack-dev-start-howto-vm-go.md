---
title: Jak nasadit webovou aplikaci přejděte do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Jak nasadit webovou aplikaci přejděte do virtuálního počítače ve službě Azure Stack
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: c0cef076522e77a6d0fdafbd8848d5e9bb8a90a8
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481915"
---
# <a name="how-to-deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>Jak nasadit webovou aplikaci přejděte do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač pro hostování přejít webové aplikace ve službě Azure Stack. Tento článek ukazuje postup popsaný v části Nastavení serveru, konfigurace serveru pro hostování webové aplikace GO a poté Nasaďte aplikaci.

Go je výrazová, ucelená, čisté a efektivní. Jeho souběžnosti mechanismy usnadňují psaní programů, které naplno využít Vícejádrová a síťově počítače, jeho typ systému umožňuje flexibilní a modulární program konstrukce. Další programovací jazyk Go a najít další zdroje informací pro GO, přečtěte si téma [Golang.org](https://golang.org).

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač nastavit ve službě Azure Stack. Postupujte podle kroků v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V okně sítě virtuálních počítačů Ujistěte se, že jsou k dispozici následující porty:

    | Port | Protocol (Protokol) | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol aplikací pro distribuované, spolupráci, hypermédia informačních systémů. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 443 | HTTPS | Přenos protokolu HTTPS (Hypertext Secure) je rozšířením sady protokol HTTP (Hypertext Transfer). Používá se pro zabezpečenou komunikaci v počítačové síti. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 22 | SSH | Secure Shell (SSH) je kryptografický síťový protokol pro bezpečné provozování síťové služby přes nezabezpečenou síť. Toto připojení použijete s klientem SSH ke konfiguraci virtuálního počítače a nasazení aplikace. |
    | 3389 | Protokol RDP | Volitelné. Remote Desktop Protocol umožňuje připojení ke vzdálené ploše použít grafické uživatelské rozhraní vašeho počítače.   |
    | 3000 | Vlastní | Port 3000 používají webové rozhraní GO ve vývoji. Pro produkční server budete chtít směrovat provoz přes 80 a 443. |

## <a name="install-go"></a>Nainstalujte jazyk GO

1. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Na řádku prostředí bash ve virtuálním počítači zadejte následující příkazy:

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. Nastavení prostředí cestách na vašem virtuálním počítači. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. Ověření instalace. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
        go version
    ```

3. Instalace Gitu. [Git](https://git-scm.com) je odesílaných revize ovládacího prvku a zdrojového kódu (SCM) systému pro správu. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Nastavení úložiště Git na virtuálním počítači. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. Spusťte aplikaci. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       go run hello-world.go
    ```

3.  Teď přejděte na nový server a měli byste vidět spuštěné webové aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md)
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).