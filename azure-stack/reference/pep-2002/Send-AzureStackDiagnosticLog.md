---
title: AzureStackDiagnosticLogý koncový bod s privilegovaným odesláním pro centrum Azure Stack
description: Referenční informace o Azure Stack privilegovaného koncového bodu v PowerShellu – Send-AzureStackDiagnosticLog
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f0e7bf2ebd7c3d5ef238a1e0e946a482c1e8d4a6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562803"
---
# <a name="send-azurestackdiagnosticlog"></a>Odeslat – AzureStackDiagnosticLog

## <a name="synopsis"></a>Stručný obsah
Odesílá diagnostické protokoly centra Azure Stack společnosti Microsoft.

## <a name="syntax"></a>Syntaxe

```
Send-AzureStackDiagnosticLog [[-FromDate] <Object>] [[-FilterByResourceProvider] <Object>]
 [[-FilterByRole] <Object>] [[-ToDate] <Object>] [-AsJob]
```

## <a name="description"></a>Popis
Vytvoří požadavek na podporu služby mostu pro nahrání diagnostických protokolů pro razítko.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1

Následující příklad odesílá poslední 4 hodiny do společnosti Microsoft.

```
PS  C:\> Send-AzureStackDiagnosticLog
```

### <a name="example-2"></a>Příklad 2
Níže uvedený příklad pošle poslední 4 hodiny službu podpory a WAS do Microsoftu protokoly.
```
PS  C:\> Send-AzureStackDiagnosticLog -FilterByRole SupportBridgeController,WAS
```

## <a name="parameters"></a>Parametry

### <a name="-filterbyrole"></a>-FilterByRole
Nepovinný parametr.
Filtrovat podle typu role infrastruktury

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

### <a name="-filterbyresourceprovider"></a>-FilterByResourceProvider
Nepovinný parametr.
Filtrovat podle hodnoty – přidat typ poskytovatele prostředků.

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

### <a name="-fromdate"></a>– FromDate
Nepovinný parametr.
Čas spuštění, který se má použít pro shromáždění protokolů

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-todate"></a>-Fulltextový
Nepovinný parametr.
Čas ukončení, který se má použít pro shromáždění protokolů

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
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