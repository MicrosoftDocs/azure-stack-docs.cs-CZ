---
title: Indikátory a kódy
description: Další informace o ukazatelích a kódech
author: justinha
ms.topic: how-to
ms.date: 01/28/2021
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: ''
ms.openlocfilehash: af4b9e3cbbff2c9d2c06f3c3966a2f67b1b7c037
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488067"
---
# <a name="indicators-and-codes"></a>Indikátory a kódy

Funkce popsané v této části jsou stejné jako u hostitele životního cyklu hardwaru a na serverech uzlů škálování jednotky.

## <a name="system-information-label"></a>Popisek systémových informací

Následující obrázek ukazuje popisek systémové informace, který je na předním panelu.

![Popisek systémových informací pro chování indikátoru LED.](media/image-75.png)

## <a name="idrac-direct-led-indicator-codes"></a>kódy indikátorů LED iDRAC přímých

Indikátory LED iDRAC s přímým přístupem, které označují, že je port připojený a používá se jako součást podsystému iDRAC.

IDRAC můžete nakonfigurovat pomocí kabelu USB na Micro-USB (typ AB), který se můžete připojit k přenosnému počítači nebo tabletu. Následující tabulka popisuje iDRAC přímou aktivitu, když je aktivní port iDRAC Direct a vysvětluje iDRAC přímé kódy indikátorů LED.

Tabulka 4. Kódy indikátoru LED

| **Kódy ukazatelů**                                         | **Condition** (Podmínka)                                                |
|-------------------------------------------------------------|--------------------------------------------------------------|
| Solid zelená po dobu dvou sekund                                 | Indikuje, že je přenosný počítač nebo tablet připojený.            |
| Blikající zelená (po dobu dvou sekund a vypnutí po dobu dvou sekund) | Indikuje, že je počítač připojený k přenosnému počítači nebo tabletu. |
| Neosvětlené                                             | Indikuje, že přenosný počítač nebo tablet je odpojený.            |

## <a name="left-control-panel"></a>Levý ovládací panel

Následující obrázek ukazuje levý stav ovládacích panelů a indikátory stavu.



Obrázek 11. Levý ovládací panel

![Diagram, který zobrazuje levý stav ovládacích panelů a indikátory stavu.](media/image-76.png)

Následující tabulka popisuje funkce na levém panelu.


|    <br>Položka       |    <br>Indikátor nebo tlačítko                       |    <br>Ikona                                |    <br>Description                                                                                                  |
|-------------------|--------------------------------------------------|--------------------------------------------|---------------------------------------------------------------------------------------------------------------------|
|    <br>1          |    <br>Indikátory LED stavu                     |    <br>–                                 |    <br>Určuje stav systému.     |
|    <br>2 a 3    |    <br>Indikátory stavu systému a ID systému    | ![Ikona stavu systému](media/image-77.png) |    <br>Určuje stav systému.                                                                                 |

V následující tabulce jsou uvedeny indikátory LED stavu.

> [!NOTE]
> Indikátory LED stavu jsou vždy vypnuté a při výskytu chyby se změní jenom na plnou žlutou žlutou.



| <br>Ikona | <br>Description           | <br>Podmínka                                                                                                                                                                       | <br>Nápravná opatření                                                                                                                                                                                                                                                                                                    |
|----------|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|          | <br>Indikátor jednotky       | <br>Indikátor změní plnou žlutou žlutou, pokud dojde k chybě jednotky.                                                                                                                      | 1. Zkontrolujte protokol událostí systému, abyste zjistili, jestli má jednotka chybu.<br>2. Spusťte příslušný test online diagnostiky.<br>3. restartujte systém a spusťte Embedded Diagnostics (ePSA).<br>4. Pokud jsou jednotky nakonfigurované v poli RAID, restartujte systém a zadejte program konfiguračního nástroje pro konfiguraci hostitelského adaptéru. |
|          | <br>Indikátor teploty | <br>Indikátor změní plnou žlutou, pokud systém dojde k tepelné chybě (například okolní teplota je mimo rozsah nebo dojde k selhání ventilátoru).                 | Zajistěte, aby neexistovaly žádné z následujících podmínek:<br>* Chladicí ventilátor byl odebrán nebo se nezdařil. <br>* Systémové pokrytí, vzduchový Shroud, modul pro vyplňování paměti nebo znaková závorka pro zálohování se odeberou.<br>* Okolní teplota je příliš vysoká.<br>* Externí tok toků je zablokován.                                                 |
|          | <br>Indikátor elektroinstalace  | <br>Indikátor změní plnou žlutou, pokud systém dojde k chybě elektrického napájení (například napětí mimo rozsah nebo neúspěšná jednotka napájecího napájení (PSU) nebo regulátor napětí. | 1. Prohlédněte si protokol událostí systému nebo systémové zprávy pro konkrétní problém.<br>2. Pokud protokol indikuje problém s PSU, podívejte se na indikátor LED na PSU.<br>3. zaPSUte.                                                                                                                                              |
|          | <br>Indikátor paměti      | <br>Indikátor změní plnou žlutou žlutou, pokud dojde k chybě paměti.                                                                                                                       | 1. Ověřte protokol událostí systému nebo systémové zprávy pro umístění nezdařené paměti.<br>2. připraví paměťový modul.                                                                                                                                                                                                  |
|          | <br>Indikátor PCIe      | <br>Indikátor změní plnou žlutou, pokud dojde k chybě karty PCIe.                                                                                                          | 1. restartujte systém.<br>2. Aktualizujte všechny požadované ovladače pro kartu PCIe.<br>3. přeinstalujte kartu.                                                                                                                                                                                                                    |

## <a name="power-supply-unit-indicator-codes"></a>Kódy indikátorů jednotky zdroje napájení

Jednotky napájení na AC (PSUs) mají světelný průhledný popisovač, který slouží jako ukazatel. Řadič domény PSUs má indikátor LED, který slouží jako ukazatel. Tento indikátor zobrazuje, zda je k dispozici napájení, nebo došlo k chybě napájení.

Na následujícím obrázku vidíte PSU AC.

Obrázek 12. PSU AC

![Diagram, který zobrazuje PSU AC s uvedeným indikátorem LED.](media/image-83.png)

Kódy ukazatelů jsou definovány v následující tabulce.

Tabulka 7 Kódy indikátorů AC PSU

| Kódy indikátoru napájení         | Podmínka                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Green                         | K PSU je připojen platný zdroj napájení a PSU je funkční.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| Blikající oranžová                | Označuje problém s PSU.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Neosvětlené               | Napájení není připojeno k PSU.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| Blikající zelená                | Při aktualizaci firmwaru PSU se popisovač PSU blikající zeleně. <br>Neodpojujte napájecí kabel nebo odpojte PSU při aktualizaci firmwaru. Pokud je aktualizace firmwaru přerušena, PSUs nefunguje.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| Blikání zelených a vypnutých  | Po připojení PSU se obslužná rutina PSU blikající zeleně o frekvenci 4 Hz a vypne se. To znamená, že PSU nesouhlasí s ohledem na efektivitu, sadu funkcí, stav nebo podporované napětí. <br><br>Pokud jsou nainstalovány dvě PSUs, musí mít PSUs stejný typ popisku; například jmenovka s rozšířeným výkonem (EPP). Kombinace PSUs z předchozích generací serverů není podporovaná, i když PSUs má stejné hodnocení napájení. Výsledkem je stav neshody PSU nebo selhání zapnutí systému. <br>Při opravě PSU neshody nahraďte pouze PSU blikajícím indikátorem. Výměna PSU, aby mohl spárovat pár, může způsobit chybový stav a <br>neočekávané vypnutí systému. Chcete-li přejít z vysoké výstupní konfigurace na nízkou výstupní konfiguraci nebo naopak, je nutné vypnout systém. <br>AC PSUs podporuje vstupní napětí 240 V a 120 V s výjimkou Titan PSUs, která podporuje pouze 240 V. Pokud dvě totožná PSUs obdrží různá vstupní napětí, můžou vycházet z různých příkonů a aktivovat neshodu. <br>Pokud se používají dva PSUs, musí být stejného typu a mít stejný maximální výstupní výkon. <br>Kombinování AC a DC PSUs není podporované a Trigger se neshoduje. |
