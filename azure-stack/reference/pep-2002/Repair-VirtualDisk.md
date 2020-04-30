---
title: Oprava-VirtualDisk privilegovaného koncového bodu pro centrum Azure Stack
description: Referenční informace o prostředí PowerShell Azure Stack privilegovaným koncovým bodem – Repair-VirtualDisk
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ef7fa22c00b11f93ff39e0ce03fa91383c2cffc7
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562913"
---
# <a name="repair-virtualdisk"></a>Opravit – VirtualDisk

## <a name="syntax"></a>Syntaxe

```
Repair-VirtualDisk [-CimSession <Object>] [-WhatIf] [-PassThru] [-Name <Object>] [-InputObject <Object>]
 [-Confirm] [-UniqueId <Object>] [-ThrottleLimit <Object>] [-AsJob] [[-FriendlyName] <Object>]
```
```

## Parameters

### -AsJob


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

### <a name="-cimsession"></a>– CimSession
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases: Session

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-confirm"></a>-Confirm
Před spuštěním rutiny zobrazí výzvu k potvrzení.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-friendlyname"></a>-FriendlyName
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-inputobject"></a>– Položky InputObject
 

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

### <a name="-name"></a>-Name
 

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

### <a name="-passthru"></a>– PassThru
 

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

### <a name="-throttlelimit"></a>-ThrottleLimit
 

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

### <a name="-uniqueid"></a>-UniqueId
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases: Id

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-whatif"></a>-WhatIf
Zobrazuje, co by se stalo při spuštění rutiny.
Rutina není spuštěna.

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="inputs"></a>Vstupy

### <a name="systemstring"></a>System.String[]

### <a name="microsoftmanagementinfrastructureciminstance"></a>Microsoft. Management. Infrastructure. CimInstance []

## <a name="outputs"></a>Výstupy

### <a name="microsoftmanagementinfrastructureciminstance"></a>Microsoft. Management. Infrastructure. CimInstance

### <a name="microsoftmanagementinfrastructureciminstancerootmicrosoftwindowsstoragemsft_virtualdisk"></a>Microsoft. Management. Infrastructure. CimInstance # ROOT/Microsoft/Windows/Storage/MSFT_VirtualDisk


## <a name="next-steps"></a>Další kroky

Informace o přístupu a používání privilegovaného koncového bodu najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).