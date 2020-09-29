---
title: Privilegovaný koncový bod Get-ClusterLog pro centrum Azure Stack
description: Referenční informace o Azure Stack privilegovaného koncového bodu prostředí PowerShell – Get-ClusterLog
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ec984252235334b5706a79a510852868c8f40dba
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487103"
---
# <a name="get-clusterlog"></a>Get-ClusterLog

## <a name="syntax"></a>Syntaxe

```
Get-ClusterLog [-SkipClusterState] [-InputObject <Object>] [-Cluster <Object>] [-NetworkDiagnostics]
 [-Destination <Object>] [-NetworkDiagnosticsLevel <Object>] [[-Node] <Object>] [-TimeSpan <Object>]
 [-PerformanceHistoryTimeFrame <Object>] [-Health] [-ExportClusterPerformanceHistory] [-UseLocalTime] [-AsJob]
```

## <a name="parameters"></a>Parametry

### <a name="-cluster"></a>– Cluster
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-destination"></a>– Cíl
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-exportclusterperformancehistory"></a>-ExportClusterPerformanceHistory
Exportujte data ClusterPorformanceHistory.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-health"></a>-Health
Vygenerujte protokoly stavu clusteru.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-inputobject"></a>– Položky InputObject
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-networkdiagnostics"></a>-NetworkDiagnostics
Vygenerujte protokoly diagnostiky sítě clusteru.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-networkdiagnosticslevel"></a>-NetworkDiagnosticsLevel
Určuje úroveň hloubky protokolů diagnostiky sítě.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-node"></a>– Node
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-performancehistorytimeframe"></a>-PerformanceHistoryTimeFrame
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipclusterstate"></a>-SkipClusterState
Vygenerujte protokol clusteru bez načítání informací o stavu clusteru.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: scs

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timespan"></a>– TimeSpan
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases: Span

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-uselocaltime"></a>-UseLocalTime
Vygenerujte protokol clusteru pomocí místního času namísto GMT.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: lt

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>– AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="next-steps"></a>Další kroky

[https://go.microsoft.com/fwlink/?LinkId=216212](https://go.microsoft.com/fwlink/?LinkId=216212)

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](../../operator/azure-stack-privileged-endpoint.md).
