---
title: Chránit virtuální počítače nasazené v Azure Stack | Microsoft Docs
description: Naučte se, jak vytvořit plán obnovení pro ochranu virtuálních počítačů nasazených v Azure Stack před ztrátou dat a neplánovanými výpadky.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: 31e574a24660367cd1189ad95890e2fffaaa15f2
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939675"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Ochrana virtuálních počítačů nasazených v centru Azure Stack

Tento článek slouží jako vodítko pro vývoj plánu ochrany virtuálních počítačů, které uživatelé nasazují v Azure Stack hub.

Pro zajištění ochrany před únikem informací a neplánovanými výpadky implementujte plán ochrany dat a zotavení po havárii pro aplikace založené na virtuálních počítačích v centru Azure Stack. Implementovaný plán ochrany bude záviset na požadavcích firmy a návrhu aplikace. Tento plán by měl postupovat podle rozhraní, které vaše organizace zavedla v \' komplexní strategii pro provozní kontinuitu a zotavení po havárii (BC/Dr). Základní informace o požadavcích BC/DR pro centrum Azure Stack najdete v článku Azure Stack: požadavky na provozní kontinuitu a zotavení po havárii.

## <a name="application-recovery-objectives"></a>Cíle obnovení aplikace

Určete množství výpadku a ztráty dat, které vaše organizace může tolerovat pro každou aplikaci. Díky kvantitativnímu výpadku a ztrátě dat můžete vytvořit plán obnovení, který minimalizuje dopad havárie ve vaší organizaci. Pro každou aplikaci zvažte následující:

- **Cíl doby obnovení (RTO)**\
    RTO je maximální přijatelná doba, po kterou může být aplikace po incidentu nedostupná. Například RTO 90 minut znamená, že musíte být schopni obnovit aplikaci do běžícího stavu během 90 minut od začátku havárie. Pokud máte malou RTO, můžete zachovat druhé nasazení nepřetržitě běžící na pohotovostním režimu pro ochranu před oblastním výpadkem.

- **Cíl bodu obnovení (RPO)**\
    Cíl bodu obnovení (RPO) je maximální období ztráty dat, které je při havárii přijatelné. Pokud například ukládáte data do jediné databáze, která se zálohuje každou hodinu a nemá žádnou replikaci do jiných databází, můžete přijít o hodinu dat.

Proveďte posouzení pro definování RTO a RPO pro každou aplikaci.

Další důležitou metrikou, kterou je třeba zvážit, je **Střední doba obnovení** (MTTR), což je průměrná doba potřebná k obnovení aplikace po selhání. MTTR je empirická hodnota pro systém. Pokud MTTR překročí RTO, pak selhání v systému způsobí nepřijatelné výpadky v podniku, protože bylo výhra, že je \' možné obnovit systém v rámci definovaného RTO.

## <a name="protection-options-for-iaas-vms"></a>Možnosti ochrany pro virtuální počítače s IaaS

### <a name="backup-restore"></a>Zálohování – obnovení

Nejběžnějším schématem ochrany pro aplikace založené na virtuálním počítači je použití zálohovacího softwaru. Zálohování virtuálního počítače obvykle zahrnuje operační systém, konfiguraci operačního systému, binární soubory aplikace a trvalá data aplikací obsažená ve virtuálním počítači. Zálohy se vytvářejí pomocí agenta v hostovaném operačním systému pro zachycení aplikace, operačního systému nebo systému souborů/svazků. Další možností je bez agenta tím, že se spoléhá na integraci s rozhraními API centra Azure Stack ke čtení informací o konfiguraci virtuálních počítačů a snímku disků připojených k virtuálnímu počítači. Uvědomte si prosím, že centrum Azure Stack nepodporuje zálohování přímo z hypervisoru.

### <a name="planning-your-backup-strategy"></a>Plánování strategie zálohování

Plánování strategie zálohování a definování požadavků na škálování začíná vystanovením počtu instancí virtuálních počítačů, které je potřeba chránit. Zálohování všech virtuálních počítačů v systému nemusí být nejúčinnějším způsobem ochrany aplikace. U Azure Stackového centra by se virtuální počítače v sadě škálování nebo skupině dostupnosti neměly zálohovat na úrovni virtuálního počítače. Tyto virtuální počítače se považují za dočasné, protože sadu virtuálních počítačů je možné škálovat nebo oddálit. V ideálním případě se všechna data, která je třeba zachovat, nachází v samostatném úložišti, jako je databáze nebo úložiště objektů. Pokud aplikace nasazené v architektuře se škálováním na více instancí obsahují data, která musí být trvalá a chráněná, pak bude vyžadovat zálohování na úrovni aplikace pomocí nativních schopností poskytovaných aplikací nebo spoléhání na agenta.

Důležité informace pro zálohování virtuálních počítačů na Azure Stack:

- **Kategorizace**
  - Vezměte v úvahu model, ve kterém uživatelé můžou použít zálohování virtuálních počítačů.
  - Definujte smlouvu o úrovni služeb (SLA) pro obnovení na základě priority aplikací nebo dopadu na firmu.
- **Škálování**
  - Při připojování k velkému počtu nových virtuálních počítačů (Pokud se vyžaduje zálohování) zvažte možnost rozložit zálohy.
  - Vyhodnoťte zálohovací produkty, které můžou efektivně zachytit a přenést data záloh, aby se minimalizoval obsah prostředků v řešení.
  - Vyhodnoťte záložní produkty, které efektivně ukládají zálohovaná data pomocí přírůstkových nebo rozdílových záloh, abyste minimalizovali potřebu úplných záloh napříč všemi virtuálními počítači v prostředí.
- **Obnovení**
  - Záložní produkty můžou obnovit virtuální disky, data aplikací v existujícím virtuálním počítači nebo celý prostředek virtuálního počítače a přidružené virtuální disky. Schéma obnovení, které potřebujete, závisí na tom, jak plánujete aplikaci obnovit. Může být například snazší znovu nasadit systém SQL Server ze šablony a pak obnovit databáze namísto obnovení celého virtuálního počítače nebo sady virtuálních počítačů.

### <a name="replicationmanual-failover"></a>Replikace/ruční převzetí služeb při selhání

Alternativním přístupem k podpoře obnovení je replikace dat do jiného prostředí. Tato data můžou být vymezená na aplikaci, jako je například replikace databáze nebo operační systém v hostovaném operačním systému, pomocí agenta nebo na úrovni virtuálního počítače integrací s rozhraními API centra Azure Stack. V případě havárie se vyžaduje převzetí služeb při selhání do sekundárního umístění. Převzetí služeb při selhání může nativně zpracovávat aplikace jako se skupinami dostupnosti SQL nebo na úrovni hostovaného operačního systému pomocí agentů nebo technologie clusterů nebo na úrovni virtuálního počítače pomocí produktu ochrany.

### <a name="high-availabilityautomatic-failover"></a>Vysoká dostupnost/automatické převzetí služeb při selhání

Aplikace, které nativně podporují vysokou dostupnost nebo spoléhá na software clusteru, aby dosáhli vysoké dostupnosti napříč uzly, se dají nasadit napříč skupinou virtuálních počítačů v jednom Azure Stackovém centru nebo mezi různými Azure Stack instancemi hub. Ve všech případech je potřeba určitou úroveň vyrovnávání zatížení, aby se zajistilo správné směrování provozu aplikací. V této konfiguraci se může aplikace automaticky zotavit z chyb. U místních hardwarových chyb implementuje infrastruktura centra Azure Stack vysokou dostupnost a odolnost proti chybám ve fyzické infrastruktuře. V případě chyb na úrovni výpočetní služby používá rozbočovač služby Azure Stack v konfiguraci N-1 více uzlů v jednotce škálování. Na úrovni virtuálního počítače se v sadách dostupnosti a škálování modelují jednotlivé uzly v jednotce škálování-jednotka jako doména selhání, aby se zaručila ochrana na úrovni uzlu, takže selhání uzlů nevezmou distribuovanou aplikaci.

### <a name="no-protection"></a>Bez ochrany

Některé aplikace nemusí obsahovat data, která je třeba zachovat. Například virtuální počítače používané pro vývoj a testování obvykle \' nemusí být obnoveny. Dalším příkladem je Bezstavová aplikace, kterou je možné v případě selhání znovu nasadit z kanálu CI/CD. Je důležité určit aplikace, které nevyžadují ochranu, aby nedocházelo k zbytečné ochraně virtuálních počítačů.

<!-- ## Recommended topologies

Important considerations for your Azure Stack deployment: -->

## <a name="next-steps"></a>Další kroky

V tomto článku najdete obecné pokyny pro ochranu virtuálních počítačů, které jsou nasazené v Azure Stack. Informace o použití služeb Azure k ochraně virtuálních počítačů uživatele najdete v tématu:

- [Důležité informace týkající se provozní kontinuity a zotavení po havárii](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="partner-products"></a>Partnerské produkty

- [Datový list ekosystému Azure Stack pro integraci Datacenter](https://aka.ms/azurestackbcdrpartners)
- Další informace o partnerských produktech, které nabízejí ochranu virtuálního počítače v Azure Stack, najdete v tématu [Ochrana aplikací a dat v Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
