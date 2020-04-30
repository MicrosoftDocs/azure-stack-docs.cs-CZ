---
title: Instalace modulu AKS v systému Linux v centru Azure Stack
description: Naučte se, jak pomocí počítače se systémem Linux v centru Azure Stack hostovat modul AKS, aby bylo možné nasadit a spravovat cluster Kubernetes.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: 5a6089a405e860bb25e52e61e273eb5094bc3647
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82556435"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack-hub"></a>Instalace modulu AKS v systému Linux v centru Azure Stack

Pomocí počítače se systémem Linux v centru Azure Stack můžete hostovat modul AKS, aby bylo možné nasadit a spravovat cluster Kubernetes. V tomto článku se podíváme na přípravu klientského virtuálního počítače pro správu clusteru pro připojení i odpojení Azure Stackch instancí, Projděte si instalaci a nastavte virtuální počítač klienta na ASDK.

## <a name="prepare-the-client-vm"></a>Příprava virtuálního počítače klienta

Modul AKS je nástroj příkazového řádku, který slouží k nasazení a správě clusteru Kubernetes. Modul můžete spustit na počítači v centru Azure Stack. Z tohoto počítače spustíte modul AKS a nasadíte prostředky IaaS a software potřebný ke spuštění clusteru. Pak můžete pomocí počítače, na kterém je spuštěný modul, provádět úlohy správy v clusteru.

Při volbě klientského počítače zvažte následující:

1. Pokud klientský počítač by měl být v případě havárie obnovitelný.
2. Jak se připojíte ke klientskému počítači a jak bude počítač pracovat s clusterem.

## <a name="install-in-a-connected-environment"></a>Instalace v připojeném prostředí

Můžete nainstalovat klientský virtuální počítač pro správu clusteru Kubernetes na rozbočovači Azure Stack připojeném k Internetu.

1. Vytvořte virtuální počítač se systémem Linux v centru Azure Stack. Pokyny najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Linux serverem pomocí portálu Azure Stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).
2. Připojte se k VIRTUÁLNÍmu počítači.
3. Vyhledá verzi AKS Engine v tabulce [podporovaných verzí Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) . V tržišti centra Azure Stack musí být dostupná základní image AKS. Při spuštění příkazu je nutné zadat verzi `--version v0.48.0`. Pokud nezadáte verzi, příkaz nainstaluje nejnovější verzi, která může potřebovat image VHD, která není na vašem webu Marketplace k dispozici.
4. Spusťte následující příkaz:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.48.0
    ```

    > [!Note]  
    > Pokud se metoda instalace nezdařila, můžete vyzkoušet postup v [odpojeném prostředí](#install-in-a-disconnected-environment)nebo [vyzkoušet GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), jiného správce balíčků.

## <a name="install-in-a-disconnected-environment"></a>Instalace v odpojeném prostředí

Můžete nainstalovat klientský virtuální počítač pro správu clusteru Kubernetes na rozbočovači Azure Stack odpojený od Internetu.

1.  Z počítače s přístupem k Internetu přejděte na GitHub [Azure/AKS-Engine](https://github.com/Azure/aks-engine/releases/latest). Stáhněte si archiv (*. tar. gz) pro počítač se systémem Linux, například `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Vytvořte účet úložiště v instanci centra Azure Stack, abyste nahráli archivní soubor (*. tar. gz) s binárním modulem AKS. Pokyny k používání Průzkumník služby Azure Storage najdete v tématu [Průzkumník služby Azure Storage pomocí centra Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Vytvořte virtuální počítač se systémem Linux v centru Azure Stack. Pokyny najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Linux serverem pomocí portálu Azure Stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).

3.  Z adresy URL objektu BLOB účtu úložiště centra Azure Stack, kam jste nahráli archivní soubor (*. tar. gz), Stáhněte soubor na virtuální počítač pro správu. Extrahujte archiv do adresáře `/usr/local/bin`.

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

3. Pokud Azure Resource Manager koncový bod používá certifikát podepsaný svým držitelem, je nutné explicitně přidat kořenový certifikát do důvěryhodného úložiště certifikátů daného počítače. Kořenový certifikát najdete na virtuálním počítači v tomto adresáři:/var/lib/waagent/Certificates.pem. Zkopírujte soubor certifikátu pomocí následujícího příkazu: 

   ```bash
   sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt 
   sudo update-ca-certificates
   ```

Pokud se nemůžete ověřit, jestli máte na VIRTUÁLNÍm počítači klienta nainstalovaný modul AKS, přečtěte si téma [řešení potíží s instalací modulu AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md) .


## <a name="asdk-installation"></a>Instalace ASDK

Při spuštění virtuálního počítače klienta pro modul AKS na ASDK budete muset přidat certifikát.

Pokud používáte ASDK, váš Azure Resource Manager koncový bod používá certifikát podepsaný svým držitelem, musíte tento certifikát explicitně přidat do důvěryhodného úložiště certifikátů počítače. Kořenový certifikát ASDK najdete na každém virtuálním počítači, který nasadíte v ASDK. Například na virtuálním počítači s Ubuntu se v tomto adresáři `/var/lib/waagent/Certificates.pem`nachází. 

Zkopírujte soubor certifikátu pomocí následujícího příkazu:

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení clusteru Kubernetes s modulem AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)