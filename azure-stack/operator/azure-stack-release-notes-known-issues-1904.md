---
title: Známé problémy s Azure Stack 1904 | Dokumentace Microsoftu
description: Přečtěte si o známých problémech v Azure stacku 1904.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 4438fdb5b97a4e08632b9c4f55fc03c91ef3aa97
ms.sourcegitcommit: 405075e7826ba2fa545dbf5498160219ef8d2db5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65453718"
---
# <a name="azure-stack-1904-known-issues"></a>Azure Stack 1904 známé problémy

V tomto článku najdete seznam známých problémů v 1904 verzi služby Azure Stack. V seznamu je aktualizovat, protože nové problémy jsou označeny.

> [!IMPORTANT]  
> V této části najdete před instalací této aktualizace.

## <a name="portal"></a>Portál

### <a name="add-on-plans"></a>Doplňkové plány

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Plány, které jsou přidány na předplatné uživatele jako doplňkový plán nelze odstranit, i když odebrat plán ze předplatné uživatele. Plán zůstane, dokud se také odstraní předplatné, které odkazují na doplňkový plán.
- Náprava: Žádné zmírňující opatření.
- Výskyt: Obecné

### <a name="administrative-subscriptions"></a>Pro správu předplatných

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Obě předplatná pro správu, které byly představeny s nástrojem verzi 1804 se nesmí používat. Typy předplatného jsou **měření** předplatného, a **spotřeby** předplatného.
- Náprava: Tato předplatná se pozastaví, počínaje 1905 a nakonec odstraněné. Pokud máte prostředky spuštěné na tyto dva odběry služeb, je znovu vytvořte v předplatných uživatele před 1905.
- Výskyt: Obecné

### <a name="subscription-resources"></a>Prostředky předplatného

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Odstraňuje se předplatné uživatele za následek osamocené prostředky.
- Náprava: Nejprve odstranit prostředky uživatele nebo celou skupinu prostředků a pak odstraňte předplatná uživatelů.
- Výskyt: Obecné

### <a name="subscription-permissions"></a>Oprávnění pro předplatné

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nelze zobrazit oprávnění k předplatnému pomocí na portálech Azure Stack.
- Náprava: Použití [Powershellu mohl ověřit oprávnění](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Výskyt: Obecné

### <a name="marketplace-management"></a>Správa webu Marketplace

- Použít: Tento problém se týká 1904.
- Příčina: Na obrazovce správy marketplace není viditelný, při přihlášení k portálu správce.
- Náprava: Aktualizujte prohlížeč.
- Výskyt: Občasně

### <a name="upload-blob"></a>Nahrát objekt blob

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal při pokusu o nahrání objektu blob pomocí možnosti OAuth(preview), úloha se nezdaří s chybovou zprávu.
- Náprava: Nahrajte objekt blob pomocí možnosti SAS.
- Výskyt: Obecné

## <a name="networking"></a>Sítě

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Přidat back-endový fond

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, pokud se pokusíte přidat **back-endový fond** k **nástroje pro vyrovnávání zatížení**, operace se nezdaří s chybovou zprávou **nepovedlo se aktualizovat nástroj pro vyrovnávání zatížení...** .
- Náprava: Pomocí prostředí PowerShell, CLI nebo šablony Resource Manageru pro přidružení k prostředku nástroje pro vyrovnávání zatížení back-endový fond.
- Výskyt: Obecné

#### <a name="create-inbound-nat"></a>Vytvoření příchozích pravidel NAT

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Na portálu user portal, pokud se pokusíte vytvořit **příchozí pravidlo NAT** pro **nástroje pro vyrovnávání zatížení**, operace se nezdaří s chybovou zprávou **nepovedlo se aktualizovat nástroj pro vyrovnávání zatížení...** .
- Náprava: Pomocí prostředí PowerShell, CLI nebo šablony Resource Manageru pro přidružení k prostředku nástroje pro vyrovnávání zatížení back-endový fond.
- Výskyt: Obecné

#### <a name="create-load-balancer"></a>Vytvoření nástroje pro vyrovnávání zatížení

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **vytvořit nástroj pro vyrovnávání zatížení** okno zobrazuje možnost pro vytvoření **standardní** SKU nástroje pro vyrovnávání zatížení. Tato možnost není podporována ve službě Azure Stack.
- Náprava: Místo toho použijte možnosti nástroje pro vyrovnávání zatížení základní.
- Výskyt: Obecné

#### <a name="public-ip-address"></a>Veřejná IP adresa

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: V portálu user portal **vytvořit veřejnou IP adresu** okno zobrazuje možnost pro vytvoření **standardní** SKU. **Standardní** SKU se nepodporuje v Azure stacku.
- Náprava: Místo toho použijte základní SKU pro veřejnou IP adresu.
- Výskyt: Obecné

## <a name="compute"></a>Compute

### <a name="vm-boot-diagnostics"></a>Diagnostika spouštění virtuálních počítačů

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Při vytváření virtuálního počítače pro nové Windows (VM), může se zobrazit následující chyba: **Nepovedlo se spustit virtuální počítač 'název_virtuálního_počítače'. Chyba: Nepovedlo se aktualizovat nastavení sériového portu výstup pro virtuální počítač 'název_virtuálního_počítače'**.
Pokud jste povolení diagnostiky spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění dojde k chybě.
- Náprava: Znovu vytvořte účet úložiště se stejným názvem, který jste použili dříve.
- Výskyt: Obecné

### <a name="virtual-machine-scale-set"></a>Škálovací sada virtuálních počítačů

#### <a name="centos"></a>CentOS

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Prostředí pro vytváření nastavit škálování virtuálního počítače (VMSS) poskytuje založené na CentOS 7.2 jako možnost pro nasazení. CentOS 7.2 není k dispozici ve službě Azure Stack.
- Náprava: Vyberte jiný operační systém pro vaše nasazení, nebo pomocí šablony Azure Resource Manageru zadáním jiné image CentOS, který byl stažen před jejich nasazením na Marketplace operátorem.
- Výskyt: Obecné

#### <a name="remove-scale-set"></a>Odstranit škálovací sady

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Nelze odebrat škálovací sady z **Virtual Machine Scale Sets** okno.
- Náprava: Vyberte škálovací sady, že chcete odebrat, klikněte **odstranit** tlačítko **přehled** podokně.
- Výskyt: Obecné

### <a name="ubuntu-ssh-access"></a>Přístup SSH se systémem Ubuntu

- Použít: Tento problém se vztahuje na všechny podporované verze.
- Příčina: Virtuální počítač s Ubuntu 18.04 vytvořené pomocí SSH autorizace povolená neumožňuje použití klíčů SSH pro přihlášení.
- Náprava: Přístup k virtuálním počítačům pro rozšíření Linuxu použít k implementaci klíče SSH po zřízení, nebo použít ověřování pomocí hesla.
- Výskyt: Obecné

## <a name="infrastructure-backup"></a>Infrastruktura zálohování

<!--Bug 3615401 - scheduler config lost; new issue in YYMM;  hectorl-->
Jakmile povolíte automatické zálohování, služba Plánovač přejde do stavu zakázáno neočekávaně. Služba zálohování řadiče zjistí, že jsou zakázány automatické zálohování a vyvolat upozornění na portálu správce. Když jsou zakázány automatické zálohování, očekává se toto upozornění.

- Použít: Jedná se o nový problém s verzí 1904.
- Příčina: Tento problém je z důvodu chyby ve službě, která vede ke ztrátě konfiguraci plánovače. Tato chyba se nezmění, umístění úložiště, uživatelské jméno, heslo nebo šifrovací klíč.
- Náprava: Chcete-li tento problém zmírnit, otevře se okno nastavení zálohování řadiče v poskytovateli prostředků infrastruktury zálohování a vyberte **povolit automatické zálohování**. Nezapomeňte nastavit požadovanou četnost a uchování období.
- Výskyt: Nízký

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Další postup

- [Kontrolní seznam pro revizi aktualizace aktivity](azure-stack-release-notes-checklist.md)
- [Zkontrolujte seznam aktualizací zabezpečení](azure-stack-release-notes-security-updates-1904.md)
