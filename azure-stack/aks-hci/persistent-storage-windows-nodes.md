---
title: Použití trvalého úložiště v kontejneru Windows
description: Použití trvalého úložiště v kontejneru Windows a příprava uzlů Windows pro skupinové účty spravované služby
author: abhilashaagarwala
ms.topic: how-to
ms.date: 09/21/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 19b934e4bdec9e0ab6f4e7808dfea6e6fb648245
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94784848"
---
# <a name="use-persistent-storage-in-a-windows-container-and-prepare-windows-nodes-for-group-managed-service-accounts"></a>Použití trvalého úložiště v kontejneru Windows a příprava uzlů Windows pro skupinové účty spravované služby

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s Kubernetes lusky. Trvalý svazek lze použít v jednom nebo více luskech a je určen pro dlouhodobé uložení. Je také nezávislá na životním cyklu pod nebo Node.  V této části se dozvíte, jak vytvořit trvalý svazek a jak používat tento svazek v aplikaci pro Windows.

## <a name="before-you-begin"></a>Než začnete

Tady je přehled toho, co potřebujete, abyste mohli začít:

* Cluster Kubernetes s alespoň jedním pracovním uzlem systému Windows.
* Soubor kubeconfig pro přístup ke clusteru Kubernetes.


## <a name="create-a-persistent-volume-claim"></a>Vytvoření deklarace identity trvalého svazku

Deklarace identity trvalého svazku se používá k automatickému zřízení úložiště na základě třídy úložiště. Chcete-li vytvořit deklaraci identity svazku, nejprve vytvořte soubor s názvem `pvc-akshci-csi.yaml` a zkopírujte následující definici YAML. Deklarace identity vyžaduje disk o velikosti 10 GB s přístupem *ReadWriteOnce*   .  *Výchozí*   třída úložiště je zadána jako třída úložiště (VHDX).  

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
 name: pvc-akshci-csi
spec:
 accessModes:
 - ReadWriteOnce
 resources:
  requests:
   storage: 10Gi
```
Vytvořte svazek spuštěním následujících příkazů v relaci PowerShellu pro správu na jednom ze serverů v clusteru Azure Stack HCI (pomocí metody, jako je například [Enter-PSSession](/powershell/module/microsoft.powershell.core/enter-pssession) nebo Vzdálená plocha pro připojení k serveru): 


```PowerShell
kubectl create -f pvc-akshci-csi.yaml 
```
Následující výstup zobrazí, že se úspěšně vytvořila deklarace identity trvalého svazku:

**Výkonem**
```PowerShell
persistentvolumeclaim/pvc-akshci-csi created
```

## <a name="use-persistent-volume"></a>Použít trvalý svazek

Chcete-li použít trvalý svazek, vytvořte soubor s názvem winwebserver. yaml a zkopírujte následující definici YAML. Pak vytvoříte pod tím, že budete mít přístup k deklaraci identity trvalého svazku a VHDX. 

V níže uvedené definici YAML je *mountPath* cesta k připojení svazku uvnitř kontejneru. Po úspěšném vytvoření pod se zobrazí podadresář *mnt* vytvořený v *C \\ :* a podadresář *akshciscsi* vytvořený v *mnt*.


```yaml
apiVersion: apps/v1 
kind: Deployment 
metadata: 
  labels: 
    app: win-webserver 
  name: win-webserver 
spec: 
  replicas: 1 
  selector: 
    matchLabels: 
      app: win-webserver 
  template: 
    metadata: 
      labels: 
        app: win-webserver 
      name: win-webserver 
    spec: 
     containers: 
      - name: windowswebserver 
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019 
        ports:  
          - containerPort: 80    
        volumeMounts: 
            - name: akshciscsi 
              mountPath: "/mnt/akshciscsi" 
     volumes: 
        - name: akshciscsi 
          persistentVolumeClaim: 
            claimName:  pvc-akshci-csi 
     nodeSelector: 
      kubernetes.io/os: windows 
```

Pokud chcete vytvořit pod výše uvedenou definicí YAML, spusťte:

```PowerShell
Kubectl create -f winwebserver.yaml 
```

Chcete-li se ujistit, že je pod ním spuštěný, spusťte následující příkaz. Počkejte několik minut, než je v běžícím stavu, protože načítání image trvá déle.

```PowerShell
kubectl get pods -o wide 
```
Jakmile je spuštěno, zobrazte stav pod spuštěním následujícího příkazu: 

```PowerShell
kubectl.exe describe pod %podName% 
```

Chcete-li ověřit, zda byl svazek připojen v části pod, spusťte následující příkaz:

```PowerShell
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Odstranění deklarace identity trvalého svazku

Před odstraněním deklarace identity trvalého svazku je nutné nasazení aplikace odstranit spuštěním:

```PowerShell
kubectl.exe delete deployments win-webserver
```

Pak můžete odstranit vynucenou deklaraci identity trvalého svazku spuštěním:

```PowerShell
kubectl.exe delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="prepare-windows-nodes-for-group-managed-service-account-support-on-windows-nodes"></a>Příprava uzlů Windows pro podporu účtu spravované služby na uzlech Windows

Skupinový účet spravované služby je konkrétní typ účtu služby Active Directory, který poskytuje automatickou správu hesel, zjednodušenou správu hlavního názvu služby (SPN) a schopnost delegovat správu na jiné správce na více serverech. Ke konfiguraci skupinových účtů spravované služby (gMSA) pro lusky a kontejnery, které se spustí na uzlech Windows, musíte nejdřív připojit uzly Windows k doméně služby Active Directory.

Pokud chcete povolit podporu účtu spravované služby skupiny, musí mít váš název clusteru Kubernetes méně než 4 znaky. Důvodem je, že maximální podporovaná délka názvu serveru připojeného k doméně je 15 znaků a AKS na Azure Stack zásady vytváření názvů clusterů HCI Kubernetes pro pracovní uzel do názvu uzlu přidá několik předdefinovaných znaků.

Pokud chcete připojit uzly Windows Worker k doméně, přihlaste se k pracovnímu uzlu Windows spuštěním a zaznamenání `kubectl get` `EXTERNAL-IP` hodnoty.

```PowerShell
kubectl get nodes -o wide
``` 

Pak můžete přes SSH na uzel pomocí `ssh Administrator@ip` . 

Po úspěšném přihlášení k uzlu Windows Worker spusťte následující příkaz prostředí PowerShell, který připojí uzel k doméně. Zobrazí se výzva k zadání přihlašovacích údajů k **účtu správce domény** . Můžete také použít zvýšené přihlašovací údaje uživatele, kterým byla udělena práva k připojení počítačů k dané doméně. Pak budete muset restartovat pracovní uzel Windows.

```PowerShell
add-computer --domainame "YourDomainName" -restart
```

Jakmile budou všechny uzly pracovních procesů Windows připojené k doméně, postupujte podle kroků popsaných v části [Konfigurace gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa) a použijte vlastní definice prostředků Kubernetes gMSA a Webhooky v clusteru Kubernetes.

Další informace o kontejnerech Windows s gMSA najdete v tématu [kontejnery Windows a gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Další kroky
- [Nasaďte aplikaci pro Windows do clusteru Kubernetes](./deploy-windows-application.md).
- [Připojte svůj cluster Kubernetes ke službě Azure ARC pro Kubernetes](./connect-to-arc.md).
