---
title: Instalace modulu AKS v systému Linux v Azure Stack | Microsoft Docs
description: Naučte se, jak pomocí počítače se systémem Linux v Azure Stack hostovat modul AKS, aby bylo možné nasadit a spravovat cluster Kubernetes.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 3095ede91ce8ac98f1571307c61b28e80aa90fba
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310265"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack"></a>Instalace modulu AKS v systému Linux v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Pomocí počítače se systémem Linux v Azure Stack můžete hostovat modul AKS, aby bylo možné nasadit a spravovat cluster Kubernetes. V tomto článku se podíváme na přípravu klientského virtuálního počítače pro správu clusteru pro připojené i odpojené Azure Stack instance, Projděte si instalaci a nastavte virtuální počítač klienta na ASDK.

## <a name="prepare-the-client-vm"></a>Příprava virtuálního počítače klienta

Modul AKS je nástroj příkazového řádku, který slouží k nasazení a správě clusteru Kubernetes. Modul můžete spustit na počítači v Azure Stack. Z tohoto počítače spustíte modul AKS a nasadíte prostředky IaaS a software potřebný ke spuštění clusteru. Pak můžete pomocí počítače, na kterém je spuštěný modul, provádět úlohy správy v clusteru.

Při volbě klientského počítače zvažte následující:

1. Pokud klientský počítač by měl být v případě havárie obnovitelný.
2. Jak se připojíte ke klientskému počítači a jak bude počítač pracovat s clusterem.

## <a name="install-in-a-connected-environment"></a>Instalace v připojeném prostředí

Můžete nainstalovat klientský virtuální počítač pro správu clusteru Kubernetes na Azure Stack připojeném k Internetu.

1. Ve svém Azure Stack vytvořte virtuální počítač se systémem Linux. Pokyny najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Linux serverem pomocí Azure Stackového portálu](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).
2. Připojte se k VIRTUÁLNÍmu počítači.
3. Vyhledá verzi AKS Engine v tabulce [podporovaných verzí Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) . Základní modul AKS musí být k dispozici na webu Marketplace pro Azure Stack. Při spuštění příkazu je nutné zadat `--version v0.43.0`verze. Pokud nezadáte verzi, příkaz nainstaluje nejnovější verzi, která může potřebovat image VHD, která není na vašem webu Marketplace k dispozici.
4. Spusťte následující příkaz:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.43.0
    ```

    > [!Note]  
    > Pokud se metoda instalace nezdařila, můžete vyzkoušet postup v [odpojeném prostředí](#install-in-a-disconnected-environment)nebo [vyzkoušet GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), jiného správce balíčků.

## <a name="install-in-a-disconnected-environment"></a>Instalace v odpojeném prostředí

Můžete nainstalovat klientský virtuální počítač pro správu clusteru Kubernetes na Azure Stack odpojený od Internetu.

1.  Z počítače s přístupem k Internetu přejděte na GitHub [Azure/AKS-Engine](https://github.com/Azure/aks-engine/releases/latest). Stáhněte si archiv (*. tar. gz) pro počítač se systémem Linux, například `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Vytvořte v instanci Azure Stack účet úložiště pro nahrání souboru archivu (*. tar. gz) s binárním modulem AKS. Pokyny k používání Průzkumník služby Azure Storage najdete v tématu [Průzkumník služby Azure Storage s Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Ve svém Azure Stack vytvořte virtuální počítač se systémem Linux. Pokyny najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Linux serverem pomocí Azure Stackového portálu](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).

3.  Z adresy URL objektu blob Azure Stack účtu úložiště, kam jste nahráli archivní soubor (*. tar. gz), Stáhněte soubor na virtuální počítač pro správu. Extrahujte archiv do adresáře `/usr/local/bin`.

4. Připojte se k VIRTUÁLNÍmu počítači.

5.  Spusťte následující příkaz:

    ```bash  
    curl -o aks-engine-v0.xx.x-linux-amd64.tar.gz <httpurl/aks-engine-v0.xx.x-linux-amd64.tar.gz>
    tar xvzf aks-engine-v0.xx.x-linux-amd64.tar.gz -C /usr/local/bin
    ```

## <a name="verify-the-installation"></a>Ověření instalace

Po nastavení klientského virtuálního počítače ověřte, že máte nainstalovaný modul AKS.

1. Připojte se k VIRTUÁLNÍmu počítači klienta.
2. Spusťte následující příkaz:

    ```bash  
    aks-engine version
    ```

Pokud se nemůžete ověřit, jestli máte na VIRTUÁLNÍm počítači klienta nainstalovaný modul AKS, přečtěte si téma [řešení potíží s instalací modulu AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md) .


## <a name="asdk-installation"></a>Instalace ASDK

Při spuštění virtuálního počítače klienta pro modul AKS na ASDK budete muset přidat certifikát.

Pokud používáte ASDK, váš Azure Resource Manager koncový bod používá certifikát podepsaný svým držitelem, musíte tento certifikát explicitně přidat do důvěryhodného úložiště certifikátů počítače. Kořenový certifikát ASDK najdete na každém virtuálním počítači, který nasadíte v ASDK. Například na virtuálním počítači s Ubuntu najdete ho v tomto adresáři `/var/lib/waagent/Certificates.pem`. 

Zkopírujte soubor certifikátu pomocí následujícího příkazu:

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení clusteru Kubernetes s modulem AKS na Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)