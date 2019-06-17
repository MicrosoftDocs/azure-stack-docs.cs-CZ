---
title: Capacity Planner služby Azure Stack | Dokumentace Microsoftu
description: Další informace o plánování kapacity pro nasazení Azure Stack.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 98dafb9df568e5e14e5f1890103d0261e07beb98
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131355"
---
# <a name="azure-stack-capacity-planner"></a>Capacity Planner služby Azure Stack

Azure Stack Capacity Planner je, že tabulky, který ukazuje, jak různé přidělování výpočetních prostředků by vešla výběr nabídky hardwaru. 

## <a name="worksheet-descriptions"></a>Popisy list
Následující tabulka popisuje každý list nástroje v Capacity Planner Azure Stack, který si můžete stáhnout z [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner). 

|Název listu|Popis|
|-----|-----|
|Verze právní omezení|Účelem kalkulačky, číslo verze a datum vydání.|
|Pokyny|Podrobné pokyny pro model plánování kapacity pro kolekci virtuálních počítačů (VM).|
|DefinedSolutionSKUs|Tabulka s definicemi až o pěti hardwaru. Položky jsou příklady. Změňte podrobnosti tak, aby odpovídaly konfigurace systému v úvahu.|
|DefineByVMFootprint|Najdete vhodný hardware skladové položky porovnáním konfigurace pomocí různých velikostí a množství virtuálních počítačů.|
|DefineByWorkloadFootprint|Najdete vhodný hardware SKU tak, že vytvoříte kolekci úloh služby Azure Stack.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs pokyny
Tento sešit obsahuje příklady definice až o pěti hardwaru. Změnit podrobnosti tak, aby odpovídaly konfigurace systému v úvahu.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Výběr hardwaru poskytuje oprávnění hardwarových partnerů
Azure Stack se dodává jako integrovaný systém se softwarem nainstalovaným partneři řešení. Partneři řešení poskytují jejich vlastní autoritativní verze nástroje pro plánování kapacity služby Azure Stack. Pomocí těchto nástrojů pro poslední diskuse o kapacitě řešení.

### <a name="multiple-ways-to-model-computing-resources"></a>Několik způsobů, jak výpočetních prostředků modelu
Modelování v rámci Azure Stack Capacity Planner prostředků závisí na různých velikostí virtuálních počítačů Azure Stack. Virtuální počítače rozsahu od 0 po nejmenší, základní až po největší, Standard_Fsv2. Výpočetní přidělení prostředků lze modelovat dvěma různými způsoby:

- Vyberte konkrétní hardware nabídky a kombinace různým prostředkům, které bude vyhovovat. 

- Vytvořit konkrétní kombinaci přidělení virtuálního počítače a nechat kalkulačky prostředků Azure zobrazit, které se tyto skladové položky k dispozici hardwaru schopný zajistit podporu této konfigurace virtuálního počítače.

Tento nástroj nabízí dvě metody přidělování prostředků virtuálních počítačů: buď jako jednu jedinou kolekci přidělení prostředků virtuálního počítače nebo jako kolekce až šest různé úlohy konfigurace. Každá úloha konfigurace může obsahovat různé přidělení dostupné prostředky virtuálních počítačů. V následujících částech mít podrobné pokyny k vytvoření a použití všech těchto modelů přidělení. Pouze hodnoty obsažené v jiných pozadí označeno šedou barvou buňky nebo v rámci SKU rozevírací seznamech tohoto listu, by měl být upraven. Změny provedené v rámci Stínované buňky by mohly narušit prostředků výpočty.


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint pokyny
Chcete-li vytvořit model s využitím jedné kolekce různé velikosti a množství virtuálních počítačů, vyberte **DefineByVMFootprint** kartu a postupujte podle těchto kroků:

1. V pravém horním rohu tohoto listu pomocí ovládací prvky zadané rozevírací seznamu vyberte počáteční počet serverů (mezi 4 a 16), které chcete nainstalovat v každém hardwaru systému (SKU). Tento počet serverů můžete změnit kdykoli během procesu modelování, chcete-li zjistit, jak to ovlivní celkové dostupné prostředky pro váš model přidělení prostředků.
2. Pokud chcete k modelování různých přidělení prostředků virtuálních počítačů na jeden konkrétní hardwarové konfigurace, najít modré rozevírací seznam pole pod **aktuální SKU** popisku v pravém horním rohu stránky. Stáhněte dolů toto pole se seznamem a vyberte požadovaný hardware SKU.
3. Nyní jste připraveni začít přidávat různé velikosti virtuálních počítačů do modelu. Zahrnout konkrétní typ virtuálního počítače, zadejte hodnotu množství do modré pole osnovy nalevo od položky tohoto virtuálního počítače.

   > [!NOTE]
   > Celková velikost úložiště virtuálního počítače se odkazuje na celkovou kapacitu datového disku virtuálního počítače (počet podporovaných disků vynásobené maximální kapacitu jednoho disku [1 TB]). Podle konfigurace ukazatele, jsme naplněné tabulce dostupné konfigurace úložiště, takže si můžete zvolit požadovanou úroveň prostředku úložiště pro každý virtuální počítač Azure Stack. Je důležité si uvědomit, že můžete přidat nebo změnit tabulku dostupné konfigurace úložiště podle potřeby.<br><br>Začíná na každý virtuální počítač původně přiřazené místní dočasné úložiště. Tak, aby odrážely dynamické zajišťování dočasného úložiště, můžete změnit temp místní číslo k ničemu v rozevírací nabídce, včetně dočasného úložiště o maximální povolená velikost.

4. Jak budete přidávat virtuální počítače, zobrazí se vám grafy, které se zobrazí dostupné zdroje SKU změna. To umožňuje vidět jejich vliv přidávání různé velikosti a množství virtuálních počítačů během procesu modelování. Dalším způsobem, jak zobrazit změny se a sledujte **spotřebovaná** a **stále k dispozici** čísla, které jsou uvedeny přímo pod seznamem dostupných virtuálních počítačů. Tato čísla odrážejí hodnoty podle aktuálně vybraného hardwaru SKU.
5. Až vytvoříte sadu virtuálních počítačů, najdete doporučené hardwarové SKU tak, že vyberete **navrhované SKU**, v pravém horním rohu stránky přímo pod **aktuální SKU** popisek. Pomocí tohoto tlačítka můžete potom změnit konfiguraci virtuálního počítače a podívejte se, jaké hardware podporuje každou konfiguraci.


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint pokyny
Chcete-li vytvořit model pomocí kolekce úloh služby Azure Stack, vyberte **DefineByWorkloadFootprint** kartu a postupujte podle tohoto pořadí kroků. Vytvoření úlohy služby Azure Stack pomocí dostupných prostředků virtuálního počítače.   

> [!TIP]
> Chcete-li změnit velikost zadaná úložiště pro virtuální počítač Azure Stack, přečtěte si poznámku z kroku 3 v předchozí části.

1. V pravém horním rohu tohoto listu pomocí ovládací prvky zadané rozevírací seznamu vyberte počáteční počet serverů (mezi 4 a 16), které chcete nainstalovat v každém hardwaru systému (SKU).
2. Pokud chcete k modelování různých přidělení prostředků virtuálních počítačů na jeden konkrétní hardwarové konfigurace, najít modré rozevírací seznam pole pod **aktuální SKU** popisku v pravém horním rohu stránky. Stáhněte dolů toto pole se seznamem a vyberte požadovaný hardware SKU.
3. Vybrat vhodnou velikost pro všechny požadované virtuální počítače Azure Stack **DefineByVMFootprint** stránce, jak je popsáno v kroku 3 v předchozí části. Velikost úložiště na virtuální počítač je definována v listu DefineByVMFootprint.
4. Nahoře vlevo od z **DefineByWorkloadFootprint** stránce, vytvořit konfigurace pro typy až šest různých úloh. Zadejte množství jednotlivých virtuálních počítačů typu obsažené v rámci úlohy. To provedete tak, že číselné hodnoty do sloupce přímo pod názvem této úlohy. Můžete změnit názvy úlohy podle typu úlohy, které budou podporované v této konkrétní konfiguraci.
5. Můžete zahrnout konkrétní množství jednotlivých typů úloh tak, že zadáte hodnotu v dolní části sloupce, hned pod tímto textem **množství** popisek.
6. Pokud jste vytvořili typy úloh a množství, vyberte **navrhované SKU** v pravém horním rohu stránky přímo pod **aktuální SKU** popisek. Zobrazí nejmenší SKU s dostatkem prostředků pro podporu tohoto celkové konfiguraci úloh.
7. Můžete provést další modelování tak, že upravíte počet serverů vybraných pro hardware SKU, nebo změna přidělení virtuálního počítače nebo množství v rámci konfigurace vaší úlohy. Přidružené grafy zobrazují okamžitou zpětnou vazbu, zobrazuje, jak provedené změny ovlivní celkové spotřeby prostředků.
8. Jakmile budete spokojeni se změnami, vyberte **navrhované SKU** znovu zobrazíte SKU navržené pro novou konfiguraci. Můžete také vybrat rozevírací nabídky vyberte požadovanou SKU.

## <a name="next-steps"></a>Další postup
Další informace o [aspekty integrace datových center pro službu Azure Stack](azure-stack-datacenter-integration.md).
