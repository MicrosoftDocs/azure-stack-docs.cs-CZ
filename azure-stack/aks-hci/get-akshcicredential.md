---
title: Get-AksHciCredential
author: jessicaguan
description: Příkaz Get-AksHciCredential PowerShellu přistupuje k vašemu clusteru pomocí kubectl.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 50f99f6e5a64a1a0b687c0dfdc42b407b4106500
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873711"
---
# <a name="get-akshcicredential"></a>Get-AksHciCredential

## <a name="synopsis"></a>Stručný obsah
Přístup ke clusteru pomocí `kubectl` . Tato funkce použije zadaný soubor _kubeconfig_ clusteru jako výchozí soubor _kubeconfig_ pro `kubectl` .

## <a name="syntax"></a>Syntax

```powershell
Get-AksHciCredential -name <String>
                    [-outputLocation <String>]
                    [-adAuth]
```

## <a name="description"></a>Popis
Přístup ke clusteru pomocí kubectl.

## <a name="examples"></a>Příklady

### <a name="access-your-cluster-using-kubectl"></a>Přístup ke clusteru pomocí kubectl.
```powershell
PS C:\> Get-AksHciCredential -name myCluster
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>– název
Název clusteru.

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

### <a name="-outputlocation"></a>-outputLocation
Umístění, kam chcete stáhnout kubeconfig. Výchozí je `%USERPROFILE%\.kube`.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %USERPROFILE%\.kube
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adauth"></a>-adAuth
Pomocí tohoto příznaku můžete získat verzi kubeconfig služby Active Directory SSO.

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
