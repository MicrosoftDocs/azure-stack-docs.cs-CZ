---
title: Privilegovaný koncový bod Get-AzureStackSupportConfiguration pro centrum Azure Stack
description: Referenční informace o Azure Stack privilegovaného koncového bodu prostředí PowerShell – Get-AzureStackSupportConfiguration
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 97f6bd0fb751885a2a4a4553aba0d7a9329e5458
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487222"
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

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](../../operator/azure-stack-privileged-endpoint.md).
