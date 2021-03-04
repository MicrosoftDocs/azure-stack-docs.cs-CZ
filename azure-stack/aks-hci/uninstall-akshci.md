---
title: Uninstall-AksHci
author: jessicaguan
description: Příkaz Uninstall-AksHci PowerShellu odstraní v Azure Stack HCI rozhraní AKS.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 89cd1bd48518f90dd4ce850e35e42c3106681823
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873744"
---
# <a name="uninstall-akshci"></a>Uninstall-AksHci

## <a name="synopsis"></a>Stručný obsah
Odebere službu Azure Kubernetes na Azure Stack HCI.

## <a name="syntax"></a>Syntax

```powershell
Uninstall-AksHci [-skipConfigCleanup]
```

## <a name="description"></a>Popis
Odebere službu Azure Kubernetes na Azure Stack HCI. 

Pokud se příkazy PowerShellu spouštějí v clusteru, kde se dřív používalo centrum pro správu Windows k nasazení, modul PowerShell zkontroluje existenci konfiguračního souboru centra pro správu systému Windows. Centrum pro správu systému Windows umístí konfigurační soubor centra pro správu systému Windows ve všech uzlech. 

## <a name="example"></a>Příklad

### <a name="example"></a>Příklad
```powershell
PS C:\> Uninstall-AksHci
```

## <a name="parameters"></a>Parametry

### <a name="-skipconfigcleanup"></a>-skipConfigCleanup
Po odinstalaci přeskočí odebrání konfigurací. Pokud použijete tento příznak, stará konfigurace se zachová.

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
