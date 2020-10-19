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
ms.date: 04/09/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 04/09/2020
ms.openlocfilehash: 1daee460239e178641c4c0b073d2d24a0e63053c
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183033"
---
# <a name="mdc-deployment-overview"></a>Přehled nasazení MDC

Tato příručka pro nasazení popisuje kroky pro instalaci a konfiguraci modulárního datového centra (MDC). Tento průvodce také popisuje automatizovaný proces pro nastavení management server Azure Stack HLH hub Host pro nasazení centra Azure Stack.

Mezi cíle tohoto průvodce patří:

- Zadáním kontrolního seznamu před nasazením ověřte, zda byly před instalací součástí splněny všechny požadavky.
- Zaveďte klíčové součásti MDC.
- Popište, jak nainstalovat a nakonfigurovat klíčové komponenty.
- Ověřte nasazení zákazníka.

Tato příručka pro nasazení je určená pro tým Microsoft Field Professional, který zodpovídá za nasazení MDC na webu zákazníka.

K úplnému pochopení obsahu tohoto průvodce se vyžaduje technické prostředí s virtualizací, servery, operačními systémy, sítěmi a řešeními úložiště. Inženýr nasazení musí mít znalosti Microsoft Windows serveru 2019 s Hyper-V, Azure Stack hub, Azure a Microsoft PowerShellem.

Tato příručka se zaměřuje na nasazení základních komponent centra Microsoft Azure Stack a specifikuje řešení MDC. Průvodce nevysvětluje provozní postupy centra Azure Stack a nezahrnuje všechny funkce, které jsou dostupné v centru Azure Stack. Další informace najdete v tématu [Průvodce pro operátora centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/).

## <a name="introduction"></a>Úvod

MDC je integrovaná nabídka pro Azure Stack hub na úrovni Standard 40-chodidla zásobník pro expedici v metalu. Kontejner zahrnuje jednotku řízení klimatu, systém osvětlení a výstrahy. Základní komponenty centra Azure Stack, jako jsou servery a přepínače, jsou nainstalovány ve šesti fyzických rackech, které jsou logicky uspořádány do tří nezávislých lusků.

Každé z nich se skládá ze dvou 42U stojanů. Objekt pod zahrnuje přepínače Top-of-Rack, přepínače Edge a přepínač řadiče pro správu základní desky (BMC). Každý pod navíc zahrnuje hostitele životního cyklu hardwaru (HLH) a koncentrátor sériového portu. Základní výpočetní kapacitu a úložnou kapacitu nabízí Azure Stack jednotky škálování centra (SU), které se skládají z osmi serverů R840 (REA). Další kapacitu úložiště poskytuje Isilon uzly úložiště 48. Fyzická konfigurace všech lusků je shodná.

## <a name="terminology"></a>Terminologie

V následující tabulce jsou uvedeny některé z podmínek, které jsou použity v této příručce.

|Označení    |Definice |
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

Proces nasazení MDC se na vysoké úrovni skládá z následujících kroků:

1. Plánovací fáze:
   1. Plánování výkonu datového centra.
   1. Plánování konfigurace logické sítě Azure Stackho centra
   1. Plánování integrace sítě Datacenter.
   1. Plánování pro integraci identity a zabezpečení
   1. Plánování certifikátů PKI.
1. Přípravná fáze:
   1. Shromažďování inventáře.
   1. Připojení k napájení a zapnutí řešení.
   1. Ověřuje se stav systému TVK.
   1. Ověřování monitorování požáru a výstrahy stavu systému.
   1. Ověřuje se stav fyzického hardwaru.
1. Fáze provádění – samostatně pro každé tři lusky:
   1. Konfiguruje se hostitel životního cyklu hardwaru.
   1. Konfigurace síťových přepínačů.
   1. Integrace sítě Datacenter.
   1. Konfigurace nastavení fyzického hardwaru.
   1. Konfigurace úložiště Isilon
   1. Nasazuje se infrastruktura prostředků infrastruktury Azure Stack hub.
   1. Integrace identit datacentra
   1. Instalace doplňků pro rozšířené funkce.
1. Fáze ověření – samostatně pro každé tři lusky:
   1. Ověření stavu po nasazení.
   1. Probíhá registrace centra Azure Stack s Microsoftem.
   1. Operátor centra Azure Stack ruky.
  
Všechna výše uvedená témata jsou podrobněji vysvětlena v tomto průvodci.
