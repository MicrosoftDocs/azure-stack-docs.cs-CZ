---
title: Restart-AksHci
author: jessicaguan
description: Příkaz Restart-AksHci PowerShellu se restartuje AKS na Azure Stack HCL a odstraní všechny nasazené clustery Kubernetes.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 358ca36b085b21c6ecdbea7a024d9e72e0ca0597
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873732"
---
# <a name="restart-akshci"></a>Restart-AksHci

## <a name="synopsis"></a>Stručný obsah
Restartujte službu Azure Kubernetes na Azure Stack HCI a odeberte všechny nasazené clustery Kubernetes.

## <a name="syntax"></a>Syntax

### <a name="restart-aks-hci"></a>Restartovat AKS-HCI
```powershell
Restart-AksHci
```

## <a name="description"></a>Popis
Po restartování služby Azure Kubernetes v systému Azure Stack HCL dojde k odebrání všech clusterů Kubernetes a hostitele služby Azure Kubernetes. Odinstaluje taky službu Azure Kubernetes na Azure Stack agenty a služby HCI v uzlech. Pak se vrátí zpět kroky původní instalace, dokud se hostitel znovu nevytvoří. Služba Azure Kubernetes na Azure Stack konfiguraci HCI, kterou jste nakonfigurovali přes Set-AksHciConfig, a stažené image VHDX se zachovají.

## <a name="examples"></a>Příklady

### <a name="example"></a>Příklad
```powershell
PS C:\> Restart-AksHci
```
