---
title: Remove-GraphApplication privilegovaného koncového bodu pro centrum Azure Stack
description: Odkaz na Azure Stack s privilegovaným koncovým bodem PowerShellu – Remove-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b91d363626bc600027b37a1b9c56e69f6c40c3aa
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486386"
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

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](../../operator/azure-stack-privileged-endpoint.md).
