---
title: Invoke – AzureStackOnDemandLog koncový bod pro centrum Azure Stack
description: Referenční informace pro Azure Stack privilegovaného koncového bodu prostředí PowerShell – vyvolání – AzureStackOnDemandLog
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 3c64b8efd1f5833a9b02c680ac93901bbfb59275
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486797"
---
# <a name="invoke-azurestackondemandlog"></a>Invoke – AzureStackOnDemandLog

## <a name="synopsis"></a>Stručný obsah
Generuje protokoly na vyžádání z Azure Stack rolí centra, kde je to možné.

## <a name="syntax"></a>Syntaxe

```
Invoke-AzureStackOnDemandLog [-OutputPath <Object>] [-SlbTimeOutInMinutes <Object>] [-Generate]
 [-FilterByRole <Object>] [-TimeOutInMinutes <Object>] [-AsJob]
```

## <a name="parameters"></a>Parametry

### <a name="-outputpath"></a>– OutputPath

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

### <a name="-filterbyrole"></a>-FilterByRole
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

### <a name="-timeoutinminutes"></a>-TimeOutInMinutes
```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 30
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-slbtimeoutinminutes"></a>-SlbTimeOutInMinutes

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 20
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-generate"></a>– Generovat

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
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

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](../../operator/azure-stack-privileged-endpoint.md).
