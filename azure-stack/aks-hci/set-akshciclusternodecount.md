---
title: Set-AksHciClusterNodeCount
author: jessicaguan
description: Příkaz Set-AksHciClusterNodeCount PowerShell škáluje počet uzlů řídicích rovin nebo pracovních uzlů v clusteru.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 1dff2962ee1997d6eaa696f6e0eeadf7ef2d59e1
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873731"
---
# <a name="set-akshciclusternodecount"></a>Set-AksHciClusterNodeCount

## <a name="synopsis"></a>Stručný obsah
Škálujte počet uzlů řídicí plochy nebo uzlů pracovních uzlů v clusteru.

## <a name="syntax"></a>Syntax

### <a name="scale-control-plane-nodes"></a>Změnit měřítko uzlů roviny ovládacího prvku
```powershell
Set-AksHciClusterNodeCount -name <String>
                           -controlPlaneNodeCount <int> 
```

### <a name="scale-worker-nodes"></a>Škálování pracovních uzlů
```powershell
Set-AksHciClusterNodeCount -name <String>
                           -linuxNodeCount <int>
                           -windowsNodeCount <int>
```

## <a name="description"></a>Popis
Škálujte počet uzlů řídicí plochy nebo uzlů pracovních uzlů v clusteru. Uzel roviny ovládacího prvku a pracovní uzly musí být škálované nezávisle.

## <a name="examples"></a>Příklady

### <a name="scale-control-plane-nodes"></a>Změnit měřítko uzlů roviny ovládacího prvku
```powershell
PS C:\> Set-AksHciClusterNodeCount -name myCluster -controlPlaneNodeCount 3
```

### <a name="scale-worker-nodes"></a>Škálování pracovních uzlů
```powershell
PS C:\> Set-AksHciClusterNodeCount -name myCluster -linuxNodeCount 2 -windowsNodeCount 2
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>– název
Alfanumerický název clusteru Kubernetes.

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

### <a name="-controlplanenodecount"></a>-controlPlaneNodeCount
Počet uzlů v rovině ovládacího prvku. Není k dispozici žádná výchozí hodnota.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: none
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-linuxnodecount"></a>-linuxNodeCount
Počet uzlů Linux v clusteru Kubernetes. Výchozí hodnota je 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
Certifikát použitý k ověření proxy server.
 
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

### <a name="-windowsnodecount"></a>-windowsNodeCount
Počet uzlů Windows v clusteru Kubernetes. Výchozí hodnota je 1.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
