---
title: Příprava uzlů Windows pro skupinový účet spravované služby
description: Zjistěte, jak nakonfigurovat skupinový účet spravované služby pro kontejnery v uzlech Windows.
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: 754ebc1a365efb7efa0e96eef438ae2347a069ab
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612569"
---
# <a name="prepare-windows-nodes-for-group-managed-service-account-support"></a>Příprava uzlů Windows pro skupinový účet spravované služby

> Platí pro: AKS on Azure Stack HCI, AKS runtime na Windows serveru 2019 Datacenter

Skupinový účet spravované služby je konkrétní typ účtu služby Active Directory, který poskytuje:
- Automatická správa hesel
- Zjednodušená správa hlavního názvu služby (SPN)
- Možnost delegovat správu na jiné správce na různých serverech 

Ke konfiguraci skupinových účtů spravované služby (gMSA) pro lusky a kontejnery, které se spustí na uzlech Windows, musíte nejdřív připojit uzly Windows k doméně služby Active Directory.

Pokud chcete povolit podporu účtu spravované služby, musí mít název clusteru Kubernetes míň než 4 znaky. Musí být kratší než 4 znaky, protože maximální podporovaná délka názvu serveru připojeného k doméně je 15 znaků. AKS na Azure Stack rozhraní HCI Kubernetes konvence názvu clusteru pro pracovní uzel přidá do názvu uzlu několik předem definovaných znaků.

## <a name="join-the-worker-nodes-to-a-domain"></a>Připojení pracovních uzlů k doméně

Pokud chcete připojit uzly Windows Worker k doméně, přihlaste se k pracovnímu uzlu Windows spuštěním a zaznamenání `kubectl get` `EXTERNAL-IP` hodnoty.

```
kubectl get nodes -o wide
```  

Pak můžete přes SSH na uzel pomocí `ssh Administrator@ip` . Další informace o tom, jak se přihlásit pomocí SSH, najdete v tématu [řešení potíží s pracovními uzly Windows pomocí SSH](troubleshoot.md#troubleshooting-windows-worker-nodes).

Po úspěšném přihlášení k uzlu Windows Worker pomocí SSH spusťte následující příkaz, který připojí uzel k doméně. Pak je nutné restartovat pracovní uzel systému Windows. 

```
netdom.exe join %computername% /domain:DomainName /UserD:DomainName\UserName /PasswordD:Password
```  

Jakmile budou všechny uzly pracovních procesů Windows připojené k doméně, postupujte podle kroků popsaných v části [Konfigurace gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/). Tyto kroky vám pomůžou použít vlastní definice prostředků Kubernetes gMSA a Webhooky v clusteru Kubernetes.

Další informace o kontejnerech Windows s gMSA najdete v tématu [kontejnery Windows a gMSA](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). 

## <a name="next-steps"></a>Další kroky

* [K monitorování clusteru a aplikace použijte Azure monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Používejte trvalý svazek v clusteru Kubernetes](persistent-volume.md).
