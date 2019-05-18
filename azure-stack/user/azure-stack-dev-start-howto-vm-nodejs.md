---
title: Nasazení aplikace v Node.js do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení aplikace Node.js do služby Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 4fcf76b8f4950fa7ca919d57281c5662b31e96f6
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838302"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack"></a>Nasazení webové aplikace Node.js do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač (VM) pro hostování webové aplikace Node.js ve službě Azure Stack. V tomto článku se nastavení serveru, nakonfigurujte server k hostování webové aplikace v Node.js a pak nasadíte aplikaci do služby Azure Stack.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavení virtuálního počítače ve službě Azure Stack podle pokynů v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně sítě virtuálních počítačů Ujistěte se, že jsou dostupné následující porty:

    | Port | Protocol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol, který slouží k doručování webových stránkách od serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresu. |
    | 443 | HTTPS | Protokol zabezpečení HTTPS (Hypertext Transfer) je zabezpečený verzi protokolu HTTP, který vyžaduje certifikát zabezpečení a umožňuje šifrovaného přenosu informací. |
    | 22 | SSH | Secure Shell (SSH) je protokol šifrovaných sítí pro zabezpečenou komunikaci. Pomocí tohoto připojení klienta SSH pro konfiguraci virtuálního počítače a nasaďte aplikaci. |
    | 3389 | Protokol RDP | Volitelné. Protokol RDP (Remote Desktop) umožňuje připojení ke vzdálené ploše na pomocí grafického uživatelského rozhraní na svém počítači.   |
    | 1337 | Vlastní | Port, který je používán Node.js. Pro produkční server směrovat provoz přes 80 a 443. |

## <a name="install-node"></a>Instalace uzlu

1. Připojení k vašemu virtuálnímu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Na příkazovém řádku bash ve virtuálním počítači zadejte následující příkaz:

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [Nainstalujte NPM](https://www.npmjs.com/), Správce balíčků pro Node.js balíčky nebo moduly. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       go version
    ```

3. [Instalace Gitu](https://git-scm.com), široce distribuované správy verzí a zdrojový kód systému pro správu (SCM). Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install git
    ```

3. Ověření instalace. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Nastavení úložiště Git na virtuálním počítači. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. Spusťte aplikaci. Když jste stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo node app.js
    ```

3. Přejdete na nový server. Měli byste vidět spuštěné webové aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md).
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Přečtěte si uzel programovací jazyk a najít další zdroje pro uzel najdete v tématu [Nodejs.org](https://nodejs.org).
