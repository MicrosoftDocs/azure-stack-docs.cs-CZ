---
title: Přehled serveru
description: Přečtěte si o robustním serveru pro Azure Stack centrum.
author: justinha
ms.topic: how-to
ms.date: 01/28/2021
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: ''
ms.openlocfilehash: a922c99121157c03705784dd99b8abf97fbec507
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577287"
---
# <a name="server-overview"></a>Přehled serveru

Tato část je přehled součástí serveru.

## <a name="chassis-front-view"></a>Přední pohled na skříň

Na následujícím obrázku vidíte Server 2U.


![Diagram, který zobrazuje přední skříň serveru.](media/image-60.png)

Následující tabulka popisuje funkce v předním panelu.

| Porty, panely a sloty  | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Levý ovládací panel        | Obsahuje indikátor stavu systému a ID systému, indikátor stavu a iDRAC rychlé synchronizace 2 (bezdrátové sítě). <br>**Poznámka:** Indikátor rychlé synchronizace iDRAC 2 je k dispozici pouze v některých konfiguracích. <br>Indikátor stavu: slouží k identifikaci všech neúspěšných hardwarových součástí. K dispozici je až pět stavových diod LED a celkový stav INDIKÁTORu stavu systému (stav skříně a ID systému). <br>Rychlá synchronizace 2 (bezdrátová): označuje systém s povolenou rychlou synchronizací. Funkce Rychlá synchronizace je volitelná. Tato funkce umožňuje správu systému pomocí mobilních zařízení. Tato funkce agreguje inventář hardwaru nebo firmwaru a různé informace o diagnostice a chybách na úrovni systému, které se dají použít při řešení potíží se systémem.  |
| Port VGA                  | Umožňuje připojit zobrazovací zařízení k systému.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| Ovládací panel vpravo       | Obsahuje tlačítko napájení, port USB, port iDRAC Direct Micro a indikátor stavu iDRAC Direct.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="chassis-back-view"></a>Pohled na pozadí skříně

Následující obrázek ukazuje součásti back-šasi.


![Diagram, který ukazuje back-skříni serveru.](media/image-61.png)

Následující tabulka popisuje funkce na zadním panelu.


| Porty, panely a sloty       | Ikona                      | Description                                                                                                                                                                                                                                                                 |
|-------------------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Pevné disky                   | –                       | Operační systém a úložiště dat pro uzly serveru HLH a SU.                                                                                                                                                                                                                        |
| Modul nadřízeného                   | –                       | Spouštěcí řešení optimalizovaného úložiště (vedoucí), které se používá k instalaci operačního systému nebo hypervisoru.                                                                                                                                                                                 |
| Jednotky zdroje napájení (2)        | –                       | Tyto síly poskytují sílu serveru a jsou k dispozici společně pro redundanci.                                                                                                                                                                                                 |
| Porty USB 3,0                 |  :::image type="icon" source="media/image-62.png"::: | Porty USB jsou 9-PIN a 3,0 kompatibilní. Tyto porty vám umožní připojit zařízení USB k systému.                                                                                                                                                                     |
| Port VGA                      |   :::image type="icon" source="media/image-63.png":::  | Umožňuje připojit zobrazovací zařízení k systému.                                                                                                                                                                                                                      |
| Sériový port                   |   :::image type="icon" source="media/image-64.png":::  | Umožňuje připojit sériové zařízení k systému.                                                                                                                                                                                                                       |
| Port iDRAC9 Enterprise        |   :::image type="icon" source="media/image-65.png":::  | Umožňuje vzdálený přístup k iDRAC.                                                                                                                                                                                                                                       |
| Port napájení CMA                | –                       | Port napájení ARM správy kabelů (CMA) umožňuje připojit kabel indikátoru stavu k CMA.                                                                                                                                                                     |
| Tlačítko Identifikace systému  |   :::image type="icon" source="media/image-66.png"::: | Tlačítko Identifikace systému (ID) je dostupné na front-a back serveru. Stisknutí tlačítka způsobí blikání indikátoru ID health\System systému. Pomocí tlačítka ID systému můžete také resetovat iDRAC a přistupovat k systému BIOS pomocí režimu krokování.  |

## <a name="right-control-panel"></a>Ovládací panel vpravo

Následující obrázek znázorňuje pravé ovládací panely.

![Diagram, který zobrazuje tlačítko napájení a porty USB v Ovládacích panelech.](media/image-67.png)

Následující tabulka popisuje ovládací prvky a indikátory na pravém panelu.


| Položka  | Indikátor nebo tlačítko  | Ikona                      | Description                                                                                                                                                                                               |
|-------|----------------------|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 1     | Tlačítko napájení         |   :::image type="icon" source="media/image-68.png":::  | Určuje, zda je systém zapnutý nebo vypnutý. Kliknutím na tlačítko napájení ručně zapněte nebo vypněte systém.  **Poznámka:** K bezproblémovému vypnutí operačního systému kompatibilního se standardem ACPI stiskněte tlačítko napájení.  |
| 2     | Port USB             | :::image type="icon" source="media/image-70.png":::    | Port USB je 4 – PIN kód, který odpovídá 2,0. Tento port umožňuje připojit zařízení USB k systému.                                                                                                         |
| 3     | Přímý LED iDRAC     | –                       | Indikátory LED iDRAC přímého světla, které označují, že port iDRAC Direct je aktivně připojen k zařízení.                                                                                        |
| 4     | Port iDRAC Direct    |   :::image type="icon" source="media/image-65.png"::: | Port iDRAC Direct je kompatibilní s rozhraním USB 2,0. Tento port vám umožní získat přístup k funkcím iDRAC Direct.                                                                                              |

## <a name="inside-the-server"></a>Uvnitř serveru

Na následujících obrázcích jsou uvedeny interní součásti serveru.

![Diagram, který zobrazuje pohled na horní stranu interních součástí serveru R640 HLH.](media/image-71.png)


![Diagram, který zobrazuje pohled na horní stranu interního serveru součásti R640 SU.](media/image-72.png)

## <a name="motherboard"></a>Základní desky

Následující obrázek znázorňuje rozložení základní desky.


![Diagram, který zobrazuje informace o službě R640 a rozložení základní desky.](media/image-73.png)

## <a name="memory"></a>Memory (Paměť)

Následující obrázek ukazuje informace o paměti.


![Diagram, který zobrazuje informace o paměti R640.](media/image-74.png)
