---
title: Nasazení modulu AKS ve Windows v centru Azure Stack
description: Naučte se, jak pomocí počítače s Windows v centru Azure Stack hostovat modul AKS za účelem nasazení a správy clusteru Kubernetes.
author: mattbriggs
ms.topic: article
ms.date: 09/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/16/2020
ms.openlocfilehash: 29255d706edcb052711b2ed42e65fd3dc0b6412b
ms.sourcegitcommit: b960df16e84ec9fbccfce772102b91f0b7ae7060
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93291235"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack-hub"></a>Instalace stroje AKS ve Windows do centra Azure Stack

Počítač s Windows můžete v centru Azure Stack použít k hostování modulu AKS, aby bylo možné nasadit a spravovat cluster Kubernetes. V tomto článku se podíváme na přípravu klientského virtuálního počítače pro správu clusteru pro připojení i odpojení Azure Stackch instancí, Projděte si instalaci a nastavte virtuální počítač klienta na ASDK.

## <a name="prepare-the-client-vm"></a>Příprava virtuálního počítače klienta

Modul AKS je nástroj příkazového řádku, který slouží k nasazení a správě clusteru Kubernetes. Modul můžete spustit na počítači v centru Azure Stack. Z tohoto počítače spustíte modul AKS a nasadíte prostředky IaaS a software potřebný ke spuštění clusteru. Pak můžete pomocí počítače, na kterém je spuštěný modul, provádět úlohy správy v clusteru.

Při volbě klientského počítače zvažte následující:

1. Pokud klientský počítač by měl být v případě havárie obnovitelný.
3. Jak se připojíte ke klientskému počítači a jak bude počítač pracovat s clusterem.

## <a name="install-in-a-connected-environment"></a>Instalace v připojeném prostředí

Můžete nainstalovat klientský virtuální počítač pro správu clusteru Kubernetes na rozbočovači Azure Stack připojeném k Internetu.

1. Vytvořte virtuální počítač s Windows ve svém rozbočovači Azure Stack. Pokyny najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí portálu Azure Stack hub](./azure-stack-quick-windows-portal.md).
2. Připojte se k VIRTUÁLNÍmu počítači.
3. [Nainstalujte čokolády pomocí instrukcí PowerShellu.](https://chocolatey.org/install#install-with-powershellexe) 

    Podle čokolády na webu: čokoláda je správce balíčků pro Windows, jako je apt-get nebo Yumu, ale pro Windows. Byla navržena jako decentralizovaná architektura pro rychlé instalování aplikací a nástrojů, které potřebujete. Je postavená na infrastruktuře NuGet, která aktuálně používá PowerShell jako svůj fokus pro doručování balíčků z distribuce na vaše dvířka, Err, Computer.
4. Vyhledá verzi AKS Engine v tabulce [podporovaných verzí Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions) . V tržišti centra Azure Stack musí být dostupný základní modul AKS. Při spuštění příkazu je nutné zadat verzi `--version v0.48.0` . Pokud nezadáte verzi, příkaz nainstaluje nejnovější verzi, která může potřebovat image VHD, která není na vašem webu Marketplace k dispozici.
5. Z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz a zahrňte číslo verze:

    ```PowerShell  
        choco install aks-engine --version 0.55.4 -y
    ```

> [!NOTE]  
> Pokud se tato metoda instalace nezdařila, můžete vyzkoušet postup v [odpojeném prostředí](#install-in-a-disconnected-environment)nebo [vyzkoušet GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), jiného správce balíčků.

## <a name="install-in-a-disconnected-environment"></a>Instalace v odpojeném prostředí

Můžete nainstalovat klientský virtuální počítač pro správu clusteru Kubernetes na rozbočovači Azure Stack odpojený od Internetu.

1.  Z počítače s přístupem k Internetu přejděte na GitHub [Azure/AKS-Engine](https://github.com/Azure/aks-engine/releases/latest). Stáhněte si archiv (*. tar. gz) pro počítač s Windows, například `aks-engine-v0.38.8-windows-amd64.tar.gz` .

2.  Vytvořte účet úložiště v instanci centra Azure Stack, abyste nahráli archivní soubor (*. tar. gz) s binárním modulem AKS. Pokyny k používání Průzkumník služby Azure Storage najdete v tématu [Průzkumník služby Azure Storage pomocí centra Azure Stack](./azure-stack-storage-connect-se.md).

3. Vytvořte virtuální počítač s Windows ve svém rozbočovači Azure Stack. Pokyny najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí portálu Azure Stack hub.](./azure-stack-quick-windows-portal.md)

4.  Z adresy URL objektu BLOB účtu úložiště centra Azure Stack, kam jste nahráli archivní soubor (*. tar. gz), Stáhněte soubor na virtuální počítač pro správu. Extrahujte archiv do adresáře, ke kterému máte přístup z příkazového řádku.

5. Připojte se k VIRTUÁLNÍmu počítači.

6. [Nainstalujte čokolády pomocí instrukcí PowerShellu.](https://chocolatey.org/install#install-with-powershellexe) 

7.  Z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz. Zahrnout správné číslo verze:

    ```PowerShell  
        choco install aks-engine --version 0.55.4 -y
    ```

## <a name="verify-the-installation"></a>Ověření instalace

Po nastavení klientského virtuálního počítače ověřte, že máte nainstalovaný modul AKS.

1. Připojte se k VIRTUÁLNÍmu počítači klienta.
2. Spusťte následující příkaz:

    ```PowerShell  
    aks-engine version
    ```

Pokud se nemůžete ověřit, jestli máte na VIRTUÁLNÍm počítači klienta nainstalovaný modul AKS, přečtěte si téma [řešení potíží s instalací modulu AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>Instalace ASDK

Pokud spouštíte virtuální počítač klienta pro modul AKS na ASDK na počítači mimo ASDK, budete muset přidat certifikát. Pokud používáte virtuální počítač s Windows v rámci samotného prostředí ASDK, počítač už důvěřuje certifikátu ASDK. Pokud je váš klientský počítač mimo ASDK, je potřeba extrahovat certifikát z ASDK a přidat ho do počítače s Windows.

Pokud používáte ASDK, váš Azure Resource Manager koncový bod používá certifikát podepsaný svým držitelem, musíte tento certifikát explicitně přidat do důvěryhodného úložiště certifikátů počítače. Kořenový certifikát ASDK najdete na každém virtuálním počítači, který nasadíte v ASDK.

1. Exportujte kořenový certifikát certifikační autority. Pokyny najdete v tématu [Export kořenového certifikátu certifikační autority centra Azure Stack](../asdk/asdk-cli.md#export-the-azure-stack-hub-ca-root-certificate).
2. Důvěřovat kořenovému certifikátu certifikační autority centra Azure Stack. Pokyny najdete v tématu [důvěřování kořenovému certifikátu certifikační autority centra Azure Stack](../asdk/asdk-cli.md#trust-the-certificate).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení clusteru Kubernetes s modulem AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
