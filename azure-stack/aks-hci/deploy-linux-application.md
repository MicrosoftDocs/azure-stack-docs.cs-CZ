---
title: Kurz – nasazení aplikace pro Linux v AKS na Azure Stack HCI
description: V tomto kurzu nasadíte aplikaci pro více kontejnerů pro Linux do clusteru s použitím vlastní image uložené v Azure Container Registry.
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 6fd907a44cdaad5f5dfe7ccb3a29f5fc6a0152b6
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948899"
---
# <a name="tutorial-deploy-linux-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Kurz: nasazení aplikací pro Linux ve službě Azure Kubernetes v Azure Stack HCL

V tomto kurzu nasadíte aplikaci s více kontejnery, která zahrnuje webový front-end a instanci databáze Redis ve službě Azure Kubernetes v clusteru Azure Stack HCI. Pak uvidíte, jak testovat a škálovat aplikaci. 

V tomto kurzu se předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service v Azure Stack HCI](kubernetes-concepts.md).

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte připravené tyto požadavky:

* Služba Azure Kubernetes v Azure Stack clusteru HCI s alespoň jedním pracovním uzlem Linux, který je v provozu. 
* Soubor kubeconfig pro přístup ke clusteru.
* Musí být nainstalovaná služba Azure Kubernetes v Azure Stack modul prostředí HCL pro rozhraní HCI.
* Spusťte příkazy v tomto dokumentu v okně pro správu prostředí PowerShell.
* Zajistěte, aby se na příslušném hostiteli kontejneru naklády úlohy specifické pro operační systém. Pokud máte smíšený cluster uzlů pro Linux a Windows Worker Kubernetes, můžete použít selektory uzlů nebo příchuti a tolerování. Další informace najdete v tématu [použití selektorů uzlů a příchuti a jejich tolerovánosti](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Nasazení aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejnerů se mají spustit. V tomto rychlém startu se manifest používá k vytvoření všech objektů potřebných ke spuštění [aplikace hlasování Azure](https://github.com/Azure-Samples/azure-voting-app-redis). Tento manifest obsahuje dvě Kubernetes nasazení – jeden pro ukázkové aplikace v Pythonu pro Azure a druhý pro instanci Redis. Vytvoří se také dvě služby Kubernetes Services – interní služba pro instanci Redis a externí služba pro přístup k aplikaci hlasování Azure z Internetu.

Vytvořte soubor s názvem `azure-vote.yaml` a zkopírujte ho do následující definice YAML.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Nasaďte aplikaci pomocí `kubectl apply` příkazu a zadejte název vašeho MANIFESTU YAML:

```PowerShell
kubectl apply -f azure-vote.yaml
```

Následující příklad výstupu ukazuje, že se nasazení a služby úspěšně vytvořily:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut.

Chcete-li sledovat průběh, použijte `kubectl get service` příkaz s `--watch` argumentem.

```PowerShell
kubectl get service azure-vote-front --watch
```

Zpočátku je *externí IP adresa* pro službu *Azure-hlas-front-end* zobrazená jako *nevyřízená*.

```output
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27      <pending>     80:30572/TCP   22m
```

Pokud se *IP* adresa změní z *čekání* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   24m
```

Pokud chcete zobrazit hlasovou aplikaci Azure v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek clusteru Kubernetes v Azure](media/deploy-linux-application/azure-vote.png)

## <a name="scale-application-pods"></a>Škálování aplikací v luskech

Vytvořili jsme jednu repliku instance webend a Redis pro hlasování Azure. Chcete-li zobrazit počet a stav lusků ve vašem clusteru, použijte `kubectl get` příkaz následujícím způsobem:

```console
kubectl get pods -n default
```

Následující příklad výstupu ukazuje jeden pod front-endu a jeden pod back-endu:

```
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1       Running   0          25m
azure-vote-front-84c8bf64fc-cdq86   1/1       Running   0          25m
```

Pokud chcete změnit počet lusků v nasazení *Azure – hlasování* , použijte `kubectl scale` příkaz. Následující příklad zvýší počet podů front-endu na *5*:

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

Spusťte `kubectl get pods` znovu a ověřte, zda byly vytvořeny další lusky. Asi za minutu budou další pody dostupné ve vašem clusteru:

```console
kubectl get pods -n default

Name                                READY   STATUS    RESTARTS   AGE
azure-vote-back-6bdcb87f89-g2pqg    1/1     Running   0          31m
azure-vote-front-84c8bf64fc-cdq86   1/1     Running   0          31m
azure-vote-front-84c8bf64fc-56h64   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-djkp8   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-jmmvs   1/1     Running   0          80s
azure-vote-front-84c8bf64fc-znc6z   1/1     Running   0          80s
```

## <a name="next-steps"></a>Další kroky

* [K monitorování clusteru a aplikace použijte Azure monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
