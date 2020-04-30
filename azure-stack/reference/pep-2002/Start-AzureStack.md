---
title: Spustit – AzureStack privilegovaný koncový bod pro centrum Azure Stack
description: Odkaz na Azure Stack s privilegovaným koncovým bodem prostředí PowerShell – Start-AzureStack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 70b0992239fc2f1f5cc5bb4b7ffb4b8615c94c0d
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563408"
---
# <a name="start-azurestack"></a>Spustit – AzureStack

## <a name="synopsis"></a>Stručný obsah
Spustí všechny služby centra Azure Stack.

## <a name="syntax"></a>Syntaxe

```
Start-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Popis
Spustí všechny služby centra Azure Stack.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1
```powershell
PS C:\> 
```



## <a name="parameters"></a>Parametry

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
Maximální doba, po jejímž uplynutí bude spuštění zastaveno.

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 2400
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