---
title: NaHLHelné nasazení centra Azure Stack a nastavení pro hostitele životního cyklu Azure Stack centra hardwaru () management server | Microsoft Docs
description: Přečtěte si, co očekávat, že úspěšné nasazení na pracovišti centra Azure Stack je robustní, od plánování po nasazení.
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2021
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 02/17/2021
ms.openlocfilehash: 4d61fc75cd7db67f113369981fe0fa4742178dd5
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100648061"
---
# <a name="azure-stack-hub-ruggedized-deployment-overview"></a>Přehled robustního nasazení centra Azure Stack

Tato příručka pro nasazení popisuje kroky pro instalaci a konfiguraci robustního centra Azure Stack. 

Mezi cíle tohoto průvodce patří:

- Zadáním kontrolního seznamu před nasazením ověřte, zda byly před instalací součástí splněny všechny požadavky.
- Zaveďte klíčové součásti Azure Stackého centra.
- Popište, jak nainstalovat a nakonfigurovat klíčové komponenty.
- Ověřte nasazení zákazníka.

Tato příručka pro nasazení je určená pro tým Microsoft Field Professional, který je zodpovědný za nasazení centra Azure Stack v zákaznickém webu.

K úplnému pochopení obsahu tohoto průvodce se vyžaduje technické prostředí s virtualizací, servery, operačními systémy, sítěmi a řešeními úložiště. Inženýr nasazení musí mít znalosti Microsoft Windows serveru 2019 s Hyper-V, Azure Stack hub, Azure a Microsoft PowerShellem.

Tato příručka se zaměřuje na nasazení základních komponent centra Microsoft Azure Stack a specifikuje řešení robustního řešení centra Azure Stack. Průvodce nevysvětluje provozní postupy centra Azure Stack a nezahrnuje všechny funkce, které jsou dostupné v centru Azure Stack. Další informace najdete v tématu [Průvodce operátorem centra Azure Stack](../operator/index.yml).

## <a name="introduction"></a>Úvod

Robustní centrum Azure Stack je robustní a nasazovatelné nabídky pro Microsoft Azure Stack centrum. Základní komponenty, jako jsou servery a přepínače, jsou obsaženy v přenosových případech s názvem lusky.

Pod je kontejner 4U pro rack, který má menší rozměry než běžný stojan 4U. Existuje jedna Správa pod a dvě jednotky škálování (SU). Správa pod zahrnuje hostitele životního cyklu hardwaru (HLH), přepínače systému 2 25 GbE Top-of-Rack a přepínač řadiče pro správu základní desky (BMC).

Každé SU pod sebou obsahuje dva Azure Stack hub robustní servery R640 SU. Jeden R640 Server zabírá v poli pod sebou prostor 2U. Během nasazování jsou servery v části SU připojené k řadičům BMC a přepínačům prostředí v pod správou.

## <a name="terminology"></a>Terminologie

V následující tabulce jsou uvedeny některé z podmínek, které jsou použity v této příručce.

|Označení   | Definice |
|-------|------------|
|Hostitel životního cyklu hardwaru (HLH)| HLH je fyzický server, který se používá k počátečnímu zavádění nasazení, a také průběžnou správu hardwaru, podporu a zálohování infrastruktury Azure Stack hub. HLH spouští Windows Server 2019 s desktopovým prostředím a rolí Hyper-V. Server slouží k hostování nástrojů pro správu hardwaru, nástrojů pro správu přepínačů, Azure Stack nástrojů centra partnerů a virtuálního počítače pro nasazení. |
|Virtuální počítač pro nasazení (DVM)|  DVM je virtuální počítač, který se vytvoří v HLH po dobu trvání nasazení softwaru Azure Stack hub. DVM spouští modul orchestrace softwaru Azure Stack hub s názvem Enterprise Cloud Engine (EHK) pro instalaci a konfiguraci Azure Stack infrastruktury centra prostředků infrastruktury na všech serverech centra Azure Stack škály, které jsou v síti.|
|Sada nástrojů pro partnery centra Azure Stack|   Kolekce softwarových nástrojů, které slouží k zaznamenání vstupních parametrů specifických pro konkrétní zákazníky a zahájení instalace a konfigurace centra Azure Stack. Zahrnuje list nasazení, což je grafické rozhraní (GUI), které se používá k zachytávání a ukládání konfigurovatelných parametrů pro instalaci centra Azure Stack. Zahrnuje taky nástroj Generátor konfigurace sítě, který používá vstupy na listech nasazení k vytváření konfiguračních souborů sítě pro všechna fyzická síťová zařízení v řešení.|
|Balíček rozšíření OEM  |Balíček firmwaru, ovladačů zařízení a nástrojů pro správu hardwaru ve specializovaném formátu, který se používá Azure Stack hub během počátečního nasazení a aktualizace.|
|Integrovaný řadič vzdáleného přístupu Dell (iDRAC)|  IDRAC s řadičem životního cyklu je kontroler pro správu základní desky, který je vložený do každého centra Azure Stack R640 Server. iDRAC poskytuje funkce vzdálené správy, které vám pomůžou nasazovat, aktualizovat, monitorovat a udržovat servery Azure Stack hub.|
|Jednotka škálování |Základní součást centra Azure Stack, která poskytuje výpočetní prostředky a prostředky úložiště pro Azure Stack infrastruktury a zatížení prostředků infrastruktury. Se skládá ze čtyř Azure Stackch robustních serverů R640 (označovaných taky jako uzly) a dají se dynamicky škálovat až na 16 uzlů.|
|Nulu    |V kontextu služby Azure Stack hub je jako fyzický robustní kontejner navržený tak, aby je mohl přenášet dvě osoby, které obsahují držáky racku a tlumiče pérování k ochraně Azure Stack hub robustního hardwaru od fyzického zatížení v životním prostředí. Zahrnuje případ front-end přenosů, které se dají nainstalovat a zapečetit pro přenos hardwaru. Kompletní řešení s minimální konfigurací zahrnuje tři lusky.|


## <a name="deployment-workflow"></a>Pracovní postup nasazení

Proces nasazení MDC se na vysoké úrovni skládá z fází popsaných v následujících oddílech.

### <a name="planning-phase"></a>Plánovací fáze
1. Plánování výkonu datového centra.
1. Plánování konfigurace logické sítě Azure Stackho centra
1. Plánování [integrace sítě Datacenter](../operator/azure-stack-network.md).
1. Plánování pro integraci [identity](../operator/azure-stack-identity-overview.md) .
1. Plánování pro integraci [zabezpečení](../operator/azure-stack-security-foundations.md)
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
