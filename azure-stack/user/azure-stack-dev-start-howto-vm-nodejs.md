---
title: Nasazení aplikace Node.js do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení aplikace Node.js do služby Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: bebf3b349a994379d5f54bd387533b8d4a63ccdd
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481926"
---
# <a name="how-to-deploy-a-nodejs-web-app-to-a-vm-in-azure-stack"></a>Jak nasadit webové aplikace v Node.js do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač pro hostování vaší webové aplikace v Node.js ve službě Azure Stack. Tento článek ukazuje postup popsaný v části Nastavení serveru, konfigurace serveru pro hostování webové aplikace uzlu a pak nasazení vaší aplikace.

Platforma Node.js je modul runtime jazyka JavaScript založená na modulu V8 JavaScript v Chrome. Jako asynchronní založená na událostech modulu runtime jazyka JavaScript uzel umožňuje sestavovat škálovatelné síťové aplikace. Přečtěte si uzel programovací jazyk a najít další zdroje pro uzel najdete v tématu [Nodejs.org](https://nodejs.org).

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač nastavit ve službě Azure Stack. Postupujte podle kroků v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V okně sítě virtuálních počítačů Ujistěte se, že jsou k dispozici následující porty:

    | Port | Protocol (Protokol) | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol aplikací pro distribuované, spolupráci, hypermédia informačních systémů. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 443 | HTTPS | Přenos protokolu HTTPS (Hypertext Secure) je rozšířením sady protokol HTTP (Hypertext Transfer). Používá se pro zabezpečenou komunikaci v počítačové síti. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 22 | SSH | Secure Shell (SSH) je kryptografický síťový protokol pro bezpečné provozování síťové služby přes nezabezpečenou síť. Toto připojení použijete s klientem SSH ke konfiguraci virtuálního počítače a nasazení aplikace. |
    | 3389 | Protokol RDP | Volitelné. Remote Desktop Protocol umožňuje připojení ke vzdálené ploše použít grafické uživatelské rozhraní vašeho počítače.   |
    | 1337 | Vlastní | Port 1337 používají na Node.js. Pro produkční server budete chtít směrovat provoz přes 80 a 443. |

## <a name="install-node"></a>Instalace uzlu

1. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Na řádku prostředí bash ve virtuálním počítači zadejte následující příkazy:

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. Nainstalujte NPM. [NPM](https://www.npmjs.com/) je Správce balíčků pro Node.js balíčky nebo moduly. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       go version
    ```

3. Instalace Gitu. [Git](https://git-scm.com) je odesílaných revize ovládacího prvku a zdrojového kódu (SCM) systému pro správu. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       sudo apt-get -y install git
    ```

3. Ověření instalace. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Nastavení úložiště Git na virtuálním počítači. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. Spusťte aplikaci. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo node app.js
    ```

3.  Teď přejděte na nový server a měli byste vidět spuštěné webové aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md)
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).