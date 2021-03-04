---
title: Remove-AksHciCluster
description: Příkaz Remove-AksHciCluster PowerShell odstraní spravovaný cluster Kubernetes.
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 868aa8a739c69ad6e29d73192f91c9bda73a3195
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873736"
---
# <a name="remove-akshcicluster"></a>Remove-AksHciCluster

## <a name="synopsis"></a>Stručný obsah
Odstraní spravovaný cluster Kubernetes.

## <a name="syntax"></a>Syntax

### <a name="delete-a-managed-kubernetes-cluster"></a>Odstranění spravovaného clusteru Kubernetes
```powershell
Remove-AksHciCluster -name 
                    [-force]   
```

## <a name="description"></a>Popis
Odstraní spravovaný cluster Kubernetes.

## <a name="examples"></a>Příklady

### <a name="delete-an-existing-managed-kubernetes-cluster"></a>Odstranění existujícího spravovaného clusteru Kubernetes
```powershell
PS C:\> Remove-AksHciCluster -name myCluster
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
