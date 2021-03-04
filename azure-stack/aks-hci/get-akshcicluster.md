---
title: Get-AksHciCluster
author: jessicaguan
description: Příkaz Get-AksHciCluster PowerShellu obsahuje seznam spravovaných clusterů Kubernetes včetně hostitele služby Azure Kubernetes.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 14d7f807ef04432e7ea8c3c57dbb4bb80c64731b
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873655"
---
# <a name="get-akshcicluster"></a>Get-AksHciCluster

## <a name="synopsis"></a>Stručný obsah
Vypíše Kubernetes spravované clustery včetně hostitele služby Azure Kubernetes.

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciCluster [-name <String>]
```

## <a name="description"></a>Popis
Vypíše Kubernetes spravované clustery včetně hostitele služby Azure Kubernete.

## <a name="examples"></a>Příklady

### <a name="list-all-kubernetes-clusters"></a>Zobrazit seznam všech clusterů Kubernetes
```powershell
PS C:\> Get-AksHciCluster
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>– název
Název vašeho clusteru.

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
