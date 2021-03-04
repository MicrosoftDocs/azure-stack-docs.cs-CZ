---
title: Get-AksHciLogs
author: jessicaguan
description: Příkaz Get-AksHciLogs PowerShell vytvoří složku zip s protokoly ze všech lusků.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: ef78efb91a6a8b5c0c91e815cb8aedd0a57adfe8
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873707"
---
# <a name="get-akshcilogs"></a>Get-AksHciLogs

## <a name="synopsis"></a>Stručný obsah
Vytvořte složku zip s protokoly ze všech lusků. 

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciLogs
```

## <a name="description"></a>Popis
Vytvořte složku zip s protokoly ze všech lusků. Tento příkaz vytvoří výstupní složku zip s názvem `akshcilogs.zip` v AKS Azure Stack v pracovním adresáři HCI. Úplná cesta k `akshcilogs.zip` souboru bude výstup po spuštění  `Get-AksHciLogs` (například `C:\AksHci\0.9.6.3\akshcilogs.zip` , kde `0.9.6.3` je Azure Stack AKS číslo verze rozhraní HCI).

## <a name="examples"></a>Příklady

### <a name="example"></a>Příklad
```powershell
PS C:\> Get-AksHciLogs
```
