---
title: Set-Telemetry
description: Reference pro Azure Stack s privilegovaným koncovým bodem Set-Telemetry PowerShellu
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 89e9182dd5ebb7a7ab9da6e9d4e7b21da6d4ce9e
ms.sourcegitcommit: 2ac64ac431411b673e655465939d3c95cc94c55d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810918"
---
# <a name="set-telemetry"></a>Set-Telemetry

## <a name="synopsis"></a>Stručný obsah
Povolí nebo zakáže přenos dat telemetrie do Microsoftu.

## <a name="syntax"></a>Syntax

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>Popis
Rutina Set-Telemetry umožňuje řídit, jestli se data telemetrie odesílají do Microsoftu, a to změnou odpovídajícího nastavení v registru.

Konkrétně tato rutina konfiguruje zásady skupiny domény, aby nastavila hodnotu registru telemetrie na 0 a zastavila spuštění služby Windows UTC na všech virtuálních počítačích a hostitelích infrastruktury.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1
```
Set-Telemetry -Enable
```

### <a name="example-2"></a>Příklad 2
```
Set-Telemetry -Disable
```

## <a name="parameters"></a>Parametry

### <a name="-enable"></a>– Povolit
Povoluje přenos dat telemetrie do Microsoftu.

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

### <a name="-disable"></a>– Zakázat
Zakáže přenos dat telemetrie do Microsoftu.

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

## <a name="next-steps"></a>Další kroky

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](../../operator/azure-stack-privileged-endpoint.md).
