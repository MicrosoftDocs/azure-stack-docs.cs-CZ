---
title: Postup zálohování prostředků pomocí Azure Stack replikátoru předplatného | Microsoft Docs
description: Naučte se, jak zálohovat prostředky pomocí Azure Stack replikátoru předplatného.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 10/29/2019
ms.openlocfilehash: 97d8b417869faa84423df78bde4029b8d18f0741
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167550"
---
# <a name="how-to-back-up-resources-using-the-azure-stack-subscription-replicator"></a>Postup zálohování prostředků pomocí replikátoru předplatného Azure Stack

K kopírování prostředků mezi odběry Azure Stack můžete použít skript prostředí PowerShell pro Azure Stack předplatné. Skript replikátoru načte a znovu sestaví Azure Resource Manager prostředky z různých předplatných Azure a Azure Stack. Tento článek se zabývá tím, jak skript funguje, jak můžete použít skript, a poskytuje odkaz na operace ve skriptu.

## <a name="subscription-replicator-overview"></a>Přehled replikátoru předplatného

Replikátor předplatných Azure (V3) byl navržen jako modulární. Tento nástroj používá základní procesor, který orchestruje replikaci prostředků. Nástroj navíc podporuje přizpůsobitelné procesory, které fungují jako šablony pro kopírování různých typů prostředků. 

Základní procesor se skládá z následujících tří skriptů:

- **resource_retriever. ps1**

    - Generuje složky pro ukládání výstupních souborů.

    - Nastaví kontext na zdrojové předplatné.

    - Načte prostředky a předá je spolu s **resource_processor. ps1**.

- **resource_processor. ps1**

    - Zpracuje prostředek předaný pomocí **resource_retriever. ps1**.

    - Určuje, který přizpůsobený procesor bude používat a předá prostředky.

- **post_process. ps1**

    - Post zpracuje výstup vygenerovaný přizpůsobeným procesorem, který připraví jeho nasazení do cílového předplatného.

    - Vygeneruje kód nasazení pro nasazení prostředků v cílovém předplatném.

Tři skripty řídí tok informací standardním způsobem, který umožňuje větší flexibilitu. Přidání podpory pro další zdroje například nevyžaduje, abyste změnili žádný kód v jádru procesoru.

Přizpůsobené procesory, které byly zmíněny výše, jsou `ps1` soubory, které určují, jak by měl být zpracován konkrétní typ prostředku. Název přizpůsobeného procesoru je vždy pojmenován pomocí dat typu v prostředku. Předpokládejme například, že `$vm` drží objekt virtuálního počítače se spuštěnou `$vm`. Typ by vrátil `Microsoft.Compute/virtualMachines`. To znamená, že procesor pro virtuální počítač bude pojmenován `virtualMachines_processor.ps1`, musí být přesně tak, jak se zobrazuje v metadatech prostředků, protože to je způsob, jakým základní procesor určí, který vlastní procesor se má použít.

Přizpůsobený procesor určuje, jak se má prostředek replikovat, určením informací, které jsou důležité a které určují, jak mají být tyto informace z metadat prostředků vydány. Přizpůsobený procesor potom převezme všechna extrahovaná data a použije je k vygenerování souboru parametrů, který se použije ve spojení se šablonou Azure Resource Manager k nasazení prostředku do cílového předplatného. Tento soubor parametrů je uložen v **Parameter_Files** po jeho následném zpracování pomocí post_process. ps1.

Ve struktuře souborů replikátoru s názvem **Standardized_ARM_Templates**je složka. V závislosti na zdrojovém prostředí budou nasazení používat jednu z těchto standardizovaných šablon Azure Resource Manager, jinak bude nutné vygenerovat vlastní šablonu Azure Resource Manager. V takovém případě musí přizpůsobený procesor volat generátor šablon Azure Resource Manager. V předchozím příkladu byl název generátoru šablon Azure Resource Manager pro virtuální počítače pojmenovaný **virtualMachines_ARM_Template_Generator. ps1**. Generátor šablon Azure Resource Manager zodpovídá za vytvoření vlastní šablony Azure Resource Manager na základě toho, jaké informace jsou v metadatech prostředku. Pokud má například prostředek virtuálního počítače metadata, která určují, že je členem skupiny dostupnosti, vytvoří generátor šablon Azure Resource Manager Azure Resource Manager šablonu s kódem, který určuje ID skupiny dostupnosti. virtuální počítač je součástí. Tímto způsobem, když se virtuální počítač nasadí do nového předplatného, automaticky se přidá do skupiny dostupnosti při nasazení. Tyto přizpůsobené šablony Azure Resource Manager jsou uložené ve složce **Custom_ARM_Templates** , která se nachází uvnitř složky **Standardized_ARM_Templates** . Post_processor. ps1 zodpovídá za rozhodnutí, zda nasazení má používat standardizovanou šablonu Azure Resource Manager nebo přizpůsobenou šablonu a generuje odpovídající kód nasazení.

Skript **post-Process. ps1** zodpovídá za vyčištění souborů parametrů a vytváření skriptů, které bude uživatel používat k nasazení nových prostředků. Ve fázi čištění nahradí skript všechny odkazy na ID zdrojového předplatného, ID tenanta a umístění s odpovídajícími cílovými hodnotami. Pak výstup souboru parametrů do složky **Parameter_Files** . Pak určí, zda zpracovávaný prostředek používá vlastní šablonu Azure Resource Manager nebo ne, a vygeneruje odpovídající kód nasazení, který využívá rutinu **New-AzureRmResourceGroupDeployment** . Kód nasazení se pak přidá do souboru s názvem **DeployResources. ps1** , který je uložený ve složce **Deployment_Files** . Nakonec skript určí skupinu prostředků, do které prostředek patří, a zkontroluje skript **DeployResourceGroups. ps1** , aby zjistil, zda již existuje kód nasazení k nasazení této skupiny prostředků. Pokud tomu tak není, přidá do tohoto skriptu kód pro nasazení skupiny prostředků, pokud pak neprovede žádnou akci.

### <a name="dynamic-api-retrieval"></a>Dynamické načtení rozhraní API

Tento nástroj má vestavěnou dynamickou implementaci rozhraní API, aby se k nasazení prostředků v cílovém předplatném použila nejnovější verze API poskytovatele prostředků, která je dostupná ve zdrojovém předplatném:

![Obrázek načtení rozhraní API](./media/azure-stack-network-howto-backup-replicator/image1.png)

Obrázek – načtení rozhraní API v **resource_processor. ps1**

Existuje však možnost, že verze rozhraní API poskytovatele prostředků cílového předplatného je starší než zdrojové předplatné a nepodporuje verzi poskytovanou ze zdrojového předplatného. V tomto případě bude vyvolána chyba při spuštění nasazení. Chcete-li tento problém vyřešit, aktualizujte poskytovatele prostředků v cílovém předplatném tak, aby odpovídal názvům ve zdrojovém předplatném.

### <a name="parallel-deployments"></a>Paralelní nasazení

Nástroj vyžaduje parametr pojmenovaný **Parallel**. Tento parametr přebírá logickou hodnotu určující, zda mají být načteny prostředky paralelně nasazeny. Pokud je hodnota nastavená na **true,** pak každé volání **New-AzureRmResourceGroupDeployment** bude mít příznak **-asJob** a bloky kódu, které se budou čekat na dokončení paralelních úloh, se přidají do mezi sadami nasazení prostředků na základě prostředku. druhy. Zajišťuje, aby všechny prostředky jednoho typu byly nasazeny před nasazením dalšího typu prostředku. Pokud je hodnota **paralelního** parametru nastavena na **false**, všechny prostředky budou nasazeny v sériovém tvaru.

## <a name="adding-additional-resource-types"></a>Přidání dalších typů prostředků

Přidávání nových typů prostředků je jednoduché. Vývojář musí vytvořit přizpůsobený procesor a buď šablonu Azure Resource Manager, nebo generátor šablon Azure Resource Manager. Po dokončení tohoto nástroje musí vývojář přidat typ prostředku do ValidateSet pro parametr **$ResourceType** a pole **$resourceTypes** v resource_retriever. ps1. Když přidáte typ prostředku do pole * * $resourceTypes * *, je nutné ho přidat ve správném pořadí. Pořadí pole určuje pořadí, v jakém budou prostředky nasazeny, takže mějte na paměti, že jsou zachovány závislosti. A konečně, pokud vlastní procesor používá generátor šablon Azure Resource Manager, musí přidat název typu prostředku do pole **$customTypes** v **post_process. ps1**.

## <a name="running-azure-subscription-replicator"></a>Spouští se Replikátor předplatných Azure.

Pokud chcete spustit nástroj replikátoru předplatného Azure (V3), budete muset aktivovat resource_retriever. ps1 a uvést všechny parametry. Parametr **ResourceType** má možnost zvolit **vše** , nikoli jeden typ prostředku. Pokud je vybrána možnost **vše** , resource_retriever. ps1 zpracuje všechny prostředky v pořadí tak, aby při spuštění nasazení byly nejprve nasazeny závislé prostředky. Například virtuální sítě se nasazují před virtuálními počítači, protože virtuální počítače vyžadují, aby byla virtuální síť v místě, aby se mohla správně nasadit.

Po dokončení spuštění skriptu budou existovat tři nové složky, **Deployment_Files**, **Parameter_Files**a **Custom_ARM_Templates**.

 > [!Note]  
 > Před spuštěním některého z generovaných skriptů musíte nastavit správné prostředí a přihlásit se k cílovému předplatnému (v nové Azure Stack pro ex) a nastavit pracovní adresář na složku **Deployment_Files** .

Deployment_Files budou obsahovat dva soubory **DeployResourceGroups. ps1** a **DeployResources. ps1**. Spouštěním DeployResourceGroups. ps1 se nasadí skupiny prostředků. Spuštění DeployResources. ps1 nasadí všechny prostředky, které se zpracovaly. V případě, že se nástroj spustil se **všemi** nebo **Microsoft. COMPUTE/virtualMachines** jako typ prostředku, DeployResources. ps1 vyzve uživatele, aby zadal heslo správce virtuálního počítače, které se použije k vytvoření všech virtuálních počítačů. .

### <a name="example"></a>Příklad:

1.  Spusťte skript.

    ![](./media/azure-stack-network-howto-backup-replicator/image2.png)

1.  Počkejte, než se skript spustí.

    ![](./media/azure-stack-network-howto-backup-replicator/image3.png)

1.  Zkontrolujte nově vytvořené složky:

    ![](./media/azure-stack-network-howto-backup-replicator/image4.png)

    ![](./media/azure-stack-network-howto-backup-replicator/image5.png)

1.  Nastavte kontext na cílové předplatné.

    ![](./media/azure-stack-network-howto-backup-replicator/image6.png)

1.  Zadejte `cd` pro změnu do složky **Deployment_Files** .

    ![](./media/azure-stack-network-howto-backup-replicator/image7.png)

1.  Pro nasazení skupin prostředků spusťte `DeployResourceGroups.ps1`.

    ![](./media/azure-stack-network-howto-backup-replicator/image8.png)

1.  K nasazení prostředků spusťte `DeployResources.ps1`.

    ![](./media/azure-stack-network-howto-backup-replicator/image9.png)

1.  Ke kontrole stavu spusťte `Get-Job`. Get-Job | Receive – úloha vrátí výsledky.

    ![](./media/azure-stack-network-howto-backup-replicator/image10.png)

## <a name="clean-up"></a>Vyčistit

Ve složce replicatorV3 se nachází soubor s názvem **cleanup_generated_items. ps1** – odstraní složky **Deployment_Files**, **Parameter_Files**a **Custom_ARM_Templates** a veškerý jejich obsah.

## <a name="subscription-replicator-operations"></a>Operace replikátoru předplatného

Replikátor předplatných Azure (V3) nyní může replikovat následující typy prostředků:

- Microsoft. COMPUTE/availabilitySets

- Microsoft.Compute/virtualMachines

- Microsoft.Network/loadBalancers

- Microsoft.Network/networkSecurityGroups

- Microsoft.Network/publicIPAddresses

- Microsoft. Network/routeTables

- Microsoft.Network/virtualNetworks

- Microsoft. Network/virtualNetworkGateways

- Microsoft.Storage/storageAccounts

Při spuštění nástroje se **všemi** typy prostředků se při replikaci a nasazení použijí následující pořadí (v níže uvedeném případě všechny prostředky mají replikovanou konfiguraci, tj. SKU, nabídky atd.):

- Microsoft.Network/virtualNetworks

    - Replikuje: – všechny adresní prostory – všechny podsítě

- Microsoft. Network/virtualNetworkGateways

    - Replikace:-Konfigurace veřejné IP adresy-konfigurace podsítě-typ sítě VPN – typ brány

- Microsoft. Network/routeTables

- Microsoft.Network/networkSecurityGroups

    - Replikuje:-všechna pravidla zabezpečení příchozí a odchozí

- Microsoft.Network/publicIPAddresses

- Microsoft.Network/loadBalancers

    - Replikuje: – privátní IP adresy – konfigurace veřejné IP adresy – konfigurace podsítě
    
- Microsoft. COMPUTE/availabilitySets

    - Replikuje:-počet domén selhání – počet aktualizačních domén

- Microsoft.Storage/storageAccounts

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

            > ![Note]  
            > Only creates managed disks for OS disk and data disks, no support for using storage accounts currently

### <a name="limitations"></a>Omezení

Nástroj může replikovat prostředky z jednoho předplatného do jiného, pokud poskytovatelé prostředků cílového předplatného podporují všechny prostředky a možnosti, které se replikují ze zdrojového předplatného.

Aby se zajistila úspěšná replikace, zajistěte, aby verze poskytovatele prostředků cílového předplatného byly stejné jako u zdrojového předplatného.

Při replikaci z komerčního Azure do komerčního prostředí Azure nebo z jednoho předplatného v rámci Azure Stack do jiného předplatného v rámci stejné Azure Stack budou při replikaci účtů úložiště k dispozici problémy. Důvodem je požadavek na pojmenovávání účtu úložiště, že všechny názvy účtů úložiště jsou jedinečné ve všech komerčních Azure nebo ve všech předplatných v Azure Stack oblasti nebo instanci. Replikace účtů úložiště napříč různými instancemi Azure Stack bude úspěšná, protože zásobníky jsou samostatné oblasti/instance.

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro Azure Stack sítě](azure-stack-network-differences.md)  