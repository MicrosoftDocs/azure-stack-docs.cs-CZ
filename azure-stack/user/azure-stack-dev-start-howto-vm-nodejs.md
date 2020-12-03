---
title: Nasazení aplikace Node.js do virtuálního počítače v centru Azure Stack
description: Nasaďte aplikaci Node.js do centra Azure Stack.
author: mattbriggs
ms.topic: overview
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 49a709370f5f3ffebe8cc5ee6d365aaf152bf67b
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524635"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack-hub"></a>Nasazení webové aplikace Node.js do virtuálního počítače v centru Azure Stack

Můžete vytvořit virtuální počítač, který bude hostovat Node.js webové aplikace v centru Azure Stack. V tomto článku jste nastavili server, nakonfigurujete server pro hostování vaší Node.js webové aplikace a pak nasadíte aplikaci do centra Azure Stack.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač v centru Azure Stack podle pokynů v tématu [nasazení virtuálního počítače se systémem Linux pro hostování webové aplikace v centru Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně síť virtuálních počítačů se ujistěte, že jsou dostupné tyto porty:

    | Port | Protokol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) je protokol, který se používá k doručování webových stránek ze serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresou. |
    | 443 | HTTPS | Protokol HTTPS (Hypertext Transfer Protocol Secure) je zabezpečená verze protokolu HTTP, která vyžaduje certifikát zabezpečení a umožňuje šifrovaný přenos informací. |
    | 22 | SSH | Secure Shell (SSH) je zašifrovaný síťový protokol pro zabezpečenou komunikaci. Pomocí tohoto připojení s klientem SSH nakonfigurujete virtuální počítač a nasadíte aplikaci. |
    | 3389 | Protokol RDP | Nepovinný parametr. Protokol RDP (Remote Desktop Protocol) (RDP) umožňuje připojení ke vzdálené ploše pro použití grafického uživatelského rozhraní na vašem počítači.   |
    | 1337 | Vlastní | Port, který používá Node.js. V případě provozního serveru směrujete provoz mezi 80 a 443. |

## <a name="install-node"></a>Instalovat uzel

1. Připojte se k VIRTUÁLNÍmu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojení přes SSH pomocí výstupu](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Na příkazovém řádku bash na svém VIRTUÁLNÍm počítači zadejte následující příkaz:

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [Nainstalujte npm](https://www.npmjs.com/), správce balíčků pro balíčky Node.js nebo moduly. K VIRTUÁLNÍmu počítači se v relaci SSH pořád připojíte zadáním následujícího příkazu:

    ```bash  
       node --version
    ```

3. [Nainstalujte Git](https://git-scm.com), široce distribuovanou správu verzí a systém správy zdrojového kódu (SCM). I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo apt-get -y install git
    ```

3. Ověřte instalaci. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Nastavte úložiště Git na VIRTUÁLNÍm počítači. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. Spusťte aplikaci. I když jste stále připojeni k VIRTUÁLNÍmu počítači v relaci SSH, zadejte následující příkaz:

    ```bash  
       sudo node app.js
    ```

3. Přejít na nový server. Měla by se zobrazit vaše spuštěná webová aplikace.

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vývoji centra Azure Stack](azure-stack-dev-start.md).
- Přečtěte si o [běžných nasazeních centra Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Informace o programovacím jazyku uzlu a vyhledání dalších prostředků pro uzel najdete v tématu [NodeJS.org](https://nodejs.org).
