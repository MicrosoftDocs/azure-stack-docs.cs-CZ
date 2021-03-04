---
title: New-AksHciCluster
author: jessicaguan
description: Příkaz New-AksHciCluster PowerShell vytvoří nový spravovaný cluster Kubernetes.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d4b374ca093c60018d1dc68c3d5a39db68456298
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873679"
---
# <a name="new-akshcicluster"></a>New-AksHciCluster

## <a name="synopsis"></a>Stručný obsah
Vytvořte nový spravovaný cluster Kubernetes.

## <a name="syntax"></a>Syntax

```powershell
New-AksHciCluster -name <String>
                 [-kubernetesVersion <String>]
                 [-controlPlaneNodeCount <int>]
                 [-linuxNodeCount <int>]
                 [-windowsNodeCount <int>]
                 [-controlPlaneVmSize <VmSize>]
                 [-loadBalancerVmSize <VmSize>]
                 [-linuxNodeVmSize <VmSize>]
                 [-windowsNodeVmSize <VmSize>]
                 [-vnet <Virtual Network>]
                 [-primaryNetworkPlugin <Network Plugin>]   
                 [-enableAdAuth]
                 [-enableSecretsEncryption]          
```

## <a name="description"></a>Popis

Vytvořte novou službu Azure Kubernetes v clusteru Azure Stack HCI.

## <a name="examples"></a>Příklady

### <a name="new-aks-hci-cluster-with-required-params"></a>Nový cluster AKS-HCI s požadovanými parametry.

```powershell
PS C:\> New-AksHciCluster -name myCluster
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>– název
Název clusteru Kubernetes.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-kubernetesversion"></a>-kubernetesVersion
Verze Kubernetes, kterou chcete nasadit. Výchozí hodnota je nejnovější verze. Pokud chcete získat seznam dostupných verzí, spusťte příkaz `Get-AksHciKubernetesVersion` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: v1.18.8 or later
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
Počet uzlů v rovině ovládacího prvku. Výchozí hodnota je 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
Počet uzlů Linux v clusteru Kubernetes. Výchozí hodnota je 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases: 

Required: False
Position: Named
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsnodecount"></a>-windowsNodeCount
Počet uzlů Windows v clusteru Kubernetes. Výchozí hodnota je 0. Uzly systému Windows můžete nasadit pouze v případě, že používáte Kubernetes v 1.18.8 nebo vyšší.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
Velikost virtuálního počítače roviny ovládacího prvku. Výchozí hodnota je Standard_A4_V2. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-loadbalancervmsize"></a>-loadBalancerVmSize
Velikost virtuálního počítače nástroje pro vyrovnávání zatížení. Výchozí hodnota je Standard_A4_V2. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodevmsize"></a>-linuxNodeVmSize
Velikost virtuálního počítače uzlu se systémem Linux. Výchozí hodnota je Standard_K8S3_v1. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-windowsvmsize"></a>-windowsVmSize
Velikost virtuálního počítače uzlu Windows Výchozí hodnota je Standard_K8S3_v1. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_K8S3_v1
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>– VNet
Název objektu AksHciNetworkSetting vytvořeného příkazem New-AksHciNetworkSetting.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-primarynetworkplugin"></a>-primaryNetworkPlugin
Síťový modul plug-in, který se má použít pro vaše nasazení. Tento parametr přebírá buď `flannel` nebo `calico` . Calico je k dispozici pouze pro clustery se systémem Linux. Pokud pro cluster zvolíte Calico, přidání uzlů Windows se nepovoluje.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: flannel
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enableadauth"></a>-enableADAuth
Pomocí tohoto příznaku povolte službu Active Directory v clusteru Kubernetes.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablesecretsencryption"></a>-enableSecretsEncryption
Pomocí tohoto příznaku povolíte šifrování ve Kubernetes tajných klíčích.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
