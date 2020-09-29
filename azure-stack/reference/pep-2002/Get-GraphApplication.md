---
title: Privilegovaný koncový bod Get-GraphApplication pro centrum Azure Stack
description: Referenční informace o Azure Stack privilegovaného koncového bodu prostředí PowerShell – Get-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a100b974a3d622c0b9bb51d850d9a8435d72f549
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487086"
---
# <a name="get-graphapplication"></a>Get-GraphApplication

## <a name="synopsis"></a>Stručný obsah
Get-GraphApplication je Obálková funkce pro získání informací o aplikaci grafu pro zadaný název aplikace nebo identifikátor.

## <a name="syntax"></a>Syntaxe

```
Get-GraphApplication [[-ApplicationIdentifier] <Object>] [-ApplicationName <Object>] [-AsJob]
```

## <a name="description"></a>Popis
Vyvolá příkaz Get-GraphApplicationGroup na počítači ADFS, který získá informace o aplikaci grafu.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1
```
Get-GraphApplication -ApplicationName $ApplicationName
```

## <a name="parameters"></a>Parametry

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
Identifikátor aplikace pro registraci aplikace v AD FS.

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

### <a name="-applicationname"></a>– ApplicationName
Název aplikace zaregistrované ve službě AD FS

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
