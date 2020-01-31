---
title: Capacity Planner centra Azure Stack
description: Přečtěte si o plánování kapacity pro Azure Stack nasazení centra.
author: ihenkel
ms.topic: article
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 6c5625c3161650d1e764df315b7b2390cd84f3f1
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2020
ms.locfileid: "76889775"
---
# <a name="azure-stack-hub-capacity-planner"></a>Capacity Planner centra Azure Stack

Capacity Planner centra Azure Stack je tabulka, která ukazuje, jakým způsobem se různé alokace výpočetních prostředků vejdou na výběr hardwarových nabídek. 

## <a name="worksheet-descriptions"></a>Popisy listů
Následující tabulka popisuje každý list v Capacity Planner centra Azure Stack, který se dá stáhnout z [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner). 

|Název listu|Popis|
|-----|-----|
|Omezení verze|Účel kalkulačky, číslo verze a datum vydání|
|Pokyny|Podrobné pokyny pro modelování plánování kapacity pro kolekci virtuálních počítačů (VM).|
|DefinedSolutionSKUs|Tabulka s až pěti definicemi hardwaru. Tyto položky jsou příklady. Změňte podrobnosti tak, aby odpovídaly zadaným konfiguracím systému.|
|DefineByVMFootprint|Vyhledejte příslušnou skladovou jednotku hardwaru porovnáním konfigurací s různými velikostmi a množstvím virtuálních počítačů.|
|DefineByWorkloadFootprint|Vyhledejte příslušnou skladovou jednotku hardwaru vytvořením kolekce úloh Azure Stack hub.|
|  |  |

## <a name="definedsolutionskus-instructions"></a>Pokyny pro DefinedSolutionSKUs
Tento list obsahuje až pět příkladů definice hardwaru. Změňte podrobnosti tak, aby odpovídaly dotyčným konfiguracím systému.

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Výběry hardwaru poskytované autorizovanými hardwarovými partnery
Centrum Azure Stack se doručuje jako integrovaný systém se softwarem nainstalovaným partnery řešení. Partneři řešení poskytují vlastní autoritativní verze nástrojů pro plánování kapacity centra Azure Stack. Použijte tyto nástroje pro konečné diskuze o kapacitě řešení.

### <a name="multiple-ways-to-model-computing-resources"></a>Více způsobů modelování výpočetních prostředků
Modelování prostředků v rámci centra Azure Stack Capacity Planner závisí na různých velikostech Azure Stackch virtuálních počítačů centra. Rozsah virtuálních počítačů je větší než nejmenší, základní hodnota 0, až do největší Standard_Fsv2. Přidělení výpočetních prostředků můžete modelovat dvěma různými způsoby:

- Vyberte konkrétní nabídku hardwaru a podívejte se, které kombinace různých prostředků se vejdou. 

- Vytvořte specifickou kombinaci přidělení virtuálních počítačů a nechte si kalkulačku prostředků Azure Ukázat, které dostupné hardwarové SKU jsou schopné podporovat tuto konfiguraci virtuálních počítačů.

Tento nástroj poskytuje dvě metody přidělování prostředků virtuálních počítačů: buď jako jednu kolekci přidělení prostředků virtuálních počítačů, nebo jako kolekci až šesti různých konfigurací úloh. Každá konfigurace úlohy může obsahovat jiné přidělení dostupných prostředků virtuálního počítače. V dalších částech najdete podrobné pokyny k vytvoření a použití každého z těchto modelů přidělování. V tomto listu by měly být upraveny pouze hodnoty, které jsou obsaženy v buňkách, které nejsou ve stínovém pozadí, nebo v rámci seznamu vyžádaného výpisu SKU. Změny provedené v šedě vybarvených buňkách mohou přerušit výpočty prostředků.


## <a name="definebyvmfootprint-instructions"></a>Pokyny pro DefineByVMFootprint
Pokud chcete vytvořit model pomocí jedné kolekce různých velikostí a množství virtuálních počítačů, vyberte kartu **DefineByVMFootprint** a postupujte podle těchto kroků:

1. V pravém horním rohu tohoto listu použijte ovládací prvky poskytnutého rozevíracího seznamu k výběru počátečního počtu serverů (mezi 4 a 16), které chcete nainstalovat do každého hardwarového systému (SKU). Tento počet serverů se dá kdykoli změnit během procesu modelování, abyste viděli, jak to ovlivní celkové dostupné prostředky pro model přidělování prostředků.
2. Pokud chcete modelovat různá přidělení prostředků virtuálních počítačů na jednu konkrétní hardwarovou konfiguraci, Najděte modrý rozevírací seznam, který je v pravém horním rohu stránky přímo pod popiskem **aktuální SKU** . Vytáhnete tento seznam a vyberte požadovanou skladovou jednotku hardwaru.
3. Nyní jste připraveni začít přidávat různé velikosti virtuálních počítačů do modelu. Chcete-li zahrnout konkrétní typ virtuálního počítače, zadejte hodnotu množství do modrého obrysového pole nalevo od položky virtuálního počítače.

   > [!NOTE]
   > Celkové úložiště virtuálních počítačů odkazuje na celkovou kapacitu datového disku virtuálního počítače (počet podporovaných disků vynásobený maximální kapacitou jednoho disku [1 TB]). Na základě indikátorů konfigurace jsme nastavili tabulku dostupných konfigurací úložiště, takže můžete zvolit požadovanou úroveň prostředku úložiště pro každý virtuální počítač centra Azure Stack. Je ale důležité si uvědomit, že v případě potřeby můžete přidat nebo změnit tabulku dostupných konfigurací úložiště.<br><br>Každý virtuální počítač začíná počátečním přiřazeným místním dočasným úložištěm. Aby odrážela dynamické zajišťování dočasného úložiště, můžete v rozevírací nabídce změnit číslo místního a dočasného pole, včetně maximální povolené velikosti dočasného úložiště.

4. Při přidávání virtuálních počítačů se zobrazí grafy, které zobrazují dostupné prostředky SKU, které se mění. To vám umožní zobrazit vliv přidávání různých velikostí a množství virtuálních počítačů během procesu modelování. Dalším způsobem, jak zobrazit změny v důsledku změn, je sledovat **spotřebované** a **stále dostupná** čísla, která jsou uvedená přímo pod seznamem dostupných virtuálních počítačů. Tato čísla odrážejí odhadované hodnoty na základě aktuálně vybrané skladové položky hardwaru.
5. Po vytvoření sady virtuálních počítačů můžete najít navrhovanou SKLADOVOU položku hardwaru výběrem možnosti **navržená SKU**, v pravém horním rohu stránky přímo pod popiskem **aktuální SKU** . Pomocí tohoto tlačítka můžete upravit konfigurace virtuálních počítačů a zjistit, který hardware podporuje jednotlivé konfigurace.


## <a name="definebyworkloadfootprint-instructions"></a>Pokyny pro DefineByWorkloadFootprint
Pokud chcete vytvořit model pomocí kolekce úloh Azure Stack centra, vyberte kartu **DefineByWorkloadFootprint** a postupujte podle těchto kroků. Úlohy centra Azure Stack vytvoříte pomocí dostupných prostředků virtuálních počítačů.   

> [!TIP]
> Pokud chcete změnit poskytnutou velikost úložiště pro virtuální počítač centra Azure Stack, přečtěte si poznámku z kroku 3 v předchozí části.

1. V pravém horním rohu tohoto listu použijte ovládací prvky poskytnutého rozevíracího seznamu k výběru počátečního počtu serverů (mezi 4 a 16), které chcete nainstalovat do každého hardwarového systému (SKU).
2. Pokud chcete modelovat různá přidělení prostředků virtuálních počítačů na jednu konkrétní hardwarovou konfiguraci, Najděte modrý rozevírací seznam, který je v pravém horním rohu stránky přímo pod popiskem **aktuální SKU** . Vytáhnete tento seznam a vyberte požadovanou skladovou jednotku hardwaru.
3. Na stránce **DefineByVMFootprint** vyberte odpovídající velikost úložiště pro každý z vašich požadovaných virtuálních počítačů centra Azure Stack, jak je popsáno v kroku tři předchozí části. Velikost úložiště pro každý virtuální počítač je definovaná v DefineByVMFootprint tabulce.
4. Začněte v levém horním rohu stránky **DefineByWorkloadFootprint** a vytvořte konfigurace pro až šest různých typů úloh. Zadejte množství jednotlivých typů virtuálních počítačů obsažených v rámci této úlohy. To provedete tak, že umístíte číselné hodnoty do sloupce přímo pod název tohoto pracovního postupu. Názvy úloh můžete upravit tak, aby odrážely typ úloh, které bude tato konkrétní konfigurace podporovat.
5. Konkrétní množství jednotlivých typů úloh můžete zahrnout zadáním hodnoty v dolní části tohoto sloupce přímo pod popisek **množství** .
6. Když jste vytvořili typy a množství úloh, vyberte možnost **navrhovaná SKU** v pravém horním rohu stránky přímo pod označením **aktuální SKU** . Zobrazí se nejmenší SKU s dostatečnými prostředky pro podporu této celkové konfigurace úloh.
7. Můžete provádět další modelování úpravou počtu serverů vybraných pro skladovou jednotku hardwaru nebo změnou přidělení nebo množství virtuálních počítačů v rámci konfigurací úloh. Přidružené grafy zobrazují okamžitou zpětnou vazbu, která ukazuje, jak vaše změny ovlivňují celkovou spotřebu prostředků.
8. Až budete s vašimi změnami spokojeni, znovu vyberte **navrhovaná SKU** znovu, abyste zobrazili SKU navrženou pro novou konfiguraci. Můžete také vybrat rozevírací nabídku a vybrat požadovanou SKU.

## <a name="next-steps"></a>Další kroky
Seznamte se s [informacemi o integraci Datacenter pro centrum Azure Stack](azure-stack-datacenter-integration.md).
