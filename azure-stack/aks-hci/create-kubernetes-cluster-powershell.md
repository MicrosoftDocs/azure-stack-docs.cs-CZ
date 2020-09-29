---
title: Rychlý Start k vytváření clusterů Kubernetes v Azure Stack HCI pomocí prostředí Windows PowerShell
description: Naučte se vytvářet Kubernetes clustery na Azure Stack HCL pomocí Windows PowerShellu.
author: jessicaguan
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: jeguan
ms.openlocfilehash: 35d527b56a2429676d343ba8098fc6821835fb00
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948880"
---
# <a name="quickstart-create-kubernetes-clusters-on-azure-stack-hci-using-windows-powershell"></a>Rychlý Start: vytvoření clusterů Kubernetes ve Azure Stack HCI pomocí prostředí Windows PowerShell

> Platí pro: Azure Stack HCI

V tomto rychlém startu se dozvíte, jak pomocí prostředí Windows PowerShell vytvořit cluster Kubernetes na Azure Stack HCI. Pokud chcete místo toho použít Centrum pro správu systému Windows, přečtěte si téma [nastavení služby Azure Kubernetes v Azure Stack HCI pomocí centra pro správu systému Windows](setup.md).

## <a name="before-you-begin"></a>Než začnete

Než začnete, ujistěte se, že jste:

- Mít cluster 2-4 Azure Stack HCI nebo jeden uzel Azure Stack HCI. **Doporučujeme, abyste měli Azure Stack clusteru HCI v uzlu 2-4.** Pokud to neuděláte, postupujte podle pokynů v [tématu](./system-requirements.md)jak ho vytvořit.
- Mít nastaveného hostitele Kubernetes Azure Stack. Pokud to neuděláte, postupujte podle pokynů uvedených [tady](./setup-powershell.md).

## <a name="step-1-create-a-kubernetes-cluster"></a>Krok 1: Vytvoření clusteru Kubernetes

Po instalaci hostitele služby Azure Kubernetes jste připraveni nasadit cluster Kubernetes.

Otevřete PowerShell jako správce a spusťte následující příkaz.

   ```powershell
   New-AksHciCluster -clusterName
                    [-kubernetesVersion]
                    [-controlPlaneNodeCount]
                    [-linuxNodeCount]
                    [-windowsNodeCount]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-linuxNodeVmSize]
                    [-windowsNodeVmSize]
   ```

### <a name="required-parameters"></a>Požadované parametry

`-clusterName`

Alfanumerický název clusteru Kubernetes.

### <a name="optional-parameters"></a>Volitelné parametry

`-kubernetesVersion`

Verze Kubernetes, kterou chcete nasadit. Výchozí hodnota je v 1.18.6. Pokud chcete získat seznam dostupných verzí, spusťte příkaz `Get-AksHciKubernetesVersion` .

`-controlPlaneNodeCount`

Počet uzlů v rovině ovládacího prvku. Výchozí hodnota je 1.

`-linuxNodeCount`

Počet uzlů Linux v clusteru Kubernetes. Výchozí hodnota je 1.

`-windowsNodeCount`

Počet uzlů Windows v clusteru Kubernetes. Výchozí hodnota je 0.

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
Set-AksHciClusterNodeCount –clusterName
                           -controlPlaneNodeCount
```

Chcete-li škálovat pracovní uzly, spusťte následující příkaz.

```powershell
Set-AksHciClusterNodeCount –clusterName
                           -linuxNodeCount
                           -windowsNodeCount
```

Uzel roviny ovládacího prvku a pracovní uzly musí být škálované nezávisle.

## <a name="step-3-upgrade-kubernetes-version"></a>Krok 3: upgrade verze Kubernetes

Pokud chcete zobrazit aktuální verzi Kubernetes, kterou používáte, spusťte následující příkaz.

```powershell
Get-AksHciKubernetesVersion
```

Pokud chcete upgradovat na další verzi Kubernetes, spusťte následující příkaz.

```powershell
Update-AksHciCluster -clusterName
```

Pokud chcete použít uzly Windows, minimální požadovaná verze je v 1.1.8.6.

## <a name="step-4-access-your-clusters-using-kubectl"></a>Krok 4: přístup ke clusterům pomocí kubectl

Pokud chcete získat přístup k hostiteli služby Azure Kubernetes nebo ke clusteru Kubernetes pomocí kubectl, spusťte následující příkaz. Tato akce použije zadaný soubor kubeconfig clusteru jako výchozí soubor kubeconfig pro kubectl.

```powershell
Set-AksHciKubeConfig -clusterName
```

## <a name="delete-a-kubernetes-cluster"></a>Odstranění clusteru Kubernetes

Pokud potřebujete odstranit cluster Kubernetes, spusťte následující příkaz.

```powershell
Remove-AksHciCluster -clusterName
```

## <a name="get-logs"></a>Získání protokolů

Pokud chcete získat protokoly ze všech lusků, spusťte následující příkaz. Tento příkaz vytvoří výstupní složku zip s názvem `akshcilogs` v cestě `C:\wssd\akshcilogs` .

```powershell
Get-AksHciLogs
```

V tomto rychlém startu jste zjistili, jak vytvořit, škálovat a upgradovat cluster Kubernetes pomocí prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

- [Připojte své clustery ke službě Azure ARC pro Kubernetes](./connect-to-arc.md).
- [Nasaďte aplikaci pro Linux do clusteru Kubernetes](./deploy-linux-application.md).
- [Nasaďte aplikaci pro Windows do clusteru Kubernetes](./deploy-windows-application.md).
