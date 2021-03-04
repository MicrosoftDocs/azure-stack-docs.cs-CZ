---
title: Install-AksHciArcOnboarding
author: jessicaguan
description: Příkaz Install-AksHciArcOnboarding PowerShellu připojí k Azure ARC pro Kubernetes cluster úloh HCI do Azure Stack AKS.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a366e553ac76882a7f45a9f25424cabbb4402319
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873683"
---
# <a name="install-akshciarconboarding"></a>Install-AksHciArcOnboarding

## <a name="synopsis"></a>Stručný obsah
Připojí AKS Azure Stack cluster úloh HCI do Azure ARC pro Kubernetes.

## <a name="syntax"></a>Syntax

```powershell
Install-AksHciArcOnboarding -Name <String> 
                            -tenantId <String>
                            -subscriptionId <String> 
                            -resourceGroup <String>
                            -clientId <String>
                            -clientSecret <String>
                            [-location <String>]
```

## <a name="description"></a>Popis
Připojí AKS Azure Stack cluster úloh HCI do Azure ARC pro Kubernetes.

## <a name="examples"></a>Příklady

### <a name="connect-an-aks-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes-with-required-parameters"></a>Připojení AKS v Azure Stack clusteru HCI ke službě Azure ARC pro Kubernetes s požadovanými parametry

```PowerShell
Install-AksHciArcOnboarding -name "myCluster" -resourcegroup "myResourceGroup" -subscriptionid "57ac26cf-a9f0-4908-b300-9a4e9a0fb205"  -clientid "22cc2695-54b9-49c1-9a73-2269592103d8" -clientsecret "09d3a928-b223-4dfe-80e8-fed13baa3b3d" -tenantid "72f988bf-86f1-41af-91ab-2d7cd011db47"
```

### <a name="connect-an-aks-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes-with-all-parameters"></a>Připojení AKS na Azure Stack clusteru HCI ke službě Azure ARC pro Kubernetes se všemi parametry

```PowerShell
Install-AksHciArcOnboarding -name "myCluster" -resourcegroup "myResourceGroup" -location "eastus" -subscriptionid "57ac26cf-a9f0-4908-b300-9a4e9a0fb205"  -clientid "22cc2695-54b9-49c1-9a73-2269592103d8" -clientsecret "09d3a928-b223-4dfe-80e8-fed13baa3b3d" -tenantid "72f988bf-86f1-41af-91ab-2d7cd011db47"
```

## <a name="parameters"></a>Parametry

### <a name="-name"></a>-Name
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

### <a name="-tenantid"></a>– tenantId
ID tenanta objektu služby Azure.

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

### <a name="-subscriptionid"></a>-subscriptionId
ID předplatného účtu Azure.

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

### <a name="-resourcegroup"></a>-resourceName
Název skupiny prostředků Azure

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

### <a name="-clientid"></a>– clientId
ID klienta nebo ID aplikace vašeho instančního objektu Azure

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

### <a name="-clientsecret"></a>– clientSecret
Tajný klíč nebo heslo klienta k instančnímu objektu Azure

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

### <a name="-location"></a>– umístění
Umístění nebo oblast Azure prostředku Azure. Výchozí hodnota je umístění vaší skupiny prostředků Azure. Umístění může být pouze eastus nebo westeurope pro Kubernetes ARC pro.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Azure resource group's location
Accept pipeline input: False
Accept wildcard characters: False
```
