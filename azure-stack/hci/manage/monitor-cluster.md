---
title: Monitorování Azure Stack clusterů HCI
description: Jak monitorovat Azure Stack clustery HCI, servery, virtuální počítače, jednotky a svazky pomocí centra pro správu Windows a PowerShellu.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/12/2020
ms.openlocfilehash: 9ce1dc258243e9e17458c1f70e5a852a0b56996a
ms.sourcegitcommit: a3e042c782a38ecf3baf7a64b1d492a655972f9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201996"
---
# <a name="monitor-azure-stack-hci-clusters"></a>Monitorování Azure Stack clusterů HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Existují tři způsoby, jak monitorovat Azure Stack clustery HCI a jejich základní komponenty: centrum pro správu systému Windows, [Azure monitor](azure-monitor.md)a prostředí PowerShell.

## <a name="monitor-using-windows-admin-center-dashboard"></a>Monitorování pomocí řídicího panelu centra pro správu Windows

[Nainstalujte centrum pro správu Windows](/windows-server/manage/windows-admin-center/deploy/install) na počítač nebo server pro správu a pak přidejte a připojte se ke clusteru Azure Stack HCI, který chcete monitorovat. Kritické výstrahy se výrazně zobrazují v horní části řídicího panelu centra pro správu systému Windows, jakmile se přihlásíte. Například následující snímek obrazovky indikuje, že je potřeba nainstalovat aktualizace a že má cluster jednu chybu kritického disku:

:::image type="content" source="media/monitor-cluster/dashboard-alert.png" alt-text="Příklad výstrah řídicího panelu centra pro správu systému Windows":::

## <a name="monitor-virtual-machines"></a>Monitorování virtuálních počítačů

Je důležité pochopit stav virtuálních počítačů, na kterých běží vaše aplikace a databáze. Pokud k virtuálnímu počítači není přiřazený dostatek procesoru nebo paměti pro spuštěné úlohy, může dojít k jeho zpomalení nebo k nedostupnosti aplikace. Pokud virtuální počítač odpoví na méně než tři prezenční signály po dobu pěti minut nebo déle, může dojít k problému.

Pokud chcete monitorovat virtuální počítače v centru pro správu Windows, klikněte na **virtuální počítače** v nabídce **nástroje** vlevo. Pokud chcete zobrazit úplný inventář virtuálních počítačů spuštěných v clusteru, klikněte v horní části stránky na **inventář** . Zobrazí se tabulka s informacemi o každém virtuálním počítači, včetně:

- **Název:** Název virtuálního počítače
- **Stav:** Určuje, jestli je virtuální počítač spuštěný nebo zastavený.
- **Hostitelský server:** Označuje, na kterém serveru v clusteru je virtuální počítač spuštěný.
- **Využití CPU:** Procento celkových prostředků procesoru clusteru, které virtuální počítač spotřebovává.
- **Tlak paměti:** Procento dostupných paměťových prostředků, které virtuální počítač spotřebovává.
- **Požadavek na paměť:** Množství přiřazené paměti (GB nebo MB), které virtuální počítač spotřebovává.
- **Přiřazená paměť:** Celková velikost paměti, která je přiřazena k virtuálnímu počítači.
- **Doba provozu:** Doba běhu virtuálního počítače ve dnech: hodiny: minuty: sekundy.
- **Prezenční signál:** Určuje, jestli cluster může komunikovat s virtuálním počítačem.
- **Stav zotavení po havárii:** Ukazuje, jestli je virtuální počítač přihlášený k Azure Site Recovery.

## <a name="monitor-servers"></a>Monitorování serverů

Hostitelské servery, které tvoří Azure Stack clusteru HCI, můžete monitorovat přímo z centra pro správu systému Windows. Pokud nejsou hostitelské servery nakonfigurovány s dostatečným PROCESORem nebo pamětí, aby vyžadovaly virtuální počítače prostředky, může se jednat o problém s výkonem. 

Chcete-li monitorovat servery v centru pro správu systému Windows, klikněte na tlačítko **servery** v nabídce **nástroje** vlevo. Pokud chcete zobrazit úplný Inventář serverů v clusteru, klikněte na **inventář** v horní části stránky. Zobrazí se tabulka s informacemi o jednotlivých serverech, včetně:

- **Název:** Název hostitelského serveru v clusteru.
- **Stav:** Určuje, zda je server mimo provoz.
- **Doba provozu:** Jak dlouho server byl.
- **Výrobce:** Výrobce hardwaru serveru.
- **Model:** Model serveru.
- **Sériové číslo:** Sériové číslo serveru.
- **Využití CPU:** Procento využití procesoru hostitelského serveru. Žádný server v clusteru by neměl používat více než 85 procent svého procesoru po dobu delší než 10 minut. 
- **Využití paměti:** Procento využité paměti hostitelského serveru. Pokud má server méně než 100 MB paměti, který je k dispozici 10 minut nebo déle, zvažte přidání paměti.

## <a name="monitor-volumes"></a>Monitorovat svazky

Svazky úložiště se můžou rychle vyplnit, takže je důležité je pravidelně monitorovat, aby se předešlo jakémukoli dopadu na aplikaci. Pokud chcete monitorovat svazky v centru pro správu Windows, klikněte na **svazky** v nabídce **nástroje** vlevo. Pokud chcete zobrazit úplný inventář svazků úložiště v clusteru, klikněte v horní části stránky na **inventář** . Zobrazí se tabulka s informacemi o jednotlivých svazcích, včetně:

- **Název:** Název svazku.
- **Stav:** "OK" značí, že je svazek v pořádku; v opačném případě je hlášeno upozornění nebo chyba.
- **Systém souborů:** Systém souborů na svazku (ReFS, CSVFS).
- **Odolnost:** Označuje, zda se jedná o dvoucestné zrcadlové, trojrozměrné zrcadlové nebo zrcadlové paritě.
- **Velikost:** Velikost svazku (TB/GB)
- **Fond úložiště:** Fond úložiště, do kterého svazek patří.
- **Využití úložiště:** Procento využité kapacity úložiště svazku.
- **IOPS:** Počet vstupně-výstupních operací za sekundu.

## <a name="monitor-drives"></a>Monitorovat jednotky

Azure Stack HCI virtualizuje úložiště takovým způsobem, že přijdete o jednotlivé jednotky, nebude mít významně vliv na cluster. Neúspěšné jednotky ale bude [nutné vyměnit](replace-drives.md)a jednotky mohou mít vliv na výkon vyplněním nebo zavedením latence. Pokud operační systém nemůže komunikovat s jednotkou, může být jednotka volná nebo odpojená, její konektor se pravděpodobně nezdařil nebo se může stát, že se samotná jednotka nezdařila. Systém Windows automaticky vyřadí jednotky po 15 minutách ztráty komunikace. 

Pokud chcete monitorovat jednotky v centru pro správu Windows, klikněte na **jednotky** v nabídce **nástroje** vlevo. Pokud chcete zobrazit úplný inventář jednotek v clusteru, klikněte v horní části stránky na **inventář** . Zobrazí se tabulka s informacemi o jednotlivých jednotkách, včetně:

- **Sériové číslo:** Sériové číslo jednotky
- **Stav:** "OK" značí, že je jednotka v dobrém stavu. v opačném případě je hlášeno upozornění nebo chyba.
- **Model:** Model jednotky.
- **Velikost:** Celková kapacita jednotky (TB/GB).
- **Zadejte:** Typ jednotky (SSD, HDD).
- **Používá se pro:** Označuje, jestli se jednotka používá pro mezipaměť nebo kapacitu.
- **Umístění:** Adaptér úložiště a port, ke kterému je jednotka připojená.
- **Server:** Název serveru, ke kterému je jednotka připojená.
- **Fond úložiště:** Fond úložiště, do kterého jednotka patří.
- **Využití úložiště:** Procento využité kapacity úložiště jednotky.

## <a name="add-performance-counters"></a>Přidat čítače výkonu

Pomocí nástroje sledování výkonu v centru pro správu Windows můžete zobrazit a porovnat čítače výkonu pro Windows, aplikace nebo zařízení v reálném čase.

1. V nabídce **nástroje** na levé straně vyberte **sledování výkonu** .
1. Kliknutím na možnost **prázdný pracovní prostor** spustíte nový pracovní prostor, nebo **obnovte předchozí** a obnovte předchozí pracovní prostor.
1. Pokud vytváříte nový pracovní prostor, klikněte na tlačítko **Přidat čítač** a vyberte jeden nebo více zdrojových serverů, které chcete monitorovat, nebo vyberte celý cluster.
1. Vyberte objekt a instanci, kterou chcete monitorovat, a také typ čítače a grafu pro zobrazení dynamických informací o výkonu.
1. Uložte pracovní prostor kliknutím na **uložit > Uložit jako** v horní nabídce.
 
Například následující snímek obrazovky ukazuje čítač výkonu s názvem "využití paměti", který zobrazuje informace o paměti v rámci clusteru se dvěma uzly.

:::image type="content" source="media/monitor-cluster/performance-monitor.png" alt-text="Příklad čítače výkonu v reálném čase v centru pro správu systému Windows":::

## <a name="query-and-process-performance-history-with-powershell"></a>Dotazování a zpracování historie výkonu pomocí PowerShellu

Můžete také monitorovat Azure Stack clustery HCI pomocí rutin prostředí PowerShell, které vracejí informace o clusteru a jeho součástech. Podívejte se [na téma historie výkonu pro prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/performance-history).

## <a name="use-the-health-service-feature"></a>Použití funkce Health Service

Měla by se prozkoumat jakákoli Health Serviceá chyba v clusteru. Informace o spouštění sestav a identifikaci chyb najdete [v tématu Health Service ve Windows serveru](/windows-server/failover-clustering/health-service-overview) .

## <a name="troubleshoot-health-and-operational-states"></a>Řešení potíží se stavem a provozními stavy

Informace o stavech a provozních stavech fondů úložiště, virtuálních disků a jednotek najdete v tématu [řešení potíží s prostory úložiště a prostory úložiště s přímým přístupem stavu a provozních stavů](/windows-server/storage/storage-spaces/storage-spaces-states).

## <a name="monitor-performance-using-storage-qos"></a>Monitorování výkonu pomocí kvality služby úložiště

Technologie QoS (Quality of Service) úložiště poskytuje způsob, jak centrálně monitorovat a spravovat vstupně-výstupní operace úložiště pro virtuální počítače, aby se zmírnily problémy s vysokou úrovní hrozeb a poskytovaly konzistentní výkon. Viz [kvalita služby úložiště](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="set-up-alerts-in-azure-monitor"></a>Nastavení výstrah v Azure Monitor

Azure Stack se HCI integruje s Azure Monitor, aby uživatelům umožnila [nastavit upozornění](azure-monitor.md#setting-up-alerts-using-windows-admin-center) a bylo možné upozornit na to, že se překročí stav procesoru, kapacity disku a prahové hodnoty využití paměti, pokud se nevrátí prezenční signály, nebo pokud dojde k chybě kritické systémové chyby nebo služby stavu.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Začínáme se službou Azure Stack HCI a centrem pro správu Windows](../get-started.md)
- [Monitorování Azure Stack HCL pomocí Azure Monitor](azure-monitor.md)