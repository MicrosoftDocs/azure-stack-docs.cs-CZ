---
title: Privilegovaný koncový bod Get-GraphApplication pro centrum Azure Stack
description: Referenční informace o Azure Stack privilegovaného koncového bodu prostředí PowerShell – Get-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: baccf889139b2a91b23b55ef9bb86387991558da
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562561"
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

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).