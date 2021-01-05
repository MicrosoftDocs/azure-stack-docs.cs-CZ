---
title: Replikace prostředků napříč několika předplatnými centra Azure Stack
description: Naučte se replikovat prostředky pomocí sady Azure Stack Replikátor předplatných rozbočovače.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: f638f4a46a805d03d30512f5f32285702c67c20c
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874009"
---
# <a name="replicate-resources-using-the-azure-stack-hub-subscription-replicator"></a>Replikace prostředků pomocí replikátoru předplatného centra Azure Stack

Pomocí skriptu PowerShellu pro replikátor předplatného centra Azure Stack můžete kopírovat prostředky mezi odběry centra Azure Stack, Azure Stack přes časová razítka centra nebo mezi Azure Stack a Azure. Skript replikátoru načte a znovu sestaví Azure Resource Manager prostředky z různých předplatných Azure a centra Azure Stack. Tento článek se zabývá tím, jak skript funguje, jak můžete použít skript, a poskytuje referenční informace pro operace se skripty.

Skripty používané v tomto článku najdete v úložišti GitHub ve [vzorcích Azure Intelligent Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) . Skripty jsou ve složce [Replikátor odběrů](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/tree/master/subscription%20replicator) .

## <a name="subscription-replicator-overview"></a>Přehled replikátoru předplatného

Replikátor předplatných Azure byl navržen jako modulární. Tento nástroj používá základní procesor, který orchestruje replikaci prostředků. Nástroj navíc podporuje přizpůsobitelné procesory, které fungují jako šablony pro kopírování různých typů prostředků. 

Základní procesor se skládá z následujících tří skriptů:

- **resource_retriever.ps1**

    - Generuje složky pro ukládání výstupních souborů.

    - Nastaví kontext na zdrojové předplatné.

    - Načte prostředky a předá je **resource_processor.ps1**.

- **resource_processor.ps1**

    - Zpracuje prostředek předaný pomocí **resource_retriever.ps1**.

    - Určuje, který přizpůsobený procesor bude používat a předá prostředky.

- **post_process.ps1**

    - Post zpracuje výstup vygenerovaný přizpůsobeným procesorem, který připraví jeho nasazení do cílového předplatného.

    - Vygeneruje kód nasazení pro nasazení prostředků v cílovém předplatném.

Tři skripty řídí tok informací standardním způsobem, který umožňuje větší flexibilitu. Přidání podpory pro další zdroje například nevyžaduje, abyste změnili žádný kód v jádru procesoru.

Přizpůsobené procesory, které jsme uvedli výše, jsou `ps1` soubory, které určují, jak se má určitý typ prostředku zpracovat. Název přizpůsobeného procesoru je vždy pojmenován pomocí dat typu v prostředku. Předpokládejme například, `$vm` že obsahuje objekt virtuálního počítače, na kterém běží `$vm` . Typ by měl vracet `Microsoft.Compute/virtualMachines` . To znamená, že procesor pro virtuální počítač bude pojmenován `virtualMachines_processor.ps1` , název musí být přesně tak, jak se zobrazuje v metadatech prostředků, protože to je způsob, jakým základní procesor určí, který vlastní procesor se má použít.

Přizpůsobený procesor určuje, jak se má prostředek replikovat, určením informací, které jsou důležité a které určují, jak mají být tyto informace z metadat prostředků vydány. Přizpůsobený procesor potom převezme všechna extrahovaná data a použije je k vygenerování souboru parametrů, který se použije ve spojení se šablonou Azure Resource Manager k nasazení prostředku do cílového předplatného. Tento soubor parametrů je uložen v **Parameter_Files** po jeho následném zpracování pomocí post_process.ps1.

Ve struktuře souborů replikátoru je složka s názvem **Standardized_ARM_Templates**. V závislosti na zdrojovém prostředí budou nasazení používat jednu z těchto standardizovaných šablon Azure Resource Manager, jinak bude nutné vygenerovat vlastní šablonu Azure Resource Manager. V takovém případě musí přizpůsobený procesor volat generátor šablon Azure Resource Manager. V předchozím příkladu byl název generátoru šablon Azure Resource Manager pro virtuální počítače pojmenovaný **virtualMachines_ARM_Template_Generator.ps1**. Generátor šablon Azure Resource Manager zodpovídá za vytvoření vlastní šablony Azure Resource Manager na základě toho, jaké informace jsou v metadatech prostředku. Pokud má například prostředek virtuálního počítače metadata, která určují, že je členem skupiny dostupnosti, vytvoří generátor šablon Azure Resource Manager Azure Resource Manager šablonu s kódem, který určuje ID skupiny dostupnosti, do které je virtuální počítač součástí. Tímto způsobem, když se virtuální počítač nasadí do nového předplatného, automaticky se přidá do skupiny dostupnosti při nasazení. Tyto přizpůsobené šablony Azure Resource Manager se ukládají do složky **Custom_ARM_Templates** nacházející se ve složce **Standardized_ARM_Templates** . post_processor.ps1 zodpovídá za rozhodnutí, zda nasazení má používat standardizovanou Azure Resource Manager šablonu nebo přizpůsobenou šablonu a generuje odpovídající kód nasazení.

Skript **post-process.ps1** zodpovídá za mazání souborů parametrů a vytváření skriptů, které bude uživatel používat k nasazení nových prostředků. Ve fázi čištění nahradí skript všechny odkazy na ID zdrojového předplatného, ID tenanta a umístění s odpovídajícími cílovými hodnotami. Pak výstup souboru parametrů do složky **Parameter_Files** . Pak určí, zda zpracovávaný prostředek používá vlastní šablonu Azure Resource Manager nebo ne, a vygeneruje odpovídající kód nasazení, který využívá rutinu **New-AzResourceGroupDeployment** . Kód nasazení se pak přidá do souboru s názvem **DeployResources.ps1** uložený ve **Deployment_Files** složce. Nakonec skript určí skupinu prostředků, do které prostředek patří, a zkontroluje skript **DeployResourceGroups.ps1** , aby zjistil, zda již existuje kód nasazení pro nasazení této skupiny prostředků. Pokud tomu tak není, přidá do tohoto skriptu kód pro nasazení skupiny prostředků, pokud pak neprovede žádnou akci.

### <a name="dynamic-api-retrieval"></a>Dynamické načtení rozhraní API

Tento nástroj má vestavěnou dynamickou implementaci rozhraní API, aby se k nasazení prostředků v cílovém předplatném použila nejnovější verze API poskytovatele prostředků, která je dostupná ve zdrojovém předplatném:

![Obrázek načtení rozhraní API](./media/azure-stack-network-howto-backup-replicator/image1.png)

Obrázek – načtení rozhraní API v **resource_processor.ps1**.

Existuje však možnost, že verze rozhraní API poskytovatele prostředků cílového předplatného je starší než zdrojové předplatné a nepodporuje verzi poskytovanou ze zdrojového předplatného. V tomto případě bude vyvolána chyba při spuštění nasazení. Chcete-li tento problém vyřešit, aktualizujte poskytovatele prostředků v cílovém předplatném tak, aby odpovídal názvům ve zdrojovém předplatném.

### <a name="parallel-deployments"></a>Paralelní nasazení

Nástroj vyžaduje parametr pojmenovaný **Parallel**. Tento parametr přebírá logickou hodnotu určující, zda mají být načteny prostředky paralelně nasazeny. Pokud je hodnota nastavená na **true,** pak každé volání **New-AzResourceGroupDeployment** bude mít příznak **-asJob** a bloky kódu, které se budou čekat na dokončení paralelních úloh, se přidají do mezi sadami nasazení prostředků na základě typů prostředků. Zajišťuje, aby všechny prostředky jednoho typu byly nasazeny před nasazením dalšího typu prostředku. Pokud je hodnota **paralelního** parametru nastavena na **false**, všechny prostředky budou nasazeny v sériovém tvaru.

## <a name="add-additional-resource-types"></a>Přidat další typy prostředků

Přidávání nových typů prostředků je jednoduché. Vývojář musí vytvořit přizpůsobený procesor a buď šablonu Azure Resource Manager, nebo generátor šablon Azure Resource Manager. Po dokončení tohoto nástroje musí vývojář přidat typ prostředku do ValidateSet pro parametr **$ResourceType** a pole **$resourceTypes** v resource_retriever.ps1. Když přidáte typ prostředku do pole **$resourceTypes** , musí být přidán ve správném pořadí. Pořadí pole určuje pořadí, v jakém budou prostředky nasazeny, takže mějte na paměti, že jsou zachovány závislosti. A konečně, pokud vlastní procesor používá generátor šablon Azure Resource Manager, musí přidat název typu prostředku do pole **$customTypes** v **post_process.ps1**.

## <a name="run-azure-subscription-replicator"></a>Spustit Replikátor předplatných Azure

Pokud chcete spustit nástroj replikátoru předplatného Azure (V3), musíte aktivovat resource_retriever.ps1 a zadáte všechny parametry. Parametr **ResourceType** má možnost zvolit **vše** , nikoli jeden typ prostředku. Pokud je vybrána možnost **vše** , resource_retriever.ps1 zpracuje všechny prostředky v pořadí tak, aby po spuštění nasazení byly nejprve nasazeny závislé prostředky. Například virtuální sítě se nasazují před virtuálními počítači, protože virtuální počítače vyžadují, aby byla virtuální síť v místě, aby se mohla správně nasadit.

Po dokončení spuštění skriptu budou k dispozici tři nové složky, **Deployment_Files**, **Parameter_Files** a **Custom_ARM_Templates**.

 > [!NOTE]  
 > Před spuštěním některého z generovaných skriptů musíte nastavit správné prostředí a přihlásit se k cílovému předplatnému (v novém centru Azure Stack pro ex) a nastavit pracovní adresář na složku **Deployment_Files** .

Deployment_Files budou obsahovat dva soubory **DeployResourceGroups.ps1** a **DeployResources.ps1**. Spuštění DeployResourceGroups.ps1 nasadí skupiny prostředků. Spuštění DeployResources.ps1 nasadí všechny prostředky, které se zpracovaly. V případě, že se nástroj spustil se **všemi** nebo **Microsoft. COMPUTE/virtualMachines** jako typ prostředku, DeployResources.ps1 vyzve uživatele, aby zadal heslo správce virtuálního počítače, které se použije k vytvoření všech virtuálních počítačů.

### <a name="example"></a>Příklad

1.  Spusťte skript.

    ![Spuštění skriptu](./media/azure-stack-network-howto-backup-replicator/image2.png)

    > [!NOTE]  
    > Nezapomeňte konfigurovat zdrojový evironment a kontext předplatného pro instanci PS. 

2.  Zkontrolujte nově vytvořené složky:

    ![Kontrola složek](./media/azure-stack-network-howto-backup-replicator/image4.png)

3.  Nastavte kontext na cílové předplatné, změňte složku na **Deployment_Files**, nasaďte skupiny prostředků (spusťte skript DeployResourceGroups.ps1) a potom spusťte nasazení prostředků (spusťte skript DeployResources.ps1).

    ![Konfigurace a spuštění nasazení](./media/azure-stack-network-howto-backup-replicator/image6.png)

4.  Spusťte `Get-Job` pro kontrolu stavu. Get-Job | Receive-Job vrátí výsledky.

## <a name="clean-up"></a>Vyčištění

Ve složce replicatorV3 se nachází soubor s názvem **cleanup_generated_items.ps1** – odstraní složky **Deployment_Files**, **Parameter_Files** a **Custom_ARM_Templates** a veškerý jejich obsah.

## <a name="subscription-replicator-operations"></a>Operace replikátoru předplatného

Replikátor předplatných Azure (V3) nyní může replikovat následující typy prostředků:

- Microsoft. COMPUTE/availabilitySets

- Microsoft.Compute/virtualMachines

- Microsoft. Network/loadBalancers

- Microsoft. Network/networkSecurityGroups

- Microsoft. Network/publicIPAddresses

- Microsoft. Network/routeTables

- Microsoft. Network/virtualNetworks

- Microsoft. Network/virtualNetworkGateways

- Microsoft. Storage/storageAccounts

Při spuštění nástroje se **všemi** typy prostředků se při replikaci a nasazení použijí následující pořadí (v níže uvedeném případě všechny prostředky mají replikovanou konfiguraci, tj. SKU, nabídky atd.):

- Microsoft. Network/virtualNetworks

    - Replikuje: – všechny adresní prostory – všechny podsítě

- Microsoft. Network/virtualNetworkGateways

    - Replikace:-Konfigurace veřejné IP adresy-konfigurace podsítě-typ sítě VPN – typ brány

- Microsoft. Network/routeTables

- Microsoft. Network/networkSecurityGroups

    - Replikuje:-všechna pravidla zabezpečení příchozí a odchozí

- Microsoft. Network/publicIPAddresses

- Microsoft. Network/loadBalancers

    - Replikuje: – privátní IP adresy – konfigurace veřejné IP adresy – konfigurace podsítě
    
- Microsoft. COMPUTE/availabilitySets

    - Replikuje:-počet domén selhání – počet aktualizačních domén

- Microsoft. Storage/storageAccounts

- Microsoft.Compute/virtualMachines
    - Replikuje  
            -Datové disky (bez dat)  
            – Velikost virtuálního počítače  
            – Operační systém  
            – Konfigurace účtu úložiště diagnostiky  
            – Konfigurace veřejné IP adresy  
            – Síťové rozhraní  
            – Privátní IP adresa síťového rozhraní  
            – Konfigurace skupiny zabezpečení sítě  
            -Konfigurace sady dostupnosti  

> [!NOTE]  
> Pro disk s operačním systémem a datové disky se vytvoří jenom spravované disky. V současné době není k dispozici podpora pro použití účtů úložiště. 

### <a name="limitations"></a>Omezení

Nástroj může replikovat prostředky z jednoho předplatného do jiného, pokud poskytovatelé prostředků cílového předplatného podporují všechny prostředky a možnosti, které se replikují ze zdrojového předplatného.

Aby se zajistila úspěšná replikace, Mare zajistěte, aby verze poskytovatele prostředků cílového předplatného odpovídaly verzi zdrojového předplatného.

Při replikaci z komerčního Azure do komerčního prostředí Azure nebo z jednoho předplatného v rámci centra Azure Stack do jiného předplatného v rámci stejného centra Azure Stack budou při replikaci účtů úložiště k dispozici problémy. Důvodem je nutnost pojmenovávání účtů úložiště, že všechny názvy účtů úložiště jsou jedinečné ve všech komerčních Azure nebo ve všech předplatných v oblasti nebo instanci centra Azure Stack. Replikace účtů úložiště napříč různými instancemi centra Azure Stack bude úspěšná, protože zásobníky jsou samostatné oblasti nebo instance.



## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)  
