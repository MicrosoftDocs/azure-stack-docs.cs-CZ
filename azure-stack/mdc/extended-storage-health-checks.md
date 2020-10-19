---
title: Azure Stack – kontroly stavu v rozšířeném úložišti pro modulární úložiště objektů BLOB datového centra
description: Tento článek poskytuje pokyny, jak provádět kontroly stavu v rozšířeném úložišti pro modulární úložiště objektů BLOB datového centra.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/01/2020
ms.openlocfilehash: 9d8e7915cb5c1ca9d0a073c750f67a5688157e74
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182956"
---
# <a name="extended-storage-health-checks"></a>Rozšířené kontroly stavu úložiště

Tento článek poskytuje pokyny pro kontrolu stavu hardwaru rozšířeného úložiště Datacenter a nasazení centra Azure Stack.

Než začnete, zkontrolujte a postupujte podle kroků v části [aktualizace firmwaru pro rozšířené úložiště](extended-storage-firmware-updates.md).

Poslední krok před odesláním systému se vypíná. Postupujte podle kroků v Úvodní příručka.

## <a name="extended-storage-health"></a>Rozšířený stav úložiště

V této části najdete pokyny k ověření stavu rozšířeného hardwaru úložiště.
Zkontroluje události a upozorní správce, jestli existují problémy, které je potřeba vyřešit. 


Pokud chcete ověřit stav fondu úložiště clusteru, spusťte následující příkaz:
```console
isi storagepool health
```

Například pokud je v pořádku, výsledky příkazu vypadají takto:
```console
All pools are healthy.
Unprovisioned drives: none
```

V případě, že nejsou v pořádku nebo se nejedná o jednotky, bude výsledný příkaz vypadat takto:

```console
SmartPools Health
Name                  Health  Type Prot   Members          Down          Smartfailed
--------------------- ------- ---- ------ ---------------- ------------- -------------
a2000_200tb_800gb-    -M---
ssd-sed_16gb
 a2000_200tb_800gb-   -M---   HDD  3x     3,11,14,19,23,30 Nodes:        Nodes:
ssd-sed_16gb:24                           ,38,41,46:bay6,1 Drives:       Drives:
                                          0-11,16,19,
                                          25:bay6,10-11,16

OK = Ok, U = Too few nodes, M = Missing drives,
D = Some nodes or drives are down, S = Some nodes or drives are smartfailed,
R = Some nodes or drives need repair
Unprovisioned drives: none
```

Pokud chcete získat nápovědu k problémům, obraťte se na podpora Microsoftu.

Následující příkaz zkontroluje celkový stav clusteru ve zjednodušeném zobrazení:
```console
isi status
```

Pokud je vše v pořádku, zobrazí se zelená ikona **OK** , jinak se žlutá upozornění nebo červené chyby zobrazí buď na řádku **stavu clusteru** , nebo na jednom nebo několika řádcích ID uzlu clusteru v tabulce, která následuje.

Pokud potřebujete další informace o stavu clusteru, můžete spustit podrobnou příkaz v rozšířeném zobrazení:
```console
isi status -a
```

Pokud chcete získat nápovědu k problémům, obraťte se na podpora Microsoftu.

## <a name="azure-stack-hub-health"></a>Stav centra Azure Stack

V této části najdete pokyny pro kontrolu stavu nasazení centra Azure Stack.

Pokud chcete získat celkový přehled o nasazení Azure Stack integrováno s rozšířeným úložným systémem, spusťte následující skript, který je obálkou pro následující:
- Připojení k virtuálnímu počítači s ERCS pomocí zadaných přihlašovacích údajů
- Provedení příkazu Test-AzureStack-Debug (který vypíše podrobné informace o stavu přímo jako výstup na obrazovce)

Požadavky pro tento skript jsou následující:
- .\Invoke-ExtendedStorageConfiguration.ps1 soubor skriptu, který se nachází ve složce C:\OEMSoftware\ExtendedStorage\ hostitele životního cyklu hardwaru (HLH)
- $AzScred proměnných přihlašovacích údajů, které by se měly naplnit přihlašovacími údaji *domény*\cloudadmin Nahraďte *doménu* skutečným názvem domény, například contoso.


```powershell
$AzScred = Get-Credential -Credential 'DOMAIN\cloudadmin'
.\Invoke-ExtendedStorageConfiguration.ps1 -TestAzureStack -AzureStackCred $AzScred
```

Zkontrolujte výstup a ověřte celkový stav stavu nasazení Azure Stack a také oddíly, které **jsou specifické pro konkrétní stav** integrace rozšířeného úložiště s Azure Stack.

Podrobné informace o Azure Stack diagnostice najdete v tématu [ověření stavu systému služby Azure Stack hub](../operator/azure-stack-diagnostic-test.md).

## <a name="technical-support"></a>Technická podpora

Pokud chcete získat nápovědu k problémům, obraťte se na podpora Microsoftu.

## <a name="next-steps"></a>Další kroky

- [Aktualizovat firmware](extended-storage-firmware-updates.md)
