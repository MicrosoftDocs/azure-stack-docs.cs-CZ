---
title: Remove-GraphApplication privilegovaného koncového bodu pro centrum Azure Stack
description: Odkaz na Azure Stack s privilegovaným koncovým bodem PowerShellu – Remove-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 164a20928f312d2ffa4dea73f54c05f41809ebf5
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562869"
---
# <a name="remove-graphapplication"></a>Remove-GraphApplication

## <a name="synopsis"></a>Stručný obsah
Remove-GraphApplication je funkce obálky pro volání rutin AD FS graphu na AD FS.

## <a name="syntax"></a>Syntaxe

```
Remove-GraphApplication [[-ApplicationIdentifier] <Object>] [-AsJob]
```

## <a name="description"></a>Popis
Vyvolá Remove-GraphApplicationGroup na AD FS pro odebrání zadané aplikace na AD FS počítače.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1
```
Remove-GraphApplication -ApplicationIdentifier "Application-Identifier-123456"
```

## <a name="parameters"></a>Parametry

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Identifikátor aplikace, která se má odstranit

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