---
title: Výuka modelu strojového učení na hraničním vzoru
description: Naučte se, jak na hraničních zařízeních s Azure a Azure Stack hub dělat školení modelu Machine Learning.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: da1012cc8847f221de6bb540ba4e191e43920f41
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847703"
---
# <a name="train-machine-learning-model-at-the-edge-pattern"></a>Výuka modelu strojového učení na hraničním vzoru

Vygenerujte přenositelné modely strojového učení (ML) z dat, která existují jenom místně.

## <a name="context-and-problem"></a>Kontext a problém

Mnoho organizací chce odemknout poznatky z místních nebo starších dat pomocí nástrojů, které jejich odborníci na data znají. [Azure Machine Learning](/azure/machine-learning/) poskytuje cloudové nativní nástroje pro školení, ladění a nasazení modelů ml a hloubkového učení.  

Některá data jsou ale příliš velká pro posílání do cloudu nebo se nedají do cloudu odeslat z důvodů regulativních předpisů. Pomocí tohoto modelu mohou odborníci přes data využít Azure Machine Learning k učení modelů pomocí místních dat a výpočetních prostředků.

## <a name="solution"></a>Řešení

Školení na hraničním vzorci používá virtuální počítač, který běží na rozbočovači Azure Stack. Virtuální počítač je v Azure ML zaregistrován jako výpočetní cíl, takže data přístupu k němu jsou dostupná jenom místně. V tomto případě jsou data uložená v úložišti objektů BLOB centra Azure Stack.

Jakmile je model vyškolený, zaregistruje se s Azure ML a přidá se do Azure Container Registry pro nasazení. Pro tuto iteraci vzoru musí být virtuální počítač pro školení centra Azure Stack dosažitelný přes veřejný Internet.

[![Model vlakové ML na hraniční architektuře](media/pattern-train-ml-model-at-edge/solution-architecture.png)](media/pattern-train-ml-model-at-edge/solution-architecture.png)

Jak tento vzor funguje:

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

Tento model umožňuje službě Azure ML přístup k možným citlivým datům v místním prostředí. Ujistěte se, že účet používaný k SSH do virtuálního počítače centra Azure Stack obsahuje silné heslo a školicí skripty neuchovávají ani neodesílají data do cloudu.

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:

- Přehled ML a Příbuzná témata najdete v [dokumentaci k Azure Machine Learning](/azure/machine-learning) .
- V tématu [Azure Container Registry](/azure/container-registry/) se dozvíte, jak vytvářet, ukládat a spravovat image pro nasazení kontejnerů.
- Další informace o poskytovateli prostředků a způsobu nasazení najdete [v tématu App Service v centru Azure Stack](/azure-stack/operator/azure-stack-app-service-overview) .
- Další informace o osvědčených postupech a o tom, jak získat odpovědi na další otázky, najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) .
- Další informace o celém portfoliu produktů a řešení najdete v [Azure Stack rodině produktů a řešení](/azure-stack) .

Až budete připraveni otestovat příklad řešení, pokračujte v [modelu vlakové ml v Průvodci nasazením Edge](https://aka.ms/edgetrainingdeploy). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.
