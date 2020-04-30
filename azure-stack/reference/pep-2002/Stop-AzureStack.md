---
title: AzureStack s privilegovaným koncovým bodem pro centrum Azure Stack
description: Odkaz na Azure Stack s privilegovaným koncovým bodem PowerShellu – stop-AzureStack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 946d31132e4a916574e8fb051e9761f81eb7cdf2
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563419"
---
# <a name="stop-azurestack"></a>Stop – AzureStack

## <a name="synopsis"></a>Stručný obsah
Zastaví všechny služby centra Azure Stack.

## <a name="syntax"></a>Syntaxe

```
Stop-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>Popis
Zastaví všechny Azure Stack služby a zastaví fyzické počítače, na kterých běží centrum Azure Stack.

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