---
title: Uninstall-AksHciAdAuth
author: jessicaguan
description: Příkaz Uninstall-AksHciAdAuth PowerShellu odinstaluje ověřování AD.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 4239d9c268d57315584381e02fa121848395e683
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873639"
---
# <a name="uninstall-akshciadauth"></a>Uninstall-AksHciAdAuth

## <a name="synopsis"></a>Stručný obsah
Odinstalujte ověřování služby Active Directory.

## <a name="syntax"></a>Syntax

```powershell
Uninstall-AksHciAdAuth -name <String>
```

## <a name="description"></a>Popis
Odinstalujte ověřování služby Active Directory.

## <a name="examples"></a>Příklady

### <a name="example"></a>Příklad
```powershell
PS C:\> Uninstall-AksHciAksHciAdAuth -name myCluster
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>– název
Alfanumerický název clusteru Kubernetes.

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
