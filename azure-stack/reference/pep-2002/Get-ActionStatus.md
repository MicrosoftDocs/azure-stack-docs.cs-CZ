---
title: Koncový bod Get-ActionStatusprivileged pro centrum Azure Stack
description: Referenční informace o Azure Stack privilegovaného koncového bodu prostředí PowerShell – Get-ActionStatus
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f7300224d6b512361c978c33a2f599dfb9a1d852
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562682"
---
# <a name="get-actionstatus"></a>Get-ActionStatus

## <a name="synopsis"></a>Stručný obsah
Získá stav poslední akce pro operaci se zadaným názvem funkce.

## <a name="syntax"></a>Syntaxe

```
Get-ActionStatus [[-ActionType] <Object>] [[-FunctionName] <Object>] [-AsJob]
```

## <a name="description"></a>Popis
Získá stav poslední akce pro operaci se zadaným názvem funkce.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1
```
Get-ActionStatus -FunctionName "Start-SecretRotation"
```

## <a name="parameters"></a>Parametry

### <a name="-functionname"></a>-Function
Název funkce, pro kterou se dotazuje na stav.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-actiontype"></a>– Typ akce
Název typu akce operace

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
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

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).
