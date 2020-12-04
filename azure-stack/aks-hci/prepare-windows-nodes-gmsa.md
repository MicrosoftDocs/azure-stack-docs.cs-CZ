---
title: Příprava uzlů Windows pro skupinový účet spravované služby
description: Zjistěte, jak nakonfigurovat skupinový účet spravované služby pro kontejnery v uzlech Windows.
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: 6710bb9c6a9e53dea60d14be686a4ea953e1bf19
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563557"
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

Pokud chcete připojit uzly Windows Worker k doméně, přihlaste se k pracovnímu uzlu Windows spuštěním `kubectl` Get a zaznamenání `EXTERNAL-IP` hodnoty.

```
   kubectl get nodes -o wide
```  

Pak můžete přes SSH na uzel pomocí `ssh Administrator@ip` .

Po úspěšném přihlášení k uzlu Windows Worker spusťte následující příkaz prostředí PowerShell, který připojí uzel k doméně. Zobrazí se výzva k zadání přihlašovacích údajů k **účtu správce domény** . Můžete také použít zvýšené přihlašovací údaje uživatele, kterým byla udělena práva k připojení počítačů k dané doméně. Pak je potřeba restartovat pracovní uzel Windows. 

```powershell
add-computer --domainame "YourDomainName" -restart
```

Jakmile budou všechny uzly pracovních procesů Windows připojené k doméně, postupujte podle kroků popsaných v části [Konfigurace gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/). Tyto kroky vám pomůžou použít vlastní definice prostředků Kubernetes gMSA a Webhooky v clusteru Kubernetes.

Další informace o kontejnerech Windows s gMSA najdete v tématu [kontejnery Windows a gMSA](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts). Informace o řešení potíží najdete na stránce [Poradce při potížích](troubleshoot.md) . 

## <a name="next-steps"></a>Další kroky

* [K monitorování clusteru a aplikace použijte Azure monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters).
* [Používejte trvalý svazek v clusteru Kubernetes](persistent-volume.md).
