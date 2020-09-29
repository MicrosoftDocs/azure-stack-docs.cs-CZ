---
title: Sada-telemetrie
description: Referenční informace pro Azure Stack privilegovaného koncového bodu prostředí PowerShell – Close-PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ad20bb8ab278e1cb6f46d4fb245ea31b3e00f61a
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742682"
---
# <a name="set-telemetry"></a>Sada-telemetrie

## <a name="synopsis"></a>Stručný obsah
Povolí nebo zakáže přenos dat telemetrie do Microsoftu.

## <a name="syntax"></a>Syntaxe

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>Popis
Rutina Set-telemetrie vám umožní řídit, jestli se data telemetrie odesílají do Microsoftu, a to změnou odpovídajícího nastavení v registru.

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
