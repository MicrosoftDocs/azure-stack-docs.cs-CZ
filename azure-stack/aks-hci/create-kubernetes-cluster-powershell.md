---
title: Rychlý Start k vytváření clusterů Kubernetes v Azure Stack HCI pomocí prostředí Windows PowerShell
description: Naučte se vytvářet Kubernetes clustery na Azure Stack HCL pomocí Windows PowerShellu.
author: jessicaguan
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: jeguan
ms.openlocfilehash: 38d65798d2f30377f3160484c9a618730cda80ea
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557136"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Rychlý Start: vytvoření clusterů Kubernetes ve Azure Stack HCI pomocí prostředí Windows PowerShell

> Platí pro: AKS on Azure Stack HCI, AKS runtime na Windows serveru 2019 Datacenter

V tomto rychlém startu se dozvíte, jak pomocí prostředí Windows PowerShell vytvořit cluster Kubernetes na Azure Stack HCI. Pak se dozvíte, jak škálovat cluster Kubernetes a upgradovat verzi Kubernetes vašeho clusteru. Pokud chcete místo toho použít Centrum pro správu systému Windows, přečtěte si téma [nastavení služby Azure Kubernetes v Azure Stack HCI pomocí centra pro správu systému Windows](setup.md).

## <a name="before-you-begin"></a>Než začnete

 - Ujistěte se, že jste nastavili Azure Stack hostitele Kubernetes. Pokud to neuděláte, přečtěte si téma [rychlý Start: nastavení hostitele služby Azure Kubernetes na Azure Stack HCL pomocí PowerShellu](./setup-powershell.md).
 - Ujistěte se, že máte nainstalovaný nejnovější modul prostředí PowerShell Aks-Hci. Pokud ne, přečtěte si téma [Stažení a instalace modulu AksHci prostředí PowerShell](./setup-powershell.md#step-1-download-and-install-the-akshci-powershell-module).

## <a name="step-1-create-a-kubernetes-cluster"></a>Krok 1: Vytvoření clusteru Kubernetes

Po instalaci hostitele služby Azure Kubernetes jste připraveni nasadit cluster Kubernetes.

Otevřete PowerShell jako správce a spusťte následující příkaz.

   ```powershell
   New-AksHciCluster -clusterName <String>
                    [-kubernetesVersion <String>]
                    [-controlPlaneNodeCount <int>]
                    [-linuxNodeCount <int>]
                    [-windowsNodeCount <int>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-linuxNodeVmSize <VmSize>]
                    [-windowsNodeVmSize <VmSize>]
   ```

### <a name="example"></a>Příklad

   ```powershell
   New-AksHciCluster -clusterName mynewcluster -kubernetesVersion v1.18.8 -controlPlaneNodeCount 1 -linuxNodeCount 1 -windowsNodeCount 0 
   ```

### <a name="required-parameters"></a>Požadované parametry

`-clusterName`

Alfanumerický název clusteru Kubernetes.

### <a name="optional-parameters"></a>Volitelné parametry

`-kubernetesVersion`

Verze Kubernetes, kterou chcete nasadit. Výchozí hodnota je v 1.18.8. Pokud chcete získat seznam dostupných verzí, spusťte příkaz `Get-AksHciKubernetesVersion` .

`-controlPlaneNodeCount`

Počet uzlů v rovině ovládacího prvku. Výchozí hodnota je 1.

`-linuxNodeCount`

Počet uzlů Linux v clusteru Kubernetes. Výchozí hodnota je 1.

`-windowsNodeCount`

Počet uzlů Windows v clusteru Kubernetes. Výchozí hodnota je 0. Uzly systému Windows můžete nasadit pouze v případě, že používáte Kubernetes v 1.18.8.

`-controlPlaneVmSize`

Velikost virtuálního počítače roviny ovládacího prvku. Výchozí hodnota je Standard_A2_v2. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

`-loadBalancerVmSize`

Velikost virtuálního počítače nástroje pro vyrovnávání zatížení. Výchozí hodnota je Standard_A2_V2. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

`-linuxNodeVmSize`

Velikost virtuálního počítače uzlu se systémem Linux. Výchozí hodnota je Standard_K8S3_v1. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

`-windowsNodeVmSize`

Velikost virtuálního počítače uzlu Windows Výchozí hodnota je Standard_K8S3_v1. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

### <a name="check-your-deployed-clusters"></a>Kontrolovat nasazené clustery

Pokud chcete získat seznam nasazených hostitelů služby Azure Kubernetes a clusterů Kubernetes, spusťte následující příkaz.

```powershell
Get-AksHciCluster
```

## <a name="step-2-scale-a-kubernetes-cluster"></a>Krok 2: škálování clusteru Kubernetes

Pokud potřebujete škálovat cluster nahoru nebo dolů, můžete změnit počet uzlů řídicích ploch, pracovních uzlů Linux nebo pracovních uzlů systému Windows.

Chcete-li změnit velikost uzlů řídicích rovin, spusťte následující příkaz.

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -controlPlaneNodeCount <int>
```

Chcete-li škálovat pracovní uzly, spusťte následující příkaz.

```powershell
Set-AksHciClusterNodeCount –clusterName <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

Uzel roviny ovládacího prvku a pracovní uzly musí být škálované nezávisle.

### <a name="example"></a>Příklad

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -controlPlaneNodeCount 3
```

```powershell
Set-AksHciClusterNodeCount –clusterName mynewcluster -linuxNodeCount 2 -windowsNodeCount 2 
```

## <a name="step-3-upgrade-kubernetes-version"></a>Krok 3: upgrade verze Kubernetes

Pokud chcete zobrazit seznam dostupných verzí Kubernetes, spusťte následující příkaz.

```powershell
Get-AksHciKubernetesVersion
```

Chcete-li provést aktualizaci na další verzi Kubernetes, spusťte následující příkaz.

```powershell
Update-AksHciCluster -clusterName <String>
                     [-patch]
```
Každá verze Kubernetes má hlavní vydanou verzi, dílčí verzi a verzi opravy. Například ve verzi v 1.18.6 je 1 hlavní verze, 18 je podverze a 6 je verze opravy. V průběhu času bude AKS-HCI podporovat jednu hlavní verzi, tři podverze a dvě opravy na dílčí verzi, a to celkem 6 podporovaných verzí. Pro tuto verzi Preview ale podporujeme celkem 4 verze 1.16.10, v 1.16.15, v 1.17.11, v 1.18.8. 

Pokud je při `patch` spuštění parametru přidán `Update-AksHciCluster` , příkaz provede upgrade na další verzi opravy (pokud existuje) pro dílčí verzi. Když se příkaz spustí bez parametru `patch` , je výchozím prostředím upgradu další podverze. Aby to bylo snazší, následující tabulka obsahuje všechna možná prostředí aktualizace:

| Aktuální verze           | Kubernetes aktualizované verze bez-patch         | Kubernetes aktualizované verze pomocí-patch
| ---------------------------- | ------------ | -------------------------------- |
| v 1.16.10           |     v 1.17.11      | v 1.16.15
| v 1.16.15            | v 1.17.11 | upgrade přidaných na místo
| v 1.17.11           |  v 1.18.8          | upgrade přidaných na místo
| v 1.18.8             | upgrade přidaných na místo   | upgrade přidaných na místo

V části přidaný upgrade aktualizace se aktualizují všechny doplňky Kubernetes, jako je například CSI, které pro vás spravuje AKS-HCI. Tento upgrade nemění verzi operačního systému uzlu. Nemění také verzi Kubernetes.

### <a name="example---upgrade-kubernetes-version-to-the-next-minor-version"></a>Příklad: upgrade verze Kubernetes na další dílčí verzi

```powershell
Update-AksHciCluster -clusterName mynewcluster
```

### <a name="example---upgrade-kubernetes-version-to-the-next-patch-version"></a>Příklad: upgrade verze Kubernetes na další verzi patch

```powershell
Update-AksHciCluster -clusterName mynewcluster -patch
```


## <a name="step-4-access-your-clusters-using-kubectl"></a>Krok 4: přístup ke clusterům pomocí kubectl

Pokud chcete získat přístup ke clusterům Kubernetes pomocí kubectl, spusťte následující příkaz. Tato akce použije zadaný soubor kubeconfig clusteru jako výchozí soubor kubeconfig pro kubectl.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Příklad

```powershell
Get-AksHciCredential -clusterName mynewcluster
```

### <a name="required-parameters"></a>Povinné parametry

`clusterName`

Název clusteru.

### <a name="optional-parameters"></a>Volitelné parametry

`outputLocation`

Umístění chcete stáhnout kubeconfig. Výchozí je `%USERPROFILE%\.kube`.

## <a name="delete-a-kubernetes-cluster"></a>Odstranění clusteru Kubernetes

Pokud potřebujete odstranit cluster Kubernetes, spusťte následující příkaz.

```powershell
Remove-AksHciCluster -clusterName
```

### <a name="example"></a>Příklad

```powershell
Remove-AksHciCluster -clusterName mynewcluster
```

## <a name="get-logs"></a>Získání protokolů

Pokud chcete získat protokoly ze všech lusků, spusťte následující příkaz. Tento příkaz vytvoří výstupní složku zip s názvem `akshcilogs` v cestě `C:\wssd\akshcilogs` .

```powershell
Get-AksHciLogs
```

V tomto rychlém startu jste zjistili, jak vytvořit, škálovat a upgradovat verzi Kubernetes clusteru pomocí prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

- [Připojte své clustery ke službě Azure ARC pro Kubernetes](./connect-to-arc.md).
- [Nasaďte aplikaci pro Linux do clusteru Kubernetes](./deploy-linux-application.md).
- [Nasaďte aplikaci pro Windows do clusteru Kubernetes](./deploy-windows-application.md).
