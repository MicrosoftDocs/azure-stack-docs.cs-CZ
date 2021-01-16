---
title: Přehled nasazení modulárního datového centra (MDC) a nastavení pro hostitele životního cyklu Azure Stack centra hardwaru (HLH) management server | Microsoft Docs
description: Přečtěte si, co očekávat úspěšné nasazení modulárního datového centra (MDC) v lokalitě, od plánování po nasazení.
services: azure-stack
documentationcenter: ''
author: asganesh
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 5d59532308239f9eda95ba9f81599235a8c8f61c
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255550"
---
# <a name="mdc-requirements-overview"></a>Přehled požadavků na MDC

Tato příručka popisuje požadavky potřebné k instalaci a konfiguraci modulárního datového centra (MDC). 

Mezi cíle tohoto průvodce patří:

- Zadáním kontrolního seznamu před nasazením ověřte, zda byly před instalací součástí splněny všechny požadavky.
- Zaveďte klíčové součásti MDC.
- Ověřte nasazení zákazníka.

K úplnému pochopení obsahu tohoto průvodce se vyžaduje technické prostředí s virtualizací, servery, operačními systémy, sítěmi a řešeními úložiště. 

Tato příručka se zaměřuje na nasazení základních komponent centra Microsoft Azure Stack a specifikuje řešení MDC. Průvodce nevysvětluje provozní postupy centra Azure Stack a nezahrnuje všechny funkce, které jsou dostupné v centru Azure Stack. 

## <a name="introduction"></a>Úvod

MDC je integrovaná nabídka pro Azure Stack hub na úrovni Standard 40-chodidla zásobník pro expedici v metalu. Kontejner zahrnuje jednotku řízení klimatu, systém osvětlení a výstrahy. Základní komponenty centra Azure Stack jsou nainstalovány jako tři nezávislé lusky: pod 1, Rack 1 a stojan 2, pod 2, Rack 1 a stojan 2 a pod 3, stojan 1 a Rack 2.

Každé z nich se skládá ze dvou 42U stojanů. Objekt pod zahrnuje přepínače Top-of-Rack, přepínače Edge a přepínač řadiče pro správu základní desky (BMC). Každý pod navíc zahrnuje hostitele životního cyklu hardwaru (HLH) a koncentrátor sériového portu. Základní výpočetní kapacitu a úložnou kapacitu nabízí Azure Stack jednotky škálování centra (SU), které se skládají z osmi serverů R840 (REA). Další kapacitu úložiště poskytuje Isilon uzly úložiště 48. Fyzická konfigurace všech lusků je shodná.

## <a name="terminology"></a>Terminologie

V následující tabulce jsou uvedeny některé z podmínek, které jsou použity v této příručce.

|Pojem    |Definice |
|-------|-----------|
|Hostitel životního cyklu hardwaru (HLH)|    HLH je fyzický server, který se používá k počátečnímu zavádění nasazení, a také průběžnou správu hardwaru, podporu a zálohování infrastruktury Azure Stack hub. HLH spouští Windows Server 2019 s desktopovým prostředím a rolí Hyper-V. Server slouží k hostování nástrojů pro správu hardwaru, nástrojů pro správu přepínačů, Azure Stack nástrojů centra partnerů a virtuálního počítače pro nasazení. |
|Virtuální počítač pro nasazení (DVM)|    DVM je virtuální počítač, který se vytvoří v HLH po dobu trvání nasazení softwaru Azure Stack hub. DVM spouští modul orchestrace softwaru Azure Stack hub s názvem Enterprise Cloud Engine (EHK) pro instalaci a konfiguraci Azure Stack infrastruktury centra prostředků infrastruktury na všech serverech centra Azure Stack škály, které jsou v síti.|
|Sada nástrojů pro partnery centra Azure Stack|    Kolekce softwarových nástrojů, které slouží k zaznamenání vstupních parametrů specifických pro konkrétní zákazníky a zahájení instalace a konfigurace centra Azure Stack. Zahrnuje list nasazení, což je grafické rozhraní (GUI), které se používá k zachytávání a ukládání konfigurovatelných parametrů pro instalaci centra Azure Stack. Zahrnuje taky nástroj Generátor konfigurace sítě, který používá vstupy na listech nasazení k vytváření konfiguračních souborů sítě pro všechna fyzická síťová zařízení v řešení.|
|Balíček rozšíření OEM    |Balíček firmwaru, ovladačů zařízení a nástrojů pro správu hardwaru ve specializovaném formátu, který se používá Azure Stack hub během počátečního nasazení a aktualizace.|
|Koncentrátor sériového portu    |V každém z nich je nainstalované fyzické zařízení, které poskytuje síťový přístup k sériovým portům síťových přepínačů pro účely nasazení a správy.|
|Jednotka škálování    |Základní součást centra Azure Stack, která poskytuje výpočetní prostředky a prostředky úložiště pro Azure Stack infrastruktury a zatížení prostředků infrastruktury. Každý pod zahrnuje osm serverů MDC R840 označovaných také jako uzly.|
|Úložiště Isilon |    Komponenta centra Azure Stack, která je specifická pro řešení MDC. Isilon poskytuje další objekty BLOB a úložiště souborů pro úlohy centra Azure Stack. Každý pod zahrnuje 48 uzlů úložiště Isilon.|
|Nulu    |V kontextu MDC je pod nezávislou logickou jednotkou, která se skládá ze dvou propojených fyzických stojanů. Kompletní řešení zahrnuje tři lusky nainstalované v jednom kontejneru.|

## <a name="deployment-workflow"></a>Pracovní postup nasazení

Proces nasazení MDC se na vysoké úrovni skládá z těchto fází:

### <a name="planning-phase"></a>Plánovací fáze
1. Plánování výkonu datového centra.
1. Plánování konfigurace logické sítě Azure Stackho centra
1. Plánování [integrace sítě Datacenter](../operator/azure-stack-network.md).
1. Plánování pro [integraci identity a zabezpečení](/azure/security/fundamentals/identity-management-best-practices)
1. Plánování [certifikátů PKI](../operator/azure-stack-pki-certs.md).

### <a name="preparation-phase"></a>Přípravná fáze
1. Shromažďování inventáře.
1. Připojení k napájení a zapnutí řešení.
1. Ověřuje se stav systému TVK.
1. Ověřování monitorování požáru a výstrahy stavu systému.
1. Ověřuje se stav fyzického hardwaru.

### <a name="execution-phase--separately-for-each-of-the-three-pods"></a>Fáze provádění – samostatně pro každé tři lusky
1. Konfiguruje se hostitel životního cyklu hardwaru.
1. Konfigurace síťových přepínačů.
1. Integrace sítě Datacenter.
1. Konfigurace nastavení fyzického hardwaru.
1. Konfigurace úložiště Isilon
1. Nasazuje se infrastruktura prostředků infrastruktury Azure Stack hub.
1. Integrace identit datacentra
1. Instalace doplňků pro rozšířené funkce.

### <a name="validation-phase--separately-for-each-of-the-three-pods"></a>Fáze ověření – samostatně pro každé tři lusky
1. Ověření stavu po nasazení.
1. Probíhá registrace centra Azure Stack s Microsoftem.
1. Azure Stack zákazníka centra.