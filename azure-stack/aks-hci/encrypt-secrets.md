---
title: Šifrování etcd tajných kódů v AKS na Azure Stack HCL
description: Naučte se šifrovat tajné klíče etcd v AKS na Azure Stack HCI.
author: aabhathipsay
ms.topic: how-to
ms.date: 02/02/2021
ms.author: aaabhathipsay
ms.reviewer: ''
ms.openlocfilehash: d79d6657197237b5806e419d94fbfb75a31a986c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873656"
---
# <a name="encrypt-etcd-secrets-on-aks-on-azure-stack-hci-clusters"></a>Šifrování etcd tajných kódů v AKS v Azure Stack clusterech HCI

Tajný kód v Kubernetes je objekt, který obsahuje malé množství citlivých dat, jako jsou hesla a klíče SSH. V serveru rozhraní API Kubernetes jsou tajné klíče uložené v _etcd_, což je úložiště s vysokou dostupností, které se používá jako úložiště záloh Kubernetes pro všechna data clusteru. Pokud povolíte šifrování tajných kódů etcd, můžete [Šifrovat tajná data v klidovém umístění](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/) a také automatizovat správu a rotaci šifrovacích klíčů. 

> [!NOTE]
> V této verzi Preview je šifrování tajných klíčů etcd k dispozici pouze pro nové clustery úloh. 

## <a name="enable-encryption-of-etcd-secrets"></a>Povolit šifrování tajných kódů etcd

Pomocí `-enableSecretsEncryption` parametru příkazu [New-AksHciCluster](./new-akshcicluster) Povolte šifrování tajných kódů etcd a automatizujte střídání šifrovacího klíče, jak je znázorněno níže: 

```powershell
New-AksHciCluster -name mynewcluster -enableSecretsEncryption
```

Když nasadíte nový cluster s `-enableSecretsEncryption` parametrem, nemůžete tuto funkci zakázat.

## <a name="monitor-and-troubleshoot"></a>Monitorování a odstraňování potíží

Pokud chcete zjednodušit nasazení aplikací v clusterech Kubernetes, Projděte si [dokumentaci a skripty](https://github.com/microsoft/AKS-HCI-Apps) , které jsou k dispozici.

- Pokud chcete nastavit protokolování pomocí Elasticsearch, Fluent a Kibana, postupujte podle pokynů pro [instalaci nástrojů a nastavení protokolování](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging) .
- Pokud chcete použít nástroj pro monitorování Prometheus, postupujte podle pokynů k [instalaci Prometheus v clusteru Kubernetes](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#certs-and-keys-monitoring) .

> [!NOTE]
> Protokoly v uzlu řídicí roviny najdete v části `/var/log/pods` .

## <a name="additional-resources"></a>Další zdroje informací

- [Šifrování neaktivních tajných dat](https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data)
- [Použití poskytovatele služby správy klíčů pro šifrování dat](https://kubernetes.io/docs/tasks/administer-cluster/kms-provider/)

## <a name="next-steps"></a>Další kroky

V této příručce se naučíte, jak povolit šifrování etcd tajných kódů pro nové clustery úloh. V dalším kroku můžete:
- [Nasaďte aplikace pro Linux do clusteru Kubernetes](./deploy-linux-application.md).
- [Nasaďte aplikaci Windows serveru v clusteru Kubernetes](./deploy-windows-application.md).
