---
title: Přidání úložiště kontejneru do Kubernetes v centru Azure Stack
description: Naučte se, jak přidat úložiště kontejnerů do Kubernetes v centru Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 3/4/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/4/2021
ms.openlocfilehash: ee176f8e111c6e10831124b78afd99535d2fe7ee
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102233841"
---
# <a name="add-container-storage-to-kubernetes-in-azure-stack-hub"></a>Přidání úložiště kontejneru do Kubernetes v centru Azure Stack

> [!IMPORTANT]  
> Tato funkce je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V rámci snahy komunity Kubernetes ([migrace svazku Kubernetes ve stromové struktuře do prostředí CSI](https://kubernetes.io/blog/2019/12/09/kubernetes-1-17-feature-csi-migration-beta/)) pro přesun zprostředkovatelů multilicencí mezi stromovou strukturou do rozhraní služby kontejneru úložiště na platformě [CSI](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)můžete najít následující dva ovladače CSI v Azure Stack: disk Azure a systém souborů NFS.

|                       | **Ovladač Azure disk CSI**                                                                                                    | **Ovladač NFS CSI**                                                       |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|
| Úložiště projektu    | [azuredisk – CSI – ovladač](https://github.com/kubernetes-sigs/azuredisk-csi-driver)                                              | [CSI – ovladač-systém souborů NFS](https://github.com/kubernetes-csi/csi-driver-nfs)       |
| Verze ovladače CSI    | v 1.0.0 +                                                                                                                      | v 3.0.0 +                                                                  |
| Režim přístupu           | ReadWriteOnce                                                                                                                | ReadWriteOnce ReadOnlyMany ReadWriteMany                                 |
| Uzel agenta systému Windows    | Podpora                                                                                                                      | Nepodporováno                                                              |
| Dynamické zřizování  | Podpora                                                                                                                      | Podpora                                                                  |
| Požadavky        | [Omezení ovladačů Azure disk CSI](https://github.com/kubernetes-sigs/azuredisk-csi-driver/blob/master/docs/limitations.md) | Uživatelé budou odpovědni za nastavení a údržbu serveru NFS. |
| Kanál podpory časové rezervy | [\#poskytovatel – Azure](https://kubernetes.slack.com/archives/C5HJXTT9Q)                                                          | [\#SIG – úložiště](https://kubernetes.slack.com/archives/C09QZFCE5)         |

V současné době odpojená prostředí nepodporují ovladače CSI.

## <a name="requirements"></a>Požadavky

-   Azure Stack Build 2011 a novější.
-   AKS Engine verze v 0.60.1 a novějších verzích.
-   Kubernetes verze 1,18 a novější.
-   Vzhledem k tomu, že server kontroleru ovladačů CSI vyžaduje dvě repliky, nedoporučuje se jeden fond hlavních uzlů.
-   [Helm 3](https://helm.sh/docs/intro/install/)

## <a name="install-and-uninstall-csi-drivers"></a>Instalovat a odinstalovat ovladače CSI

V této části použijte příklady příkazů k nasazení statefulset aplikace využívající rozhraní CSI.

## <a name="azure-disk-csi-driver"></a>Ovladač Azure disk CSI

### <a name="install-csi-driver"></a>Nainstalovat ovladač CSI

```bash  
helm repo add azuredisk-csi-driver https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/charts
helm install azuredisk-csi-driver azuredisk-csi-driver/azuredisk-csi-driver --namespace kube-system --set cloud=AzureStackCloud --set controller.runOnMaster=true --version v1.0.0

```
### <a name="deploy-storage-class"></a>Nasadit třídu úložiště

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/storageclass-azuredisk-csi-azurestack.yaml
```

### <a name="deploy-example-statefulset-application"></a>Nasadit ukázkovou aplikaci statefulset

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/statefulset.yaml
```

### <a name="validate-volumes-and-applications"></a>Ověřit svazky a aplikace

Měla by se zobrazit sekvence časových razítek ve svazku.

```bash  
kubectl exec statefulset-azuredisk-0 -- tail /mnt/azuredisk/outfile
```

### <a name="delete-example-statefulset-application"></a>Odstranit příklad aplikace statefulset

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/statefulset.yaml
```

### <a name="delete-storage-class"></a>Odstranit třídu úložiště

Před odstraněním třídy úložiště se ujistěte, že byly ukončeny lusky, které spotřebovávají třídu úložiště.

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/azuredisk-csi-driver/master/deploy/example/storageclass-azuredisk-csi-azurestack.yaml
```

### <a name="uninstall-csi-driver"></a>Odinstalace ovladače CSI

```bash  
helm uninstall azuredisk-csi-driver --namespace kube-system
helm repo remove azuredisk-csi-driver
```

## <a name="nfs-csi-driver"></a>Ovladač NFS CSI

### <a name="install-csi-driver"></a>Nainstalovat ovladač CSI

```bash  
helm repo add csi-driver-nfs https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/charts
helm install csi-driver-nfs csi-driver-nfs/csi-driver-nfs --namespace kube-system --set controller.runOnMaster=true --version v3.0.0
```

### <a name="deploy-nfs-server"></a>Nasazení serveru NFS

> [!NOTE]  
> Server NFS je jenom pro ověření, nastavuje a udržuje pro produkční prostředí správný server NFS.

Nastavte a udržujte svůj server NFS správně pro produkční prostředí.

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="deploy-storage-class"></a>Nasadit třídu úložiště

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/storageclass-nfs.yaml
```

### <a name="deploy-example-statefulset-application"></a>Nasadit ukázkovou aplikaci statefulset

```bash  
kubectl apply -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/statefulset.yaml
```

### <a name="validate-volumes-and-applications"></a>Ověřit svazky a aplikace

Měla by se zobrazit sekvence časových razítek ve svazku.

```bash  
kubectl exec statefulset-nfs-0 -- tail /mnt/nfs/outfile
```

### <a name="delete-example-statefulset-application"></a>Odstranit příklad aplikace statefulset

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="delete-storage-class"></a>Odstranit třídu úložiště

Před odstraněním třídy úložiště se ujistěte, že byly ukončeny lusky, které spotřebovávají třídu úložiště.

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/storageclass-nfs.yaml
```
### <a name="delete-example-nfs-server"></a>Odstranit příklad serveru NFS

```bash  
kubectl delete -f https://raw.githubusercontent.com/kubernetes-csi/csi-driver-nfs/master/deploy/example/nfs-provisioner/nfs-server.yaml
```

### <a name="uninstall-csi-driver"></a>Odinstalace ovladače CSI

```bash  
helm uninstall csi-driver-nfs --namespace kube-system
helm repo remove csi-driver-nfs
```
## <a name="azure-disk-csi-driver-limitations-on-azure-stack-hub"></a>Omezení ovladačů Azure disk CSI v centru Azure Stack

-   Azure disk IOPS je omezené na 2300. podrobné informace o [velikostech virtuálních počítačů podporovaných v centru Azure Stack](azure-stack-vm-sizes.md) najdete.
-   Centrum Azure Stack nepodporuje sdílený disk, takže parametr `maxShares` větší než 1 není platný v StorageClass.
-   Rozbočovač Azure Stack podporuje jenom standardní typy účtů úložiště (Standard_LRS) a úrovně Premium, které jsou místně redundantní (Premium_LRS), takže Standard_LRS a Premium_LRS jsou platné jenom pro parametr `skuName` v `StorageClass` .
-   Centrum Azure Stack nepodporuje přírůstkový snímek disku, takže jenom hodnota false je platná pro parametr `incremental` v `VolumeSnapshotClass` .
-   V případě uzlů agentů Windows budete muset nainstalovat proxy Windows CSI, podívejte se na [proxy Windows CSI](https://github.com/kubernetes-csi/csi-proxy). Pokud chcete povolit proxy prostřednictvím modelu rozhraní API AKS Engine, přečtěte si web [CSI proxy pro Windows](https://github.com/Azure/aks-engine/blob/master/docs/topics/csi-proxy-windows.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .  
