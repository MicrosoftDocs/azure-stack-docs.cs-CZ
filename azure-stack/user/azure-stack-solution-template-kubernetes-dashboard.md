---
title: Přístup k řídicímu panelu Kubernetes v Azure Stack | Microsoft Docs
description: Přečtěte si, jak získat přístup k řídicímu panelu Kubernetes v Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: fdda72e215590c7bbd7d739e2eb46b085fc55405
ms.sourcegitcommit: 0d27456332031ab98ba2277117395ae5ffcbb79f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73047195"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Přístup k řídicímu panelu Kubernetes v Azure Stack 

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit* 
> [!Note]   
> K nasazení clusterů jako zkušebního konceptu použijte jenom položku Kubernetes Azure Stack Marketplace. Pro podporované Kubernetes clustery v Azure Stack použijte [modul AKS](azure-stack-kubernetes-aks-engine-overview.md).

Kubernetes zahrnuje webový řídicí panel, který můžete použít pro základní operace správy. Tento řídicí panel umožňuje zobrazit základní stav a metriky pro vaše aplikace, vytvářet a nasazovat služby a upravovat stávající aplikace. V tomto článku se dozvíte, jak nastavit řídicí panel Kubernetes na Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Předpoklady pro řídicí panel Kubernetes

* Cluster Kubernetes Azure Stack

    Pro Azure Stack budete muset nasadit cluster Kubernetes. Další informace najdete v tématu [nasazení Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Klient SSH

    K zabezpečení připojení k hlavnímu uzlu v clusteru budete potřebovat klienta SSH. Pokud používáte systém Windows, můžete použít výstup do [výstupu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Budete potřebovat privátní klíč, který se použije při nasazení clusteru Kubernetes.

* FTP (PSCP)

    Je také možné, že budete potřebovat klienta FTP, který podporuje SSH a SSH protokol FTP (File Transfer Protocol) k přenosu certifikátů z hlavního uzlu do počítače se správou Azure Stack. Můžete použít [FileZilly](https://filezilla-project.org/download.php?type=client). Budete potřebovat privátní klíč, který se použije při nasazení clusteru Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Přehled kroků pro povolení řídicího panelu

1.  Exportujte certifikáty Kubernetes z hlavního uzlu v clusteru. 
2.  Importujte certifikáty do počítače pro správu Azure Stack.
2.  Otevřete webový řídicí panel Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Exportovat certifikát z hlavní větve 

Adresu URL řídicího panelu můžete načíst z hlavního uzlu v clusteru.

1. Získejte veřejnou IP adresu a uživatelské jméno pro hlavní server vašeho clusteru z řídicího panelu Azure Stack. Získat tyto informace:

    - Přihlášení k [portálu Azure Stack](https://portal.local.azurestack.external/)
    - Vyberte **všechny služby** > **všechny prostředky**. Ve vaší skupině prostředků clusteru Najděte hlavní server. Hlavní název je pojmenován `k8s-master-<sequence-of-numbers>`. 

2. Otevřete hlavní uzel na portálu. Zkopírujte **veřejnou IP** adresu. Kliknutím na **připojit** získáte své uživatelské jméno v poli **přihlášení pomocí místního účtu virtuálního počítače** . Toto je stejné uživatelské jméno, které jste nastavili při vytváření clusteru. Místo soukromé IP adresy uvedené v okně připojit použijte veřejnou IP adresu.

3.  Otevřete klienta SSH pro připojení k hlavnímu serveru. Pokud pracujete v systému Windows, můžete vytvořit připojení pomocí [výstupu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) . Použijete veřejnou IP adresu pro hlavní uzel, uživatelské jméno a přidáte privátní klíč, který jste použili při vytváření clusteru.

4.  Po připojení terminálu zadejte `kubectl` a otevřete tak klienta příkazového řádku Kubernetes.

5. Spusťte následující příkaz:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Vyhledejte adresu URL řídicího panelu. Například: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extrahujte certifikát podepsaný svým držitelem a převeďte ho do formátu PFX. Spusťte následující příkaz:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Získejte seznam tajných kódů v oboru názvů **Kube-System** . Spusťte následující příkaz:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Poznamenejte si hodnotu > Kubernetes-Dashboard-token-\<XXXXX. 

8.  Získejte token a uložte ho. Aktualizujte `kubernetes-dashboard-token-<####>` s použitím tajné hodnoty z předchozího kroku.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importovat certifikát

1. Otevřete FileZilly a připojte se k hlavnímu uzlu. Budete potřebovat:

    - Veřejná IP adresa hlavního uzlu
    - uživatelské jméno
    - privátní tajný klíč
    - Použití protokolu **SFTP-SSH Protokol FTP (File Transfer Protocol)**

2. Zkopírujte `/etc/kubernetes/certs/client.pfx` a `/etc/kubernetes/certs/ca.crt` do počítače pro správu Azure Stack.

3. Poznamenejte si umístění souborů. Aktualizujte skript pomocí umístění a pak otevřete PowerShell s výzvou se zvýšenými oprávněními. Spusťte aktualizovaný skript:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Otevření řídicího panelu Kubernetes 

1. Zakažte blokování automaticky otevíraných oken ve webovém prohlížeči.

2. Najeďte na adresu URL v prohlížeči, který jste si poznamenali při spuštění příkazu `kubectl cluster-info`. Například: https: \//azurestackdomainnamefork8sdashboard/API/v1/obory názvů/Kube-System/Services/https: Kubernetes-Dashboard:/proxy 
3. Vyberte certifikát klienta.
4. Zadejte token. 
5. Znovu se připojte k příkazovému řádku bash na hlavním uzlu a udělte oprávnění `kubernetes-dashboard`. Spusťte následující příkaz:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Skript poskytuje `kubernetes-dashboard` oprávnění správce cloudu. Další informace najdete v tématu [pro clustery s podporou RBAC](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Řídicí panel můžete použít. Další informace o řídicím panelu Kubernetes najdete v tématu [řídicí panel webového uživatelského rozhraní Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) . 

![Řídicí panel Azure Stack Kubernetes](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Další kroky 

[Nasazení Kubernetes do Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Přidání clusteru Kubernetes do Marketplace (pro operátor Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes v Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
