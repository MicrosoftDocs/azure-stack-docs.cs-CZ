---
title: Povoluje se projekce svazku tokenu účtu služby pro modul AKS v centru Azure Stack.
description: Přečtěte si, jak povolit projekci svazku tokenu účtu služby pro modul AKS v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 10/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 5e712577bb01f7f084c24eadfd3931ebb67f23d9
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906160"
---
# <a name="enabling-service-account-token-volume-projection-for-the-aks-engine-on-azure-stack-hub"></a>Povoluje se projekce svazku tokenu účtu služby pro modul AKS v centru Azure Stack.

Istio je konfigurovatelná, open source vrstva – mřížka, která spojuje, monitoruje a zabezpečuje kontejnery v clusteru Kubernetes. Istio 1,3 a novější používá funkci v Kubernetes s názvem *projekce svazku tokenu účtu služby* . Tato funkce není ve výchozím nastavení povolená v clusterech Kubernetes nasazených modulem AKS Engine. V tomto článku najdete vlastnosti JSON modelu rozhraní API v `apiServerConfig` elementu, který zobrazuje příznaky serveru KUBERNETES API, které jsou nutné k povolení projekce svazku tokenu účtu služby pro váš cluster.

Další informace o projekci svazku tokenu účtu služby najdete v tématu [projekce svazku tokenu účtu služby](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection).

## <a name="enable-service-account-token-volume-projection"></a>Povolit projekce svazku tokenu účtu služby

K povolení projekce svazku tokenu účtu služby přidejte do souboru JSON modelu rozhraní API následující nastavení. 

```json
{
    "kubernetesConfig": {
        "apiServerConfig": {
            "--service-account-api-audiences": "api,istio-ca",
            "--service-account-issuer": "kubernetes.default.svc",
            "--service-account-signing-key-file": "/etc/kubernetes/certs/apiserver.key"
        }
    }
}
```

> [!Note]  
> Možná budete muset upravit `--service-account-api-audiences` a `--service-account-issuer` na svůj konkrétní případ použití.

Úplný ukázkový model rozhraní API najdete [ v tématuistio.jsv](https://github.com/Azure/aks-engine/blob/master/examples/service-mesh/istio.json).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .
- [Upgrade clusteru Kubernetes na rozbočovači Azure Stack](azure-stack-kubernetes-aks-engine-upgrade.md)
