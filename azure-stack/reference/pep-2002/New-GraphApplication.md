---
title: New-GraphApplication privilegovaný koncový bod pro centrum Azure Stack
description: Reference pro Azure Stack s privilegovaným koncovým bodem PowerShellu – New-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b0a9aeb2e7ec1035f24d89409f5fa23ef13500fb
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486610"
---
# <a name="new-graphapplication"></a>New-GraphApplication

## <a name="synopsis"></a>Stručný obsah
New-GraphApplication je funkce obálky pro volání rutin AD FS graphu na AD FS.

## <a name="syntax"></a>Syntaxe

```
New-GraphApplication [-ClientCertificates <Object>] [-Name <Object>] [-ClientRedirectUris <Object>]
 [-GenerateClientSecret] [-AsJob]
```

## <a name="description"></a>Popis
Vyvolá příkaz New-GraphApplicationGroup na AD FS, aby se přidala nová aplikace do AD FS počítače.

## <a name="examples"></a>Příklady

### <a name="example-1"></a>Příklad 1
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>-Name
Název aplikace s maximální délkou 50 znaků, bude změněn jako `Azurestack-$Name-$({guid}::{NewGuid}())` a je vrácen funkcí.

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

### <a name="-clientredirecturis"></a>-ClientRedirectUris
Identifikátor URI přesměrování použitý k vytvoření nové aplikace v AD FS.

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

### <a name="-clientcertificates"></a>– Vlastnost ClientCertificates
Certifikát použitý k vytvoření nové aplikace v AD FS.

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

### <a name="-generateclientsecret"></a>-GenerateClientSecret
 

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
