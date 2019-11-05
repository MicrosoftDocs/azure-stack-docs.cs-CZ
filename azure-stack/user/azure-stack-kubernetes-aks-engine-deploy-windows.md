---
title: Nasazení modulu AKS ve Windows v Azure Stack | Microsoft Docs
description: Naučte se, jak pomocí počítače s Windows v Azure Stack hostovat modul AKS, aby bylo možné nasadit a spravovat cluster Kubernetes.
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
ms.date: 10/30/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/30/2019
ms.openlocfilehash: b9dd45b6ea7fdfaab1b1e7418800dcd2031dc151
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595175"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack"></a>Nainstalujte modul AKS ve Windows do Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Počítač s Windows můžete ve svém Azure Stack použít k hostování modulu AKS, aby bylo možné nasadit a spravovat cluster Kubernetes. V tomto článku se podíváme na přípravu klientského virtuálního počítače pro správu clusteru pro připojené i odpojené Azure Stack instance, Projděte si instalaci a nastavte virtuální počítač klienta na ASDK.

## <a name="prepare-the-client-vm"></a>Příprava virtuálního počítače klienta

Modul AKS je nástroj příkazového řádku, který slouží k nasazení a správě clusteru Kubernetes. Modul můžete spustit na počítači v Azure Stack. Z tohoto počítače spustíte modul AKS a nasadíte prostředky IaaS a software potřebný ke spuštění clusteru. Pak můžete pomocí počítače, na kterém je spuštěný modul, provádět úlohy správy v clusteru.

Při volbě klientského počítače zvažte následující:

1. Pokud klientský počítač by měl být v případě havárie obnovitelný.
3. Jak se připojíte ke klientskému počítači a jak bude počítač pracovat s clusterem.

## <a name="install-in-a-connected-environment"></a>Instalace v připojeném prostředí

Můžete nainstalovat klientský virtuální počítač pro správu clusteru Kubernetes na Azure Stack připojeném k Internetu.

1. Vytvořte ve svém Azure Stack virtuální počítač s Windows. Pokyny najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí Azure Stackového portálu](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).
2. Připojte se k VIRTUÁLNÍmu počítači.
3. [Nainstalujte čokolády pomocí instrukcí PowerShellu.](https://chocolatey.org/install#install-with-powershellexe) 

    Podle webu Chocolaty: čokoláda je správce balíčků pro Windows, jako je apt-get nebo Yumu, ale pro Windows. Byla navržena jako decentralizovaná architektura pro rychlé instalování aplikací a nástrojů, které potřebujete. Je postavená na infrastruktuře NuGet, která aktuálně používá PowerShell jako svůj fokus pro doručování balíčků z distribuce na vaše dvířka, Err, Computer.
4. Vyhledá verzi AKS Engine v tabulce [podporovaných verzí Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) . Základní modul AKS musí být k dispozici na webu Marketplace pro Azure Stack. Při spuštění příkazu je nutné zadat verzi `--version v0.43.0`. Pokud nezadáte verzi, příkaz nainstaluje nejnovější verzi, která může potřebovat image VHD, která není na vašem webu Marketplace k dispozici.
5. Z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz a zahrňte číslo verze:

    ```PowerShell  
        choco install aks-engine --version 0.43.0 -y
    ```

> [!Note]  
> Pokud se tato metoda instalace nezdařila, můžete vyzkoušet postup v [odpojeném prostředí](#install-in-a-disconnected-environment)nebo [vyzkoušet GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), jiného správce balíčků.

## <a name="install-in-a-disconnected-environment"></a>Instalace v odpojeném prostředí

Můžete nainstalovat klientský virtuální počítač pro správu clusteru Kubernetes na Azure Stack odpojený od Internetu.

1.  Z počítače s přístupem k Internetu přejděte na GitHub [Azure/AKS-Engine](https://github.com/Azure/aks-engine/releases/latest). Stáhněte si archiv (*. tar. gz) pro počítač s Windows, například `aks-engine-v0.38.8-windows-amd64.tar.gz`.

2.  Vytvořte v instanci Azure Stack účet úložiště pro nahrání souboru archivu (*. tar. gz) s binárním modulem AKS. Pokyny k používání Průzkumník služby Azure Storage najdete v tématu [Průzkumník služby Azure Storage s Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Vytvořte ve svém Azure Stack virtuální počítač s Windows. Pokyny najdete v tématu [rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí Azure Stackového portálu.](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal)

4.  Z adresy URL objektu blob Azure Stack účtu úložiště, kam jste nahráli archivní soubor (*. tar. gz), Stáhněte soubor na virtuální počítač pro správu. Extrahujte archiv do adresáře, ke kterému máte přístup z příkazového řádku.

5. Připojte se k VIRTUÁLNÍmu počítači.

6. [Nainstalujte čokolády pomocí instrukcí PowerShellu.](https://chocolatey.org/install#install-with-powershellexe) 

7.  Z příkazového řádku se zvýšenými oprávněními spusťte následující příkaz. Zahrnout správné číslo verze:

    ```PowerShell  
        choco install aks-engine --version 0.43.0 -y
    ```

## <a name="verify-the-installation"></a>Ověření instalace

Po nastavení klientského virtuálního počítače ověřte, že máte nainstalovaný modul AKS.

1. Připojte se k VIRTUÁLNÍmu počítači klienta.
2. Spusťte následující příkaz:

    ```PowerShell  
    aks-engine version
    ```

Pokud se nemůžete ověřit, jestli máte na VIRTUÁLNÍm počítači klienta nainstalovaný modul AKS, přečtěte si téma [řešení potíží s instalací modulu AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md) .


## <a name="asdk-installation"></a>Instalace ASDK

Pokud spouštíte virtuální počítač klienta pro modul AKS na ASDK na počítači mimo ASDK, budete muset přidat certifikát. Pokud používáte virtuální počítač s Windows v rámci samotného prostředí ASDK, počítač už důvěřuje certifikátu ASDK. Pokud je váš klientský počítač mimo ASDK, je potřeba extrahovat certifikát z ASDK a přidat ho do počítače s Windows.

Pokud používáte ASDK, váš Azure Resource Manager koncový bod používá certifikát podepsaný svým držitelem, musíte tento certifikát explicitně přidat do důvěryhodného úložiště certifikátů počítače. Kořenový certifikát ASDK najdete na každém virtuálním počítači, který nasadíte v ASDK.

1. Exportujte kořenový certifikát certifikační autority. Pokyny najdete v tématu [Export kořenového certifikátu certifikační autority Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#export-the-azure-stack-ca-root-certificate) .
2. Důvěřovat kořenovému certifikátu certifikační autority Azure Stack. Pokyny najdete v tématu [důvěřování kořenovému certifikátu certifikační autority Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#trust-the-azure-stack-ca-root-certificate).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení clusteru Kubernetes s modulem AKS na Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
