---
title: Rychlý Start k vytváření clusterů Kubernetes v Azure Stack HCI pomocí prostředí Windows PowerShell
description: Naučte se vytvářet Kubernetes clustery na Azure Stack HCL pomocí Windows PowerShellu.
author: jessicaguan
ms.topic: quickstart
ms.date: 2/10/2021
ms.author: jeguan
ms.openlocfilehash: 68539a77907bb0c5eef4809882c562525b93e983
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116729"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Rychlý Start: vytvoření clusterů Kubernetes ve Azure Stack HCI pomocí prostředí Windows PowerShell

> Platí pro: AKS on Azure Stack HCI, AKS runtime na Windows serveru 2019 Datacenter

V tomto rychlém startu se dozvíte, jak pomocí prostředí Windows PowerShell vytvořit cluster Azure Kubernetes na Azure Stack HCI. Pak se dozvíte, jak škálovat cluster Kubernetes a upgradovat verzi Kubernetes vašeho clusteru. Pokud chcete místo toho použít Centrum pro správu systému Windows, přečtěte si téma [nastavení služby Azure Kubernetes v Azure Stack HCI pomocí centra pro správu systému Windows](setup.md).

## <a name="before-you-begin"></a>Než začnete

 - Ujistěte se, že jste nastavili Azure Stack hostitele Kubernetes. Pokud to neuděláte, přečtěte si téma [rychlý Start: nastavení hostitele služby Azure Kubernetes na Azure Stack HCL pomocí PowerShellu](./setup-powershell.md).
 - Ujistěte se, že máte nainstalovaný nejnovější modul prostředí PowerShell Aks-Hci. Pokud to neuděláte, přečtěte si téma [Stažení a instalace modulu AksHci PowerShellu](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module).

## <a name="step-1-create-a-kubernetes-cluster"></a>Krok 1: Vytvoření clusteru Kubernetes

Po instalaci hostitele služby Azure Kubernetes jste připraveni nasadit cluster Kubernetes.

Otevřete PowerShell jako správce a spusťte následující příkaz [New-AksHciCluster](./new-akshcicluster.md) .

```powershell
New-AksHciCluster -name mycluster
```

### <a name="check-your-deployed-clusters"></a>Kontrolovat nasazené clustery

Pokud chcete získat seznam nasazených hostitelů služby Azure Kubernetes a clusterů Kubernetes, spusťte následující příkaz.

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>Krok 2: škálování clusteru Kubernetes

Pokud potřebujete škálovat cluster nahoru nebo dolů, můžete změnit počet uzlů řídicích ploch, pracovních uzlů Linux nebo pracovních uzlů systému Windows pomocí příkazu [set-AksHciClusterNodeCount](./set-akshciclusternodecount.md) .

Chcete-li změnit velikost uzlů řídicích rovin, spusťte následující příkaz.

```powershell
Set-AksHciClusterNodeCount –name mycluster -controlPlaneNodeCount 3
```

Chcete-li škálovat pracovní uzly, spusťte následující příkaz.

```powershell
Set-AksHciClusterNodeCount –name mycluster -linuxNodeCount 3 -windowsNodeCount 1
```

Uzel roviny ovládacího prvku a pracovní uzly musí být škálované nezávisle.

## <a name="step-3-upgrade-kubernetes-version"></a>Krok 3: upgrade verze Kubernetes

Pokud chcete zjistit, jestli jsou pro cluster Kubernetes dostupné nějaké upgrady, spusťte následující příkaz.

```powershell
Get-AksHciClusterUpgrades -name mycluster
```

Chcete-li provést aktualizaci na nejnovější verzi Kubernetes, spusťte následující příkaz.

```powershell
Update-AksHciCluster -name mycluster -kubernetesVersion <k8s version>
```

Spuštěním tohoto příkazu bez zadání verze Kubernetes se upgraduje cluster na nejnovější verzi. Pokud chcete upgradovat na jinou verzi, která není nejnovější, použijte [příkaz Update-AksHciCluster](./update-akshcicluster.md) s `-kubernetesVersion` parametrem a požadovanou verzí jako hodnotu (tj. v 1.18.8).

## <a name="step-4-access-your-clusters-using-kubectl"></a>Krok 4: přístup ke clusterům pomocí kubectl

Pokud chcete získat přístup ke clusterům Kubernetes pomocí kubectl, spusťte příkaz [Get-AksHciCredential](./get-akshcicredential.md) . Tato akce použije zadaný soubor kubeconfig clusteru jako výchozí soubor kubeconfig pro kubectl.

```powershell
Get-AksHciCredential -name mycluster
```

## <a name="delete-a-kubernetes-cluster"></a>Odstranění clusteru Kubernetes

Pokud potřebujete odstranit cluster Kubernetes, spusťte následující příkaz.

```powershell
Remove-AksHciCluster -name mycluster
```

## <a name="get-logs"></a>Získání protokolů

Pokud chcete získat protokoly ze všech lusků, spusťte příkaz [Get-AksHciLogs](./get-akshcilogs.md) . Tento příkaz vytvoří výstupní složku zip `akshcilogs.zip` s názvem v pracovním adresáři. Úplná cesta ke `akshcilogs.zip` složce bude výstup po spuštění příkazu níže.

```powershell
Get-AksHciLogs
```

V tomto rychlém startu jste zjistili, jak vytvořit, škálovat a upgradovat verzi Kubernetes clusteru pomocí prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

- [Připojte své clustery ke službě Azure ARC pro Kubernetes](./connect-to-arc.md).
- [Nasaďte aplikaci pro Linux do clusteru Kubernetes](./deploy-linux-application.md).
- [Nasaďte aplikaci pro Windows do clusteru Kubernetes](./deploy-windows-application.md).
