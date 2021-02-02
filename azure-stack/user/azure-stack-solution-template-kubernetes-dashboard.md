---
title: Přístup k řídicímu panelu Kubernetes v centru Azure Stack
description: Přečtěte si, jak získat přístup k řídicímu panelu Kubernetes v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 5787fb0df74d0abbf2c05c76d0c1cd3fd06eb04b
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247723"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack-hub"></a>Přístup k řídicímu panelu Kubernetes v centru Azure Stack 

> [!NOTE]   
> K nasazení clusterů jako zkušebního konceptu použijte jenom položku Kubernetes Azure Stack Marketplace. Pro podporované Kubernetes clustery v Azure Stack použijte [modul AKS](azure-stack-kubernetes-aks-engine-overview.md).

Kubernetes zahrnuje webový řídicí panel, který můžete použít pro základní operace správy. Tento řídicí panel umožňuje zobrazit základní stav a metriky pro vaše aplikace, vytvářet a nasazovat služby a upravovat stávající aplikace. V tomto článku se dozvíte, jak nastavit řídicí panel Kubernetes na rozbočovači Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Předpoklady pro řídicí panel Kubernetes

* Cluster Kubernetes hub pro Azure Stack

    Budete muset nasadit cluster Kubernetes do centra Azure Stack. Další informace najdete v tématu [nasazení Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Klient SSH

    K zabezpečení připojení k hlavnímu uzlu v clusteru budete potřebovat klienta SSH. Pokud používáte systém Windows, můžete použít výstup do [výstupu](https://www.ssh.com/ssh/putty/download). Budete potřebovat privátní klíč, který se použije při nasazení clusteru Kubernetes.

* FTP (PSCP)

    Je také možné, že budete potřebovat klienta FTP, který podporuje SSH a SSH protokol FTP (File Transfer Protocol) k přenosu certifikátů z hlavního uzlu do počítače správy Azure Stack hub. Můžete použít [FileZilly](https://filezilla-project.org/download.php?type=client). Budete potřebovat privátní klíč, který se použije při nasazení clusteru Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Přehled kroků pro povolení řídicího panelu

1.  Exportujte certifikáty Kubernetes z hlavního uzlu v clusteru. 
2.  Importujte certifikáty do počítače správy centra Azure Stack.
2.  Otevřete webový řídicí panel Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Exportovat certifikát z hlavní větve 

Adresu URL řídicího panelu můžete načíst z hlavního uzlu v clusteru.

1. Z řídicího panelu centra Azure Stack Získejte veřejnou IP adresu a uživatelské jméno pro hlavní server vašeho clusteru. Získat tyto informace:

    - Přihlaste se k portálu centra Azure Stack `https://portal.local.azurestack.external/` .
    - Vyberte **všechny služby**  >  **všechny prostředky**. Ve vaší skupině prostředků clusteru Najděte hlavní server. Hlavní název je pojmenován `k8s-master-<sequence-of-numbers>` . 

2. Otevřete hlavní uzel na portálu. Zkopírujte **veřejnou IP** adresu. Kliknutím na **připojit** získáte své uživatelské jméno v poli  **přihlášení pomocí místního účtu virtuálního počítače** . Toto je stejné uživatelské jméno, které jste nastavili při vytváření clusteru. Místo soukromé IP adresy uvedené v okně připojit použijte veřejnou IP adresu.

3.  Otevřete klienta SSH pro připojení k hlavnímu serveru. Pokud pracujete v systému Windows, můžete vytvořit připojení pomocí [výstupu](https://www.ssh.com/ssh/putty/download) . Použijete veřejnou IP adresu pro hlavní uzel, uživatelské jméno a přidáte privátní klíč, který jste použili při vytváření clusteru.

4.  Po připojení terminálu zadejte `kubectl` a otevřete klienta příkazového řádku Kubernetes.

5. Spusťte následující příkaz:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Vyhledejte adresu URL řídicího panelu. Například:  `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Extrahujte certifikát podepsaný svým držitelem a převeďte ho do formátu PFX. Spusťte následující příkaz:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Získejte seznam tajných kódů v oboru názvů **Kube-System** . Spusťte následující příkaz:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Poznamenejte si Kubernetes-Dashboard-token- \<XXXXX> Value. 

8.  Získejte token a uložte ho. Aktualizujte `kubernetes-dashboard-token-<####>` hodnotu s tajnou hodnotou z předchozího kroku.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Importovat certifikát

1. Otevřete FileZilly a připojte se k hlavnímu uzlu. Budete potřebovat:

    - Veřejná IP adresa hlavního uzlu
    - uživatelské jméno
    - privátní tajný klíč
    - Použití protokolu **SFTP-SSH Protokol FTP (File Transfer Protocol)**

2. Zkopírujte `/etc/kubernetes/certs/client.pfx` a  `/etc/kubernetes/certs/ca.crt` do počítače správy centra Azure Stack.

3. Poznamenejte si umístění souborů. Aktualizujte skript pomocí umístění a pak otevřete PowerShell s výzvou se zvýšenými oprávněními. Spusťte aktualizovaný skript:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Otevření řídicího panelu Kubernetes 

1. Zakažte blokování automaticky otevíraných oken ve webovém prohlížeči.

2. Najeďte v prohlížeči na adresu URL, kterou jste si poznamenali při spuštění příkazu `kubectl cluster-info` . Například: https: \/ /azurestackdomainnamefork8sdashboard/API/v1/Namespaces/Kube-System/Services/https: Kubernetes-Dashboard:/proxy 
3. Vyberte certifikát klienta.
4. Zadejte token. 
5. Znovu se připojte k příkazovému řádku bash na hlavním uzlu a udělte oprávnění `kubernetes-dashboard` . Spusťte následující příkaz:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Skript poskytuje `kubernetes-dashboard` oprávnění správce cloudu. Další informace najdete v tématu [pro clustery s podporou RBAC](/azure/aks/kubernetes-dashboard).

Řídicí panel můžete použít. Další informace o řídicím panelu Kubernetes najdete v tématu [řídicí panel webového uživatelského rozhraní Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) . 

![Řídicí panel Kubernetes centra Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="troubleshooting"></a>Řešení potíží

### <a name="custom-virtual-networks"></a>Vlastní virtuální sítě

Pokud čelíte problémům s připojením k řídicímu panelu Kubernetes po nasazení Kubernetes do [vlastní virtuální sítě](./kubernetes-aks-engine-custom-vnet.md), zajistěte, aby se cílové podsítě propojili s tabulkami směrování a skupinami zabezpečení sítě, které vytvořil modul AKS.

Ujistěte se, že pravidla skupiny zabezpečení sítě umožňují komunikaci mezi hlavními uzly a řídicím panelem Kubernetes pod IP adresou. To lze ověřit pomocí příkazu příkazu pro odeslání z hlavního uzlu.

## <a name="next-steps"></a>Další kroky 

[Nasazení Kubernetes do centra Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Přidání clusteru Kubernetes do Marketplace (pro operátor centra Azure Stack)](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes v Azure](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
