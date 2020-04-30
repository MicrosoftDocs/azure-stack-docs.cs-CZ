---
title: Privilegovaný koncový bod Get-AzureStackSupportConfiguration pro centrum Azure Stack
description: Referenční informace o Azure Stack privilegovaného koncového bodu prostředí PowerShell – Get-AzureStackSupportConfiguration
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 710201cc045b356037a54e6f7f0eeee63284b0d6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562550"
---
# <a name="get-azurestacksupportconfiguration"></a>Get-AzureStackSupportConfiguration

## <a name="synopsis"></a>Stručný obsah
Načte nastavení konfigurace služby podpory.

## <a name="syntax"></a>Syntaxe

```
Get-AzureStackSupportConfiguration [-IncludeRegistrationObjectId] [-AsJob]
```

## <a name="description"></a>Popis
Nastavení konfigurace služby podpory.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1
Níže uvedený příklad získá podrobnosti o registraci, pokud bylo razítko zaregistrováno, nebo má jinou hodnotu null.

```
PS  C:\> Get-AzureStackSupportConfiguration
```

## <a name="parameters"></a>Parametry

### <a name="-includeregistrationobjectid"></a>-IncludeRegistrationObjectId
Nepovinný parametr.
Vyžaduje připojení k Internetu.
Načte ID objektu identity registrace.

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

## <a name="notes"></a>Poznámky
Vyžaduje, aby virtuální počítač podporoval připojení k Internetu.

## <a name="next-steps"></a>Další kroky

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).