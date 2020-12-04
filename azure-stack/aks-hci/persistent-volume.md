---
title: Použití trvalého svazku v AKS ve Azure Stack clusteru HCI
description: Použití trvalého úložiště v kontejneru Windows a příprava uzlů Windows pro skupinové účty spravované služby
author: v-susbo
ms.topic: how-to
ms.date: 11/19/2020
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: af18b885999ce4eaa387f7268a77ab3f1bb51d9f
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563558"
---
# <a name="use-persistent-volume-in-an-aks-on-azure-stack-hci-cluster"></a>Použití trvalého svazku v AKS ve Azure Stack clusteru HCI

> Platí pro: AKS on Azure Stack HCI, AKS runtime na Windows serveru 2019 Datacenter

Trvalý svazek představuje část úložiště, která byla zřízena pro použití s Kubernetes lusky. Trvalý svazek lze použít v jednom nebo více luskech a je určen pro dlouhodobé uložení. Je také nezávislá na životním cyklu pod nebo Node. I když můžete zřídit okruh PVC pro uzly Windows i Linux, v této části se dozvíte, jak vytvořit trvalý svazek a jak tento svazek použít v aplikaci pro Windows. Další informace najdete v tématu [trvalé svazky v Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/).

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

```
kubectl create -f pvc-akshci-csi.yaml 
```
Následující výstup zobrazí, že se úspěšně vytvořila deklarace identity trvalého svazku:

**Výkonem**
```
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
```
kubectl create -f winwebserver.yaml 
```

Chcete-li se ujistit, že je pod ním spuštěný, spusťte následující příkaz. Počkejte několik minut, než je v běžícím stavu, protože načítání image trvá déle. 
```
kubectl get pods -o wide 
```
Jakmile je spuštěno, zobrazte stav pod spuštěním následujícího příkazu: 
```
kubectl.exe describe pod %podName% 
```

Chcete-li ověřit, zda byl svazek připojen v části pod, spusťte následující příkaz:
```
kubectl exec -it %podname% cmd.exe 
```

## <a name="delete-a-persistent-volume-claim"></a>Odstranění deklarace identity trvalého svazku

Před odstraněním deklarace identity trvalého svazku je nutné nasazení aplikace odstranit spuštěním:
```
kubectl delete deployments win-webserver
```

Pak můžete odstranit vynucenou deklaraci identity trvalého svazku spuštěním:
```
kubectl delete PersistentVolumeClaim pvc-akshci-csi
```

## <a name="next-steps"></a>Další kroky
- [Nasaďte aplikaci pro Windows do clusteru Kubernetes](./deploy-windows-application.md).
- [Připojte svůj cluster Kubernetes ke službě Azure ARC pro Kubernetes](./connect-to-arc.md).
