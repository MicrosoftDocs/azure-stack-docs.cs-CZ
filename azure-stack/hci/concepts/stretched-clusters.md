---
title: Přehled roztaženého clusteru
description: Přečtěte si o roztažené clustery
author: v-dasis
ms.topic: how-to
ms.date: 11/05/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e9651111e82b3449823ec7d13135154e7f0a447e
ms.sourcegitcommit: 96bc36a203954622be411fdb038d601e49f97d4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94441136"
---
# <a name="stretched-clusters-overview"></a>Přehled roztaženého clusteru

> Platí pro Azure Stack HCI, verze v20H2

Azure Stack řešení roztaženého clusteru v HCI pro zotavení po havárii poskytuje automatické převzetí služeb při selhání pro rychlé obnovení provozu a bez nutnosti ručního zásahu. Replika úložiště poskytuje replikaci svazků napříč lokalitami při zotavení po havárii se všemi servery, které se synchronizují.

Replika úložiště podporuje synchronní i asynchronní replikaci:

- Synchronní replikace zrcadlí data napříč lokalitami v síti s nízkou latencí se svazky konzistentními se selháním, aby se zajistila nulová ztráta dat na úrovni systému souborů během selhání.
- Asynchronní replikace zrcadlí data v lokalitách nad rámec metropolitních oblastí přes síťová propojení s vyššími latencemi, ale bez záruky, že obě lokality mají v době selhání totožné kopie dat.

Existují dva typy roztaženého clusteru, aktivní – pasivní a aktivní-aktivní. Můžete nastavit replikaci sítě aktivní – pasivní, kde je k dispozici upřednostňovaný web a směr pro replikaci. Replikace aktivní-aktivní je v případě, kdy se replikace může probíhat obousměrně z obou lokalit. Tento článek se zabývá jenom konfigurací aktivní/pasivní.

Jediným pojmem je *aktivní* lokalita, která má prostředky a poskytuje role a úlohy pro připojení klientů k. *Pasivní* lokalita je taková, která neposkytuje žádné role ani úlohy pro klienty a čeká na převzetí služeb při selhání z aktivní lokality po zotavení po havárii.

Lokality můžou být ve dvou různých stavech, v různých městech, v různých podlažích nebo různých místnostech. Roztažené clustery, které používají dvě lokality, poskytují zotavení po havárii a kontinuitu podnikového prostředí, protože lokalita utrpěla výpadek nebo selhání.

Pokud si chcete přehrát video v roztaženém clusteringu s Azure Stackm HCI, zabrat několik minut:
> [!VIDEO https://www.youtube.com/embed/rYnZL1wMiqU]

## <a name="active-passive-stretched-cluster"></a>Aktivní – pasivní roztažený cluster

Následující diagram znázorňuje web 1 jako aktivní lokalitu s replikací do lokality 2, což je jednosměrná replikace.

:::image type="content" source="media/stretched-cluster/active-passive-stretched-cluster.png" alt-text="Scénář aktivního nebo pasivního roztaženého clusteru"  lightbox="media/stretched-cluster/active-passive-stretched-cluster.png":::

## <a name="active-active-stretched-cluster"></a>Roztažený cluster aktivní – aktivní

Následující diagram ukazuje lokalitu 1 i Web 2 jako aktivní lokality a obousměrnou replikaci na druhou lokalitu.

:::image type="content" source="media/stretched-cluster/active-active-stretched-cluster.png" alt-text="Scénář aktivního/aktivního roztaženého clusteru" lightbox="media/stretched-cluster/active-active-stretched-cluster.png":::

## <a name="next-steps"></a>Další kroky

- Seznamte se s hardwarem a dalšími požadavky na roztažené clustery. Viz [požadavky na systém](system-requirements.md).
- Naučte se nasadit roztaženého clusteru pomocí centra pro správu Windows. Další informace najdete v tématu [Vytvoření clusteru pomocí centra pro správu systému Windows](../deploy/create-cluster.md).
- Naučte se nasazovat roztaženého clusteru pomocí prostředí PowerShell. Další informace najdete v tématu [Vytvoření clusteru pomocí prostředí PowerShell](../deploy/create-cluster-powershell.md).
- Naučte se vytvářet svazky a nastavovat replikaci pro roztažené clustery. Přečtěte si téma [vytvoření svazků a nastavení replikace pro roztažené clustery](../manage/create-stretched-volumes.md).
