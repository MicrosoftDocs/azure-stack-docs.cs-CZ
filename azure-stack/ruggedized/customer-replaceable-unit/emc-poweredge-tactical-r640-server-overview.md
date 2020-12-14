---
title: Server EMC PowerEdge taktické R640 – přehled
description: Další informace o serveru EMC PowerEdge taktické R640
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d1f0e141c4ca4966aab9735064287121718dee87
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391194"
---
# <a name="dell-emc-poweredge-tactical-r640-server-overview"></a>Server Dell EMC PowerEdge taktické R640 – přehled

Tato část je přehled komponent serveru Dell EMC PowerEdge taktické R640.

## <a name="chassis-front-view"></a>Přední pohled na skříň

Na následujícím obrázku je příklad serveru PowerEdge taktické R640, 2U serveru.

Obrázek 3: Taktické R640 pro PowerEdge – HLH a SU – pohled na přední skříň

![](media/image-60.png)

Následující tabulka popisuje funkce front-taktické R640 \' s PowerEdge.

Tabulka 1. Funkce front-end R640 pro PowerEdge taktické

| Porty, panely a sloty  | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Levý ovládací panel        | Obsahuje indikátor stavu systému a ID systému, indikátor stavu a iDRAC rychlé synchronizace 2 (bezdrátové sítě). <br>**Poznámka:** Indikátor rychlé synchronizace iDRAC 2 je k dispozici pouze v některých konfiguracích. <br>Indikátor stavu: slouží k identifikaci všech neúspěšných hardwarových součástí. K dispozici je až pět stavových diod LED a celkový stav INDIKÁTORu stavu systému (stav skříně a ID systému). Další informace najdete v tématu indikátory LED pro stav PowerEdge na stránce 61. <br>Rychlá synchronizace 2 (bezdrátová): označuje systém s povolenou rychlou synchronizací. Funkce Rychlá synchronizace je volitelná. Tato funkce umožňuje správu systému pomocí mobilních zařízení. Tato funkce agreguje inventář hardwaru nebo firmwaru a různé informace o diagnostice a chybách na úrovni systému, které se dají použít při řešení potíží se systémem.  |
| Port VGA                  | Umožňuje připojit zobrazovací zařízení k systému.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Ovládací panel vpravo       | Obsahuje tlačítko napájení, port USB, port iDRAC Direct Micro a indikátor stavu iDRAC Direct.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Pohled na pozadí skříně

Následující obrázek ukazuje komponenty back-R640 pro PowerEdge taktické.

Obrázek 4. Taktické R640 HLH – zobrazení šasi pro back-

![](media/image-61.png)

Následující tabulka popisuje funkce na panelu back-taktické R640 pro PowerEdge.

Tabulka 2. Funkce zadního panelu pro PowerEdge taktické R640

| Porty, panely a sloty       | Ikona                      | Popis                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Pevné disky                   | –                       | Operační systém a úložiště dat pro uzly serveru HLH a SU.                                                                                                                                                                                                                        |
| Modul nadřízeného                   | –                       | Spouštěcí řešení optimalizovaného úložiště (vedoucí), které se používá k instalaci operačního systému nebo hypervisoru.                                                                                                                                                                                 |
| Jednotky zdroje napájení (2)        | –                       | Tyto síly poskytují sílu serveru a jsou k dispozici společně pro redundanci.                                                                                                                                                                                                 |
| Porty USB 3,0                 |  ![](media/image-62.png)   | Porty USB jsou 9-PIN a 3,0 kompatibilní. Tyto porty vám umožní připojit zařízení USB k systému.                                                                                                                                                                     |
| Port VGA                      |   ![](media/image-63.png)  | Umožňuje připojit zobrazovací zařízení k systému.                                                                                                                                                                                                                      |
| Sériový port                   |   ![](media/image-64.png)  | Umožňuje připojit sériové zařízení k systému.                                                                                                                                                                                                                       |
| Port iDRAC9 Enterprise        |   ![](media/image-65.png)  | Umožňuje vzdálený přístup k iDRAC.                                                                                                                                                                                                                                       |
| Port napájení CMA                | –                       | Port napájení ARM správy kabelů (CMA) umožňuje připojit kabel indikátoru stavu k CMA.                                                                                                                                                                     |
| Tlačítko Identifikace systému  |   ![](media/image-66.png) | Tlačítko Identifikace systému (ID) je dostupné na front-a back serveru. Stisknutí tlačítka způsobí blikání indikátoru ID health\System systému. Pomocí tlačítka ID systému můžete také resetovat iDRAC a přistupovat k systému BIOS pomocí režimu krokování.  |

## <a name="right-control-panel"></a>Ovládací panel vpravo

Následující obrázek ukazuje ovládací panel PowerEdge taktické R640 Right.

Obrázek 5. Ovládací panel PowerEdge taktické R640 vpravo

![](media/image-67.png)

Následující tabulka popisuje ovládací prvky a indikátory na pravém panelu.

Tabulka 3. Ovládací prvky a indikátory taktické pravého ovládacího panelu PowerEdge R640

| Položka  | Indikátor nebo tlačítko  | Ikona                      | Popis                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Tlačítko napájení         |   ![](media/image-68.png)  | Určuje, zda je systém zapnutý nebo vypnutý. Kliknutím na tlačítko napájení ručně zapněte nebo vypněte systém.  **Poznámka:** K bezproblémovému vypnutí operačního systému kompatibilního se standardem ACPI stiskněte tlačítko napájení.  |
| 2     | Port USB             | ![](media/image-70.png)    | Port USB je 4 – PIN kód, který odpovídá 2,0. Tento port umožňuje připojit zařízení USB k systému.                                                                                                         |
| 3     | Přímý LED iDRAC     | –                       | Indikátory LED iDRAC přímého světla, které označují, že port iDRAC Direct je aktivně připojen k zařízení.                                                                                        |
| 4     | Port iDRAC Direct    |   ![](media/image-65.png) | Port iDRAC Direct je kompatibilní s rozhraním USB 2,0. Tento port vám umožní získat přístup k funkcím iDRAC Direct.                                                                                              |

## <a name="inside-the-poweredge-tactical-r640"></a>V rámci PowerEdge taktické R640

Následující obrázek ukazuje interní součásti nástroje PowerEdge taktické R640.

Obrázek 6. V rámci PowerEdge taktické R640 HLH

![](media/image-71.png)

Obrázek 7. V rámci PowerEdge taktické R640 SU

![](media/image-72.png)

## <a name="motherboard"></a>Základní desky

Následující obrázek znázorňuje rozložení základní desky.

Obrázek 8. Rozložení základní desky pro PowerEdge taktické R640

![](media/image-73.png)

## <a name="memory"></a>Memory (Paměť)

Následující obrázek ukazuje informace o paměti pro PowerEdge taktické R640.

Obrázek 9: Informace o paměti pro PowerEdge taktické R640

![](media/image-74.png)
