---
title: Řešení potíží s nasazením Kubernetes pro Azure Stack | Microsoft Docs
description: Naučte se řešit potíže s nasazením Kubernetes pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabrigg
ms.date: 11/14/2019
ms.reviewer: waltero
ms.lastreviewed: 11/14/2019
ms.openlocfilehash: 900ff88136d75759fdc3bc05bf351968f9c13654
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689938"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack"></a>Řešení potíží s nasazením Kubernetes pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

> [!Note]  
> K nasazení clusterů jako zkušebního konceptu použijte jenom položku Kubernetes Azure Stack Marketplace. Pro podporované Kubernetes clustery v Azure Stack použijte [modul AKS](azure-stack-kubernetes-aks-engine-overview.md).

Tento článek popisuje, jak řešit potíže s clusterem Kubernetes. Chcete-li zahájit odstraňování potíží, zkontrolujte prvky požadované pro nasazení. Možná budete muset shromáždit protokoly nasazení z Azure Stack nebo virtuálních počítačů se systémem Linux, které hostují Kubernetes. Chcete-li načíst protokoly z koncového bodu správy, obraťte se na správce Azure Stack.

## <a name="overview-of-kubernetes-deployment"></a>Přehled nasazení Kubernetes

Než začnete řešit potíže s clusterem, Projděte si proces nasazení clusteru Azure Stack Kubernetes. Nasazení používá šablonu řešení Azure Resource Manager k vytvoření virtuálních počítačů a instalaci modulu AKS pro váš cluster.

### <a name="kubernetes-deployment-workflow"></a>Pracovní postup nasazení Kubernetes

Následující diagram ukazuje obecný proces nasazení clusteru.

![Nasadit proces Kubernetes](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Kroky nasazení

1. Shromáždí vstupní parametry z položky Marketplace.

    Zadejte hodnoty, které potřebujete k nastavení clusteru Kubernetes, včetně:
    -  **Uživatelské jméno**: uživatelské jméno pro virtuální počítače (VM) pro Linux, které jsou součástí clusteru KUBERNETES a DVM.
    -  **Veřejný klíč SSH**: klíč, který se používá pro autorizaci všech počítačů se systémem Linux, které byly vytvořeny jako součást clusteru KUBERNETES a DVM.
    -  **Instanční objekt**: ID, které používá poskytovatel cloudu Azure Kubernetes. ID klienta identifikované jako ID aplikace při vytváření instančního objektu. 
    -  **Tajný klíč klienta**: klíč, který jste vytvořili při vytváření instančního objektu.

2. Vytvořte virtuální počítač nasazení a rozšíření vlastních skriptů.
    -  Vytvořte virtuální počítač pro nasazení Linux pomocí webu Marketplace Linux image **Ubuntu Server 16,04-LTS**.
    -  Stáhněte a spusťte rozšíření vlastních skriptů z webu Marketplace. Tento skript je **vlastní skript pro Linux 2,0**.
    -  Spusťte vlastní skript DVM. Skript provádí následující úlohy:
        1. Načte koncový bod galerie z Azure Resource Manager koncového bodu metadat.
        2. Získá ID prostředku služby Active Directory z Azure Resource Manager koncového bodu metadat.
        3. Načte model rozhraní API pro modul AKS.
        4. Nasadí modul AKS do clusteru Kubernetes a uloží profil Azure Stack Cloud do `/etc/kubernetes/azurestackcloud.json`.
3. Vytvořte hlavní virtuální počítače.

4. Stáhněte a spusťte rozšíření vlastních skriptů.

5. Spusťte hlavní skript.

    Skript provádí následující úlohy:
    - Nainstaluje etcd, Docker a Kubernetes prostředky, jako je například kubelet. etcd je úložiště hodnot distribuovaných klíčů, které poskytuje způsob, jak ukládat data napříč clusterem počítačů. Docker podporuje holé virtualizace na úrovni operačního systému označované jako kontejnery. Kubelet je agent uzlu, který běží na jednotlivých uzlech Kubernetes.
    - Nastaví službu **etcd** .
    - Nastaví službu **kubelet** .
    - Spustí kubelet. Tato úloha zahrnuje následující kroky:
        1. Spustí službu API.
        2. Spustí službu kontroleru.
        3. Spustí službu Scheduler Service.
6. Vytvořte virtuální počítače agenta.

7. Stáhněte a spusťte rozšíření vlastních skriptů.

7. Spusťte skript agenta. Vlastní skript agenta provádí následující úlohy:
    - Nainstaluje **etcd**.
    - Nastaví službu **kubelet** .
    - Připojí se ke clusteru Kubernetes.

## <a name="steps-to-troubleshoot-kubernetes"></a>Postup řešení potíží s Kubernetes

Na virtuálních počítačích, které podporují cluster Kubernetes, můžete shromažďovat a kontrolovat protokoly nasazení. Obraťte se na správce Azure Stack a ověřte verzi Azure Stack, kterou potřebujete použít, a získejte protokoly od Azure Stack, které souvisejí s vaším nasazením.

1. Přečtěte si kód chyby vrácený nasazením ARM v podokně **nasazení** ve skupině prostředků, do které jste cluster nasadili. Popisy chybových kódů najdete v článku [věnovaném řešení potíží](https://github.com/msazurestackworkloads/azurestack-gallery/blob/master/kubernetes/docs/troubleshooting.md) v ÚLOŽIŠTI GitHub AKS Engine. Pokud problém s popisem chyby nemůžete vyřešit, pokračujte v tomto postupu.
2. Zkontrolujte [stav nasazení](#review-deployment-status) a načtěte protokoly z hlavního uzlu v clusteru Kubernetes.
3. Ověřte, že používáte nejnovější verzi Azure Stack. Pokud si nejste jistí, kterou verzi používáte, obraťte se na správce Azure Stack.
4. Zkontrolujte soubory pro vytváření virtuálních počítačů. Mohli byste mít následující problémy:  
    - Veřejný klíč může být neplatný. Zkontrolujte klíč, který jste vytvořili.  
    - Při vytváření virtuálního počítače se možná aktivovala vnitřní chyba nebo se aktivovala Chyba při vytváření. Řada faktorů může způsobit chyby, včetně omezení kapacity pro předplatné Azure Stack.
    - Ujistěte se, že plně kvalifikovaný název domény (FQDN) pro virtuální počítač začíná duplicitní předponou.
5.  Pokud je virtuální počítač v **pořádku**, vyhodnoťte DVM. Pokud má DVM chybovou zprávu:
    - Veřejný klíč může být neplatný. Zkontrolujte klíč, který jste vytvořili.  
    - Obraťte se na správce Azure Stack a načtěte protokoly pro Azure Stack pomocí privilegovaných koncových bodů. Další informace najdete v tématu [Nástroje pro diagnostiku Azure Stack](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).
5. Pokud máte dotaz týkající se nasazení, můžete ho publikovat, nebo zjistit, jestli už někdo na dotaz na [Azure Stack Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)odpověděl. 


## <a name="review-deployment-status"></a>Zkontrolovat stav nasazení

Když nasadíte cluster Kubernetes, můžete zkontrolovat stav nasazení a zkontrolovat případné problémy.

1. Otevřete [portál Azure Stack](https://portal.local.azurestack.external).
2. Vyberte **skupiny prostředků**a pak vyberte název skupiny prostředků, kterou jste použili při nasazování clusteru Kubernetes.
3. Vyberte **nasazení**a potom vyberte **název nasazení**.

    ![Řešení potíží s Kubernetes: vyberte nasazení.](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Projděte si okno řešení potíží. Každý nasazený prostředek poskytuje tyto informace:
    
    | Vlastnost | Popis |
    | ----     | ----        |
    | Prostředek | Název prostředku. |
    | Typ | Poskytovatel prostředků a typ prostředku. |
    | Stav | Stav položky |
    | Časové razítko | Časové razítko UTC v čase. |
    | Podrobnosti o operaci | Podrobnosti o operaci, jako je například poskytovatel prostředků, který byl součástí operace, koncový bod prostředku a název prostředku. |

    Každá položka má ikonu stavu zelenou nebo červenou.

## <a name="review-deployment-logs"></a>Kontrola protokolů nasazení

Pokud Azure Stack portál neposkytuje dostatek informací, abyste mohli vyřešit nebo překonat selhání nasazení, je dalším krokem dig do protokolů clusteru. Pokud chcete protokoly nasazení načíst ručně, musíte se obvykle připojit k jednomu z hlavních virtuálních počítačů v clusteru. Jednodušší alternativní přístup by byl stáhnout a spustit následující [skript bash](https://aka.ms/AzsK8sLogCollectorScript) poskytnutý týmem Azure Stack. Tento skript se připojuje k virtuálním počítačům DVM a clusteru, shromažďuje relevantní protokoly systému a clusteru a stáhne je zpátky do pracovní stanice.

### <a name="prerequisites"></a>Předpoklady

K počítači, který použijete ke správě Azure Stack, budete potřebovat výzvu k bash. Na počítači s Windows můžete získat výzvu bash instalací [Gitu pro Windows](https://git-scm.com/downloads). Po instalaci vyhledejte _Git bash_ v nabídce Start.

### <a name="retrieving-the-logs"></a>Načítání protokolů

Pomocí těchto kroků můžete shromáždit a stáhnout protokoly clusteru:

1. Otevřete příkazový řádek bash. Z počítače se systémem Windows otevřete _Git bash_ nebo spusťte: `C:\Program Files\Git\git-bash.exe`.

2. Spuštěním následujících příkazů v příkazovém řádku bash Stáhněte skript kolektoru protokolů:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Vyhledejte informace požadované skriptem a spusťte ho:

    | Parametr           | Popis                                                                                                      | Příklad:                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d,--VMD-Host      | Veřejná IP adresa nebo plně kvalifikovaný název domény (FQDN) pro DVM. Název virtuálního počítače začíná na `vmd-`. | IP ADRESA: 192.168.102.38<br>DNS: VMD-myk8s. Local. cloudapp. azurestack. external |
    | -h,--help  | Použití příkazu tisku. | |
    | -i,--identity-File | Cesta k souboru privátního klíče RSA předanému položce Marketplace při vytváření clusteru Kubernetes. Vyžaduje se pro vzdálené přihlášení k uzlům Kubernetes. | C:\data\ id_rsa. pem (do výstupu)<br>~/.ssh/id_rsa (SSH)
    | -m,--Master-Host   | Veřejná IP adresa nebo plně kvalifikovaný název domény (FQDN) hlavního uzlu Kubernetes Název virtuálního počítače začíná na `k8s-master-`. | IP ADRESA: 192.168.102.37<br>Plně kvalifikovaný název domény: k8s-12345. Local. cloudapp. azurestack. external      |
    | -u,--User          | Uživatelské jméno předané položce Marketplace při vytváření clusteru Kubernetes. Vyžaduje se pro vzdálené přihlášení k uzlům Kubernetes. | azureuser (výchozí hodnota) |


   Když přidáte hodnoty parametrů, váš příkaz může vypadat podobně jako v tomto příkladu:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Po několika minutách skript vytvoří výstup shromážděných protokolů do adresáře s názvem `KubernetesLogs_{{time-stamp}}`. Pro každý virtuální počítač, který patří do clusteru, najdete adresář.

    Skript kolektoru protokolů bude také vyhledávat chyby v souborech protokolu a zahrnovat kroky pro řešení potíží, pokud najde známý problém. Ujistěte se, že používáte nejnovější verzi skriptu, abyste zvýšili pravděpodobnost hledání známých problémů.

> [!Note]  
> Další informace o skriptu kolektoru protokolů najdete v tomto [úložišti](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) GitHub.

## <a name="next-steps"></a>Další kroky

[Nasazení Kubernetes do Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Přidání clusteru Kubernetes do Marketplace (pro operátor Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
