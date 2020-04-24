---
title: Vzor pro školení modelu Machine Learning (ML) na okraji pomocí Azure a centra Azure Stack.
description: Naučte se používat služby Azure a Azure Stack hub pro školení modelů ML na hraničních zařízeních.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 4df466a3b1b1e89be704302153e9e835f504445e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77688861"
---
# <a name="train-machine-learning-ml-model-at-the-edge-pattern"></a>Postup analýzy Machine Learning (ML) na hraničním modelu

Vygenerujte přenositelné modely ML z dat, která existují jenom místně.

## <a name="context-and-problem"></a>Kontext a problém

Mnoho organizací chce odemknout poznatky z místních nebo starších dat pomocí nástrojů, které jejich odborníci na data znají. [Azure Machine Learning](/azure/machine-learning/) poskytuje cloudové nativní nástroje pro školení, ladění a nasazení modelů ml a hloubkového učení.  

Některá data jsou ale příliš velká pro posílání do cloudu nebo se nedají do cloudu odeslat z důvodů regulativních předpisů. Pomocí tohoto modelu mohou odborníci přes data využít Azure Machine Learning k učení modelů pomocí místních dat a výpočetních prostředků. 

## <a name="solution"></a>Řešení

Školení na hraničním vzorci používá virtuální počítač, který běží na rozbočovači Azure Stack. Virtuální počítač je v Azure ML zaregistrován jako výpočetní cíl, což umožňuje přístup k datům dostupným místně. V tomto případě jsou data uložená v úložišti objektů BLOB centra Azure Stack. 

Jakmile je model vyškolený, zaregistruje se s Azure ML a přidá se do Azure Container Registry pro nasazení. Pro tuto iteraci vzoru musí být virtuální počítač pro školení centra Azure Stack dosažitelný přes veřejný Internet. 

[![model vlakové ml na hraniční architektuře](media/pattern-train-ml-model-at-edge/solution-architecture.png)](media/pattern-train-ml-model-at-edge/solution-architecture.png)

Jak řešení funguje: 

1. Virtuální počítač centra Azure Stack je nasazený a registrovaný jako cíl pro výpočetní prostředky pomocí Azure ML.
2. V Azure ML se vytvoří experiment, který jako cíl výpočetní služby používá virtuální počítač centra Azure Stack.
3. Jakmile je model vyškolen, je zaregistrován a kontejnerem.
4. Model se teď dá nasadit do umístění, která jsou v místním prostředí nebo v cloudu.

## <a name="components"></a>Komponenty

Toto řešení používá následující komponenty:

| Vrstva | Součást | Popis |
|----------|-----------|-------------|
| Azure | Azure Machine Learning | [Azure Machine Learning](/azure/machine-learning/) orchestruje školení modelu ml. |
| | Azure Container Registry | Azure ML zabalí model do kontejneru a uloží ho do [Azure Container Registry](/azure/container-registry/) pro nasazení.|
| Centrum Azure Stack | App Service | [Azure Stack centrum s App Service](/azure-stack/operator/azure-stack-app-service-overview) poskytuje základ pro komponenty na hraničních zařízeních. |
| | Compute | Pro výuku modelu ML se používá virtuální počítač centra Azure Stack se systémem Ubuntu s Docker. |
| | Storage | Soukromá data můžou být hostovaná v úložišti objektů BLOB centra Azure Stack. |

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Při rozhodování, jak implementovat toto řešení, vezměte v úvahu následující body:

### <a name="scalability"></a>Škálovatelnost 

Pokud chcete toto řešení povolit pro škálování, budete muset vytvořit vhodný velikost virtuálního počítače na Azure Stackovém centru pro školení.

### <a name="availability"></a>Dostupnost

Ujistěte se, že školicí skripty a virtuální počítač centra Azure Stack mají přístup k místním datům používaným pro školení.

### <a name="manageability"></a>Možnosti správy

Zajistěte, aby modely a experimenty byly správně registrovány, označeny verzí a označily, aby nedocházelo k záměně při nasazení modelu. 

### <a name="security"></a>Zabezpečení

Tento model umožňuje službě Azure ML přístup k možným citlivým údajům v místním prostředí. Ujistěte se, že účet použitý k SSH do virtuálního počítače centra Azure Stack má silné heslo, a školicí skripty neuchovávají ani neodesílají data do cloudu. 

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:
- Přehled ML a Příbuzná témata najdete v [dokumentaci k Azure Machine Learning](/azure/machine-learning) .
- V tématu [Azure Container Registry](/azure/container-registry/) se dozvíte, jak vytvářet, ukládat a spravovat image pro nasazení kontejnerů.
- Další informace o poskytovateli prostředků a způsobu nasazení najdete [v tématu App Service v centru Azure Stack](/azure-stack/operator/azure-stack-app-service-overview) .
- Další informace o osvědčených postupech najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) a odpovědi na další otázky.
- Podívejte se na [Azure Stack rodinu produktů a řešení](/azure-stack), abyste se dozvěděli víc o celém portfoliu produktů a řešení.

Až budete připraveni otestovat příklad řešení, pokračujte v [modelu vlakové ml v Průvodci nasazením Edge](https://aka.ms/edgetrainingdeploy). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.