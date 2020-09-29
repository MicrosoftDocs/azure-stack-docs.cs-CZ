---
title: Kurz – nasazení aplikace pro Windows v AKS na Azure Stack HCI
description: V tomto kurzu nasadíte aplikaci pro Windows do clusteru s použitím vlastní image uložené v Azure Container Registry.
author: abha
ms.topic: tutorial
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: f6bc0132dd7ce3ee9972b0aaff6d0718cab86843
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948893"
---
# <a name="tutorial-deploy-windows-applications-in-azure-kubernetes-service-on-azure-stack-hci"></a>Kurz: nasazení aplikací pro Windows ve službě Azure Kubernetes v Azure Stack HCL

V tomto kurzu nasadíte ukázkovou aplikaci ASP.NET v kontejneru Windows serveru do clusteru Kubernetes. Pak uvidíte, jak testovat a škálovat aplikaci. V tomto kurzu se předpokládá základní znalost konceptů Kubernetes. Další informace najdete v tématu [základní koncepty Kubernetes pro Azure Kubernetes Service v Azure Stack HCI](kubernetes-concepts.md).

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte připravené tyto požadavky:

* Služba Azure Kubernetes v Azure Stack clusteru HCI s alespoň jedním pracovním uzlem Windows, který je v provozu. 
* Soubor kubeconfig pro přístup ke clusteru.
* Musí být nainstalovaná služba Azure Kubernetes v Azure Stack modul prostředí HCL pro rozhraní HCI.
* Spusťte příkazy v tomto dokumentu v okně pro správu prostředí PowerShell.
* Zajistěte, aby se na příslušném hostiteli kontejneru naklády úlohy specifické pro operační systém. Pokud máte smíšený cluster uzlů pro Linux a Windows Worker Kubernetes, můžete použít selektory uzlů nebo příchuti a tolerování. Další informace najdete v tématu [použití selektorů uzlů a příchuti a jejich tolerovánosti](adapt-apps-mixed-os-clusters.md).

## <a name="deploy-the-application"></a>Nasazení aplikace

Soubor manifestu Kubernetes definuje požadovaný stav clusteru, například jaké image kontejnerů se mají spustit. V tomto článku se k vytvoření všech objektů potřebných ke spuštění ukázkové aplikace ASP.NET v kontejneru Windows serveru používá manifest. Tento manifest zahrnuje nasazení Kubernetes pro ukázkovou aplikaci ASP.NET a externí službu Kubernetes pro přístup k aplikaci z Internetu.

Ukázková aplikace ASP.NET se poskytuje jako součást ukázek .NET Framework a běží v kontejneru Windows serveru. Služba Azure Kubernetes na Azure Stack HCI vyžaduje, aby kontejnery Windows serveru byly založené na imagí *systému Windows server 2019*. 

Soubor manifestu Kubernetes musí také definovat selektor uzlů, aby mohl cluster AKS spustit na uzlu, na kterém je možné spustit kontejnery Windows serveru, na kterém je spuštěná vaše ukázková aplikace ASP.NET.

Vytvořte soubor s názvem `sample.yaml` a zkopírujte ho do následující definice YAML. 

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Nasaďte aplikaci pomocí `kubectl apply` příkazu a zadejte název vašeho MANIFESTU YAML:

```console
kubectl apply -f sample.yaml
```

Následující příklad výstupu ukazuje, že se úspěšně vytvořilo nasazení a služba:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Testování aplikace

Když je aplikace spuštěná, služba Kubernetes zpřístupňuje front-end aplikace na internetu. Dokončení tohoto procesu může trvat několik minut. Služba může občas trvat déle než několik minut. V těchto případech můžete trvat až 10 minut.

Chcete-li sledovat průběh, použijte `kubectl get service` příkaz s `--watch` argumentem.

```PowerShell
kubectl get service sample --watch
```

Zpočátku je *externí IP adresa* *ukázkové* služby zobrazená jako *čeká na vyřízení*.

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Pokud se *IP* adresa změní z *čekání* na skutečnou veřejnou IP adresu, použijte `CTRL-C` k zastavení `kubectl` procesu sledování. Následující příklad výstupu ukazuje platnou veřejnou IP adresu přiřazenou ke službě:

```output
NAME    TYPE           CLUSTER-IP   EXTERNAL-IP     PORT(S)        AGE
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Pokud chcete vidět ukázkovou aplikaci v akci, otevřete webový prohlížeč na externí IP adresu vaší služby.

![Obrázek přechodu na ukázkovou aplikaci ASP.NET](media/deploy-windows-application/asp-net-sample-app.png)

Pokud při pokusu o načtení stránky dojde k vypršení časového limitu připojení, ověřte, jestli je ukázková aplikace připravená pomocí `kubectl get pods --watch` příkazu. V některých případech je externí IP adresa k dispozici před spuštěním kontejneru Windows.

## <a name="scale-application-pods"></a>Škálování aplikací v luskech

Vytvořili jsme jednu repliku front-endu aplikace. Chcete-li zobrazit počet a stav lusků ve vašem clusteru, použijte `kubectl get` příkaz následujícím způsobem:

```console
kubectl get pods -n default
```

Chcete-li změnit počet lusků v *ukázkovém* nasazení, použijte `kubectl scale` příkaz. Následující příklad zvyšuje počet obou front-endu na *3*:

```console
kubectl scale --replicas=3 deployment/sample
```

Spusťte `kubectl get pods` znovu a ověřte, zda byly vytvořeny další lusky. Asi za minutu budou další pody dostupné ve vašem clusteru:

```console
kubectl get pods -n default
```

## <a name="next-steps"></a>Další kroky

* [K monitorování clusteru a aplikace použijte Azure monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Používejte trvalé úložiště a nakonfigurujte podporu gMSA v kontejneru Windows](persistent-storage-windows-nodes.md).
