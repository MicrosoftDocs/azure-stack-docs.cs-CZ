---
title: Test-AzureStack privilegovaného koncového bodu pro centrum Azure Stack
description: Reference k Azure Stack privilegovaného koncového bodu prostředí PowerShell – test-AzureStack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: e2c3b6213d31880b262501eb123d37eab7ec0a48
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563364"
---
# <a name="test-azurestack"></a>Test-AzureStack

## <a name="synopsis"></a>Stručný obsah
Ověří stav centra Azure Stack.

## <a name="syntax"></a>Syntaxe

```
Test-AzureStack [-BackupShareCredential <Object>] [-Include <Object>] [-DetailedResults]
 [-ServiceAdminCredentials <Object>] [-List] [-Group <Object>] [-DoNotDeployTenantVm] [-Repair]
 [-Ignore <Object>] [-Confirm] [-TimeoutSeconds <Object>] [-AdminCredential <Object>]
 [-BackupSharePath <Object>] [-WhatIf] [-UseRunners] [-DisableConsoleOutput] [-AsJob]
```

## <a name="description"></a>Popis
Pokud se nezjistí žádné chyby, vrátí `$true`test-AzureStack.
Pokud se zjistí chyby, vrátí `$false`se.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1

Spuštění rutiny Test-AzureStack bez operátorů a uživatelských scénářů:
```
[azs-ercs01\]: PS\>Test-AzureStack
```

Spuštění rutiny Test-AzureStack s operátorem a uživatelskými scénáři:
```
[azs-ercs01\]: PS\>Test-AzureStack -ServiceAdminCredentials \<PSCredential\>
```

## <a name="parameters"></a>Parametry

### <a name="-serviceadmincredentials"></a>-ServiceAdminCredentials
 

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

### <a name="-donotdeploytenantvm"></a>-DoNotDeployTenantVm
 

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

### <a name="-admincredential"></a>-AdminCredential
 

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

### <a name="-backupsharepath"></a>-BackupSharePath
 

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

### <a name="-backupsharecredential"></a>-BackupShareCredential
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: [PSCredential]::Empty
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-list"></a>– List
 

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

### <a name="-ignore"></a>-Ignore
 

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

### <a name="-include"></a>-Include
 

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

### <a name="-detailedresults"></a>-DetailedResults
 

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

### <a name="-timeoutseconds"></a>-TimeoutSeconds
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1800
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-group"></a>-Group
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Default
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-repair"></a>– Opravit
 

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

### <a name="-userunners"></a>-UseRunners
 

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

### <a name="-disableconsoleoutput"></a>-DisableConsoleOutput
 

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
