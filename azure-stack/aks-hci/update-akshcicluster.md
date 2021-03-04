---
title: Update-AksHciCluster
author: jessicaguan
description: Příkaz Update-AksHciCluster PowerShell aktualizuje spravovaný cluster Kubernetes na novější verzi Kubernetes nebo operačního systému.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a8fc5c1e87ed78891333af7a3a998767110428ea
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873640"
---
# <a name="update-akshcicluster"></a>Update-AksHciCluster

## <a name="synopsis"></a>Stručný obsah
Aktualizujte spravovaný cluster Kubernetes na novější verzi Kubernetes nebo operačního systému.

## <a name="syntax"></a>Syntax

```powershell
Update-AksHciCluster -name <String>
                    [-kubernetesVersion <String>]
                    [-operatingSystem]
```

## <a name="description"></a>Popis
Aktualizujte spravovaný cluster Kubernetes na novější verzi Kubernetes nebo operačního systému.

## <a name="examples"></a>Příklady

### <a name="example"></a>Příklad
```powershell
PS C:\> Update-AksHciCluster -clusterName myCluster -kubernetesVersion v1.18.8 -operatingSystem
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>– název
Název clusteru Kubernetes.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-kubernetesversion"></a>-kubernetesVersion
Verze Kubernetes, na kterou chcete upgradovat

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-operatingsystem"></a>– operatingSystem
Tento příznak použijte v případě, že chcete aktualizovat na další verzi operačního systému.

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

