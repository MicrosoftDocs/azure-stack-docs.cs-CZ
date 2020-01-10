---
title: Plánování kapacity pro role App Service serveru v centru Azure Stack | Microsoft Docs
description: Přečtěte si o plánování kapacity App Service rolích serveru v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: d03a7a848213be2f57556616e9fdbf4cd3a0f3e2
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809062"
---
# <a name="capacity-planning-for-app-service-server-roles-in-azure-stack-hub"></a>Plánování kapacity pro role App Service serveru v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Pokud chcete nastavit nasazení Azure App Service připraveného pro produkční prostředí v centru Azure Stack, musíte naplánovat kapacitu, kterou systém očekává pro podporu.  

Tento článek poskytuje pokyny pro minimální počet výpočetních instancí a SKU výpočtů, které byste měli použít pro jakékoli provozní nasazení.

Pomocí těchto pokynů můžete naplánovat strategii App Service kapacity.

| Role serveru App Service | Minimální doporučený počet instancí | Doporučená jednotka COMPUTE|
| --- | --- | --- |
| Správce | 2 | A1 |
| Front-end | 2 | A1 |
| Správa | 2 | A3 |
| Vydavatel | 2 | A1 |
| Webové pracovníky – sdílené | 2 | A1 |
| Webové pracovníky – vyhrazené | 2 na vrstvu | A1 |

## <a name="controller-role"></a>Role kontroleru

**Doporučené minimum**: dvě instance standardu a1

Kontroler Azure App Service obvykle využívá nízkou spotřebu procesoru, paměti a síťových prostředků. Pro zajištění vysoké dostupnosti ale musíte mít dva řadiče. Maximální počet povolených řadičů je také dva řadiče. Během nasazování můžete vytvořit druhý kontroler webových serverů přímo z instalačního programu.

## <a name="front-end-role"></a>Role front-endu

**Doporučené minimum**: dvě instance standardu a1

Front-end směruje požadavky na webové pracovníky v závislosti na dostupnosti webového pracovního procesu. Pro zajištění vysoké dostupnosti byste měli mít více než jeden front-end a můžete mít více než dvě. Pro účely plánování kapacity zvažte, že každý jádro může zpracovávat přibližně 100 požadavků za sekundu.

## <a name="management-role"></a>Role správy

**Doporučené minimum**: dvě instance standardu a3

Role modelu nasazení Classic pro Azure App zodpovídá za App Service Azure Resource Manager a koncové body rozhraní API, rozšíření portálu (správce, tenant, funkce portálu) a datovou službu. Role management server obvykle vyžaduje v produkčním prostředí pouze přibližně 4 GB paměti RAM. Pokud ale provádíte mnoho úloh správy (například vytváření webů), může docházet k vysokým úrovním CPU. Pro zajištění vysoké dostupnosti byste měli mít k této roli přiřazený víc než jeden server a aspoň dvě jádra na server.

## <a name="publisher-role"></a>Role vydavatele

**Doporučené minimum**: dvě instance standardu a1

Pokud je mnoho uživatelů současně publikováním, může mít role vydavatele těžké využití CPU. V případě vysoké dostupnosti se ujistěte, že je k dispozici více než jedna role vydavatele. Vydavatel zpracovává jenom přenosy FTP/FTPS.

## <a name="web-worker-role"></a>Role webového pracovního procesu

**Doporučené minimum**: dvě instance standardu a1

Pro zajištění vysoké dostupnosti byste měli mít aspoň čtyři role webového pracovního procesu: dva pro sdílený režim webu a dvě pro každou vyhrazenou vrstvu pracovního procesu, kterou hodláte nabízet. Sdílené a vyhrazené výpočetní režimy poskytují klientům různé úrovně služeb. Pokud máte spoustu vašich zákazníků, možná budete potřebovat víc webových procesů:

- Používání vyhrazených vrstev pracovního procesu výpočetního režimu (které jsou náročné na prostředky).
- Spuštěno ve sdíleném výpočetním režimu.

Jakmile uživatel vytvoří plán App Service pro vyhrazenou SKLADOVOU položku výpočetního režimu, počet webových pracovních procesů, které jsou uvedené v tomto plánu App Service, už nejsou k dispozici pro uživatele.

Pokud chcete poskytnout Azure Functions uživatelům v modelu plánování spotřeby, je nutné nasadit sdílené webové pracovní procesy.

Při rozhodování o počtu sdílených rolí sdíleného webového pracovního procesu, které se mají použít, si přečtěte tyto požadavky:

- **Paměť**: paměť je nejdůležitějším prostředkem pro roli webového pracovního procesu. Nedostatek paměti má vliv na výkon webového serveru při výměně virtuální paměti z disku. Každý server vyžaduje přibližně 1,2 GB paměti RAM pro operační systém. Paměť RAM nad touto prahovou hodnotou se dá použít ke spouštění webů.
- **Procento aktivních**webů: obvykle je u nasazení Azure Stack centra aktivní asi 5 procent aplikací v Azure App Service. Procento aplikací, které jsou v daném okamžiku aktivní, ale může být vyšší nebo nižší. V případě aktivní míry aplikace o 5 procent maximální počet aplikací, které se mají umístit v Azure App Service v Azure Stackm nasazení centra, by měl být méně než 20 časů počtu aktivních webů (5 × 20 = 100).
- **Průměrné nároky na paměť**: průměrné nároky na paměť pro aplikace zjištěné v produkčním prostředí mají přibližně 70 MB. Pomocí těchto nároků se vypočte paměť přidělená napříč všemi počítači rolí webového pracovního procesu nebo virtuálními počítači následujícím způsobem:

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   Pokud máte například v prostředí s 10 rolemi webového pracovního procesu 5 000 aplikací, musí mít každý virtuální počítač role webového pracovního procesu 7060-MB paměti RAM:

   `5,000 * 70 * 0.05 - (10 * 1044) = 7060 (= about 7 GB)`

   Informace o přidání dalších instancí pracovních procesů najdete v tématu [Přidání dalších rolí pracovního procesu](azure-stack-app-service-add-worker-roles.md).

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>Další požadavky na vyhrazené pracovní procesy během upgradu a údržby

Během upgradu a údržby pracovních procesů bude Azure App Service v centru Azure Stack v jednom okamžiku provádět údržbu na 20% každé vrstvy pracovního procesu.  Cloudové skupiny proto musí vždy udržovat 20% fondu nepřidělených pracovních procesů na vrstvu pracovního procesu, aby se jejich klienti nevědomi jakékoli ztráty služby během upgradu a údržby.  Pokud například máte 10 pracovních procesů v úrovni pracovního procesu, měli byste zajistit, aby 2 nepřidělené aktualizace umožňovaly upgrade a údržbu. Pokud se celé 10 pracovníků stane přiděleno, měli byste škálovat vrstvu pracovního procesu až do udržování fondu nepřidělených pracovních procesů. 

Při upgradu a údržbě Azure App Service přesunou úlohy na nepřidělené pracovní procesy, aby se zajistilo, že úlohy budou fungovat i nadále. Pokud ale během upgradu nejsou k dispozici žádní nepřidělené pracovní procesy, může dojít k výpadku zatížení tenanta. Pokud jde o sdílené pracovní procesy, zákazníci nepotřebují zřídit další pracovní procesy, protože služba bude přidělovat klientské aplikace v rámci dostupných pracovních procesů automaticky. Pro zajištění vysoké dostupnosti je na této úrovni minimální požadavek na dva pracovní procesy.

Správci cloudu můžou monitorovat své přidělení na úrovni pracovního procesu v oblasti pro správu App Service na portálu Azure Stack správce centra. Přejděte na App Service a v levém podokně vyberte vrstvy pracovního procesu. Tabulka vrstev pracovního procesu zobrazuje název, velikost, použitý obrázek, počet dostupných pracovních procesů (nepřidělené), celkový počet pracovních procesů v každé úrovni a celkový stav vrstvy pracovního procesu.

![Správa App Service – vrstvy pracovního procesu][1]

## <a name="file-server-role"></a>Role souborového serveru

Pro roli souborového serveru můžete použít samostatný souborový server pro vývoj a testování. Například při nasazení Azure App Service na Azure Stack Development Kit (ASDK) můžete použít tuto [šablonu](https://aka.ms/appsvconmasdkfstemplate).  Pro produkční účely byste měli použít předem nakonfigurovaný souborový server systému Windows nebo předem nakonfigurovaný souborový server, který není Windows.

V produkčních prostředích používá role souborového serveru náročné vstupně-výstupní operace disku. Vzhledem k tomu, že jsou všechny soubory obsahu a aplikace pro uživatelské weby, měli byste pro tuto roli předem nakonfigurovat jeden z následujících prostředků:

- Souborový server Windows
- Cluster souborových serverů systému Windows
- Souborový server jiný než Windows
- Cluster souborových serverů s jiným systémem než Windows
- Zařízení NAS (úložiště připojené k síti)

Další informace najdete v tématu [zřízení souborového serveru](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server).

## <a name="next-steps"></a>Další kroky

[Předpoklady pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png