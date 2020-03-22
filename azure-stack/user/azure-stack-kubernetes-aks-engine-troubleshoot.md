---
title: Řešení potíží s modulem AKS v centru Azure Stack
description: Tento článek obsahuje postup řešení potíží pro modul AKS v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: d362eb0bf418cf292953afc50fe993dc4867253c
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2020
ms.locfileid: "80069300"
---
# <a name="troubleshoot-the-aks-engine-on-azure-stack-hub"></a>Řešení potíží s modulem AKS v centru Azure Stack

Může dojít k potížím při nasazení nebo práci s modulem AKS v centru Azure Stack. V tomto článku se dozvíte, jak řešit potíže s nasazením modulu AKS, shromažďovat informace o vašem modulu AKS, shromažďovat protokoly Kubernetes, prohlížet kódy chyb v rozšíření vlastních skriptů a pokyny k otevření problému GitHubu pro modul AKS.

## <a name="troubleshoot-the-aks-engine-install"></a>Řešení potíží s instalací modulu AKS

### <a name="try-gofish"></a>Vyzkoušejte GoFish

Pokud předchozí kroky instalace selhaly, můžete modul AKS nainstalovat pomocí Správce balíčků GoFish. [GoFish](https://gofi.sh) popisuje sebe sama jako homebrew pro různé platformy.

#### <a name="install-the-aks-engine-with-gofish-on-linux"></a>Instalace modulu AKS pomocí GoFish v systému Linux

Nainstalujte GoFish ze stránky [instalace](https://gofi.sh/#install) .

1. Na příkazovém řádku bash spusťte následující příkaz:

    ```bash
    curl -fsSL https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.sh | bash
    ```

2.  Spuštěním následujícího příkazu nainstalujte modul AKS s GoFish:

    ```bash
    Run "gofish install aks-engine"
    ```

#### <a name="install-the-aks-engine-with-gofish-on-windows"></a>Instalace modulu AKS pomocí GoFish ve Windows

Nainstalujte GoFish ze stránky [instalace](https://gofi.sh/#install) .

1. Z příkazového řádku PowerShellu se zvýšenými oprávněními spusťte následující příkaz:

    ```PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force
    iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.ps1'))
    ```

2.  Spuštěním následujícího příkazu ve stejné relaci nainstalujte modul AKS s GoFish:

    ```PowerShell
    gofish install aks-engine
    ```

### <a name="checklist-for-common-deployment-issues"></a>Kontrolní seznam pro běžné problémy s nasazením

Když při nasazování clusteru Kubernetes pomocí modulu AKS narazíte na chyby, můžete ověřit:

1.  Používáte správné přihlašovací údaje instančního objektu (SPN)?
2.  Má hlavní název služby (SPN) roli přispěvatelé k předplatnému centra Azure Stack?
3. Máte v plánu centra Azure Stack dostatečně velkou kvótu?
4.  Je instance centra Azure Stack s použitím opravy nebo upgradu?

Další informace najdete v článku [věnovaném řešení potíží](https://github.com/Azure/aks-engine/blob/master/docs/howto/troubleshooting.md) v úložišti GitHub **Azure/AKS-Engine** .

## <a name="collect-aks-engine-logs"></a>Shromažďovat protokoly modulu AKS

Můžete získat přístup k informacím o kontrole, které vytvořil modul AKS. Modul AKS hlásí stav a chyby při spuštění aplikace. Výstup můžete buď předat do textového souboru, nebo ho zkopírovat přímo z konzoly příkazového řádku. Přečtěte si seznam kódů chyb aktivovaných modulem AKS na stránce [Kontrola kódů chyb rozšíření vlastních skriptů](#review-custom-script-extension-error-codes).

1.  Shromáždí standardní výstup a chybu z informací zobrazených v nástroji příkazového řádku AKS Engine.

2. Získá protokoly z místního souboru. Můžete nastavit výstupní adresář s parametrem **--Output-Directory** .

    Nastavení místní cesty pro protokoly:

    ```bash  
    aks-engine --output-directory <path to the directory>
    ```

## <a name="collect-kubernetes-logs"></a>Shromažďovat protokoly Kubernetes

Kromě protokolů modulu AKS generují komponenty Kubernetes stav a chybové zprávy. Tyto protokoly můžete shromažďovat pomocí skriptu bash [getkuberneteslogs.sh](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/diagnosis-v0.1.3).

Tento skript automatizuje proces shromažďování následujících protokolů: 

 - Protokoly waagent (agent pro Linux) Microsoft Azure
 - Protokoly rozšíření vlastních skriptů
 - Spuštění Kube-System metadata kontejneru
 - Spuštění Kube-System Container logs
 - Stav a deník služby Kubelet
 - Stav a deník služby Etcd
 - Protokoly DVM položky Galerie
 - Kube – snímek systému

Bez tohoto skriptu byste se museli připojit k jednotlivým uzlům v clusteru, vyhledat a stáhnout protokoly ručně. Skript navíc může volitelně nahrát shromážděné protokoly do účtu úložiště, který můžete použít ke sdílení protokolů s ostatními.

Požadavky:

 - Virtuální počítač Linux, Git bash nebo bash ve Windows.
 - Rozhraní příkazového [řádku Azure](azure-stack-version-profiles-azurecli2.md) je nainstalované v počítači, ze kterého se skript spustí.
 - Identita instančního objektu se přihlásila k relaci Azure CLI a Azure Stack hub. Vzhledem k tomu, že skript má schopnost zjistit a vytvořit prostředky ARM, aby fungoval, vyžaduje Azure CLI a identitu instančního objektu.
 - Uživatelský účet (předplatné), kde je v prostředí již vybraný cluster Kubernetes. 
1. Stáhněte si nejnovější verzi souboru tar Script do svého klientského virtuálního počítače, počítače, který má přístup ke clusteru Kubernetes nebo ke stejnému počítači, který jste použili k nasazení clusteru s modulem AKS.

    Spusťte následující příkazy:

    ```bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    wget https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.1/diagnosis-v0.1.1.tar.gz
    tar xvf diagnosis-v0.1.1.tar.gz -C ./
    ```

2. Vyhledejte parametry vyžadované skriptem `getkuberneteslogs.sh`. Skript bude používat následující parametry:

    | Parametr | Popis | Požadováno | Příklad |
    | --- | --- | --- | --- |
    | -h,--help | Použití příkazu tisku. | ne | 
    -u,--uživatel | Uživatelské jméno správce pro virtuální počítače clusteru | ano | azureuser<br>(výchozí hodnota) |
    | -i,--identity-File | Privátní klíč RSA vázaný k veřejnému klíči, který se používá k vytvoření clusteru Kubernetes (někdy s názvem "id_rsa")  | ano | `./rsa.pem` (výstupu)<br>`~/.ssh/id_rsa` (SSH) |
    |   -g,--Resource-Group    | Skupina prostředků clusteru Kubernetes | ano | k8sresourcegroup |
    |   -n,--User-Namespace               | Shromažďovat protokoly z kontejnerů v zadaných oborech názvů (Kube – systémové protokoly se vždycky shromažďují) | ne |   sledování |
    |       --API-model                    | Uchovává soubor apimodel. JSON v účtu úložiště centra Azure Stack. Nahrání souboru apimodel. JSON do účtu úložiště se stane, když je k dispozici i parametr--upload-Logs. | ne | `./apimodel.json` |
    | --všechny – obory názvů               | Shromažďování protokolů z kontejnerů ve všech oborech názvů. Přepisuje nastavení--User-Namespace. | ne | |
    | --upload-logs                  | Uchovává načtené protokoly v účtu úložiště Azure Stack hub. Protokoly najdete ve skupině prostředků KubernetesLogs. | ne | |
    --Disable-host-key-kontrola    | Nastaví možnost StrictHostKeyChecking SSH na ne, zatímco se skript spustí. Používejte pouze v bezpečném prostředí. | ne | |

3. Spusťte libovolný z následujících ukázkových příkazů s vašimi informacemi:

    ```bash
    ./getkuberneteslogs.sh -u azureuser -i private.key.1.pem -g k8s-rg
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --disable-host-key-checking
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg -n default -n monitoring
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs --api-model clusterDefinition.json
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs
    ```

## <a name="review-custom-script-extension-error-codes"></a>Kontrola kódů chyb rozšíření vlastních skriptů

Můžete se obrátit na seznam kódů chyb vytvořených pomocí rozšíření vlastních skriptů (CSE) ve spuštěném clusteru. Chyba rozšíření může být užitečná při diagnostikování hlavní příčiny problému. Rozšíření Ubuntu serveru používaného v clusteru Kubernetes podporuje mnoho operací modulu AKS. Další informace o ukončovacích kódech rozšíření naleznete v tématu [cse_helpers. sh](https://github.com/Azure/aks-engine/blob/master/parts/k8s/cloud-init/artifacts/cse_helpers.sh).

### <a name="providing-kubernetes-logs-to-a-microsoft-support-engineer"></a>Poskytování protokolů Kubernetes technickému pracovníkovi podpory Microsoftu

Pokud po shromáždění a prozkoumání protokolů stále nemůžete problém vyřešit, můžete chtít zahájit proces vytváření lístku podpory a poskytnout protokoly, které jste shromáždili spuštěním `getkuberneteslogs.sh` s `--upload-logs` sadou parametrů. 

Obraťte se na svého operátora centra Azure Stack. Váš operátor používá informace od vašich protokolů k vytvoření případu podpory.

Během procesu řešení potíží s podporou může pracovník podpory Microsoftu požádat, aby váš operátor centra Azure Stack shromažďovat protokoly systému Azure Stack hub. K informacím o účtu úložiště, do kterého jste nahráli protokoly Kubernetes spuštěním `getkuberneteslogs.sh`, budete možná muset zadat operátora.

Operátor může spustit PowerShell rutiny **Get-AzureStackLog** . Tento příkaz používá parametr (`-InputSaSUri`), který určuje účet úložiště, kam jste uložili protokoly Kubernetes.

Operátor může kombinovat vytvořené protokoly spolu s jinými systémovými protokoly, které může podporovat společnost Microsoft, a zpřístupnit je společnosti Microsoft.

## <a name="open-github-issues"></a>Otevřít problémy GitHubu

Pokud nemůžete vyřešit chybu nasazení, můžete otevřít problém GitHubu. 

1. V úložišti modulu AKS otevřete [problém GitHubu](https://github.com/Azure/aks-engine/issues/new) .
2. Přidejte název pomocí následujícího formátu: C`SE error: exit code <INSERT_YOUR_EXIT_CODE>`.
3. Do tohoto problému zahrňte tyto informace:

    - Konfigurační soubor clusteru, `apimodel json`používaný k nasazení clusteru. Před odesláním na GitHubu odeberte všechny tajné klíče a klíče.  
     - Výstup následujícího příkazu **kubectl** `get nodes`.  
     - Obsah `/var/log/azure/cluster-provision.log` a `/var/log/cloud-init-output.log`

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .
