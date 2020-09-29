---
title: Přizpůsobení aplikací pro použití v clusterech Kubernetes se smíšeným operačním systémem
description: Jak používat selektory uzlů nebo chuti a funkce pro službu Azure Kubernetes, abyste zajistili, že se aplikace v kombinovaných clusterech Kubernetes s operačním systémem běžícím na Azure Stack HCI naplánovaly na správném operačním systému pracovního uzlu
author: abha
ms.topic: how-to
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: e70ac456929b5c8402e49c969ca8b9c87a703311
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948942"
---
# <a name="adapt-apps-for-mixed-os-kubernetes-clusters-using-node-selectors-or-taints-and-tolerations"></a>Přizpůsobení aplikací pro clustery Kubernetes se smíšenými operačními systémy pomocí selektorů uzlů nebo příchuti a tolerovánosti

Služba Azure Kubernetes ve službě Azure Stack HCI umožňuje spouštět clustery Kubernetes s uzly Linux i Windows, ale vyžaduje, abyste v těchto clusterech se smíšeným operačním systémem provedli malé úpravy, aby je bylo možné použít. V tomto průvodci se dozvíte, jak zajistit, aby se aplikace naplánovala na správném hostitelském operačním systému pomocí selektorů uzlů nebo chuti a příjímání.

Tato příručka předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service v Azure Stack HCI](kubernetes-concepts.md).

## <a name="node-selector"></a>Selektor uzlů

*Selektor uzlů* je jednoduché pole ve specifikaci pod specifikací pod YAML, které omezuje lusky tak, aby se naplánovaly jenom na zdravých uzlech, které odpovídají operačnímu systému. Ve specifikaci pod YAML zadejte  `nodeSelector`   -Windows nebo Linux, jak je znázorněno v níže uvedených příkladech. 

```yaml
kubernetes.io/os = Windows
```
ani

```yaml
kubernetes.io/os = Linux
```

Další informace o nodeSelectors najdete v [selektorech uzlů](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/). 

## <a name="taints-and-tolerations"></a>Příchuti a tolerovánosti

*Chuti* a *tolerovány* vzájemně spolupracují, aby se zajistilo, že lusky nejsou na uzlech záměrně naplánované. Uzel může být "ve stavu", aby nepřijímal lusky, které explicitně nedovolují jeho chuti pomocí "tolerovánosti" ve specifikaci "pod" YAML.

Uzly operačního systému Windows ve službě Azure Kubernetes v Azure Stack HCI se můžou nacházet s následující dvojicí klíč-hodnota. Uživatelé by neměli používat jiný uživatel.

```yaml
node.kubernetes.io/os=Windowss:NoSchedule
```
Spusťte `kubectl get` a Identifikujte pracovní uzly systému Windows, které chcete použít pro obchuti.

```PowerShell
kubectl get nodes --all-namespaces -o=custom-columns=NAME:.metadata.name,OS:.status.nodeInfo.operatingSystem
```
Výstup:
```output
NAME                                     OS
my-aks-hci-cluster-control-plane-krx7j   linux
my-aks-hci-cluster-md-md-1-5h4bl         windows
my-aks-hci-cluster-md-md-1-5xlwz         windows
```

Pracovní uzly Windows serveru s příchuti pomocí `kubectl taint node` .

```PowerShell
kubectl taint node my-aks-hci-cluster-md-md-1-5h4bl node.kubernetes.io/os=Windows:NoSchedule
kubectl taint node my-aks-hci-cluster-md-md-1-5xlwz node.kubernetes.io/os=Windows:NoSchedule
```

Zadáte tolerovat hodnotu v poli pod specifikací pod (YAML). Následující funkce tolerovat "odpovídá" chuti vytvořeným čárou kubectl (chuti), a tak i pod ním by bylo možné naplánovat na My-AKS-HCI-cluster-MD-MD-1-5h4bl nebo My-AKS-HCI-cluster-MD-MD-1-5xlwz:

```yaml
tolerations:
- key: node.kubernetes.io/os
  operator: Equal
  value: Windows
  effect: NoSchedule
```
Další informace o chuti a tolerováních najdete v informacích o [chuti a tolerováních](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/). 

## <a name="next-steps"></a>Další kroky

V této příručce se naučíte přidávat selektory uzlů nebo příchuti a jejich tolerování do clusterů Kubernetes pomocí kubectl. V dalším kroku můžete:
- [Nasaďte aplikace pro Linux do clusteru Kubernetes](./deploy-linux-application.md).
- [Nasaďte aplikaci Windows serveru v clusteru Kubernetes](./deploy-windows-application.md).
