---
title: Sešit nasazení pro integrované systémy centra Azure Stack
description: Naučte se instalovat a používat nástroj pro nasazení sešitu Azure Stack k nasazení centra.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/19/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: 8c13121f5d591abca8a6c83771848d97cc9106db
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77700064"
---
# <a name="deployment-worksheet-for-azure-stack-hub-integrated-systems"></a>Sešit nasazení pro integrované systémy centra Azure Stack

Sešit Azure Stackho nasazení centra je model Windows Forms aplikace, která agreguje všechny nezbytné informace o nasazení a rozhodnutí na jednom místě. Během plánování můžete dokončit list nasazení a před zahájením nasazení ho zkontrolovat.

Informace požadované v listu zahrnují informace o síti, zabezpečení a identitě. Vyžaduje důležitá rozhodnutí, která mohou vyžadovat znalosti v mnoha různých oblastech; Proto byste se měli chtít poradit s týmy, které mají zkušenosti v těchto oblastech, aby bylo možné list dokončit.

Při vyplňování listu může být nutné provést některé změny konfigurace před nasazením ve vašem síťovém prostředí. To může zahrnovat rezervaci adresních prostorů IP adres pro řešení centra Azure Stack a konfiguraci směrovačů, přepínačů a bran firewall pro přípravu připojení k novému řešení centra Azure Stack.

> [!NOTE]
> Další informace o tom, jak dokončit nástroj listu nasazení, najdete [v tomto článku v dokumentaci centra Azure Stack](azure-stack-datacenter-integration.md).

[![Sešit nasazení](media/azure-stack-deployment-worksheet/depworksheet.png "Sešit nasazení")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>Instalace modulu Windows PowerShell

Pro každé vydání listu nasazení je třeba provést jednorázovou instalaci modulu prostředí PowerShell pro každý počítač, na kterém chcete použít sešit nasazení.

> [!NOTE]  
> Aby tato metoda fungovala, musí být počítač připojen k Internetu.

1. Otevřete příkazový řádek PowerShell se zvýšenými oprávněními.

2. V okně PowerShellu nainstalujte modul z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/):

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

Pokud se vám zobrazí zpráva o instalaci z nedůvěryhodného úložiště, stiskněte klávesu **Y** a pokračujte v instalaci.

## <a name="use-the-deployment-worksheet-tool"></a>Použití nástroje list nasazení

Pokud chcete spustit sešit nasazení a použít ho na počítači, na kterém máte nainstalovaný modul PowerShellu pro nasazení, proveďte následující kroky:

1. Spusťte prostředí Windows PowerShell (nepoužívejte PowerShellový ISE, protože může dojít k neočekávaným výsledkům). Není nutné spouštět prostředí PowerShell jako správce.

2. Importujte modul PowerShellu **AzS. Deployment. Worksheet** :

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. Po importu modulu otevřete sešit nasazení:

   ```PowerShell
   Start-DeploymentWorksheet
   ```

List nasazení se skládá ze samostatných karet pro shromažďování nastavení prostředí, jako je **nastavení zákazníka**, **nastavení sítě**a **škálování jednotky #**. Než bude možné vygenerovat jakékoli soubory konfiguračních dat, je nutné zadat všechny hodnoty (kromě těch, které jsou označeny jako **volitelné**) na všech kartách. Po zadání všech požadovaných hodnot do nástroje můžete pomocí nabídky **Akce** **importovat**, **exportovat**a **Generovat**. Soubory JSON vyžadované pro nasazení jsou následující:

**Import**: umožňuje importovat soubor dat konfigurace centra Azure Stack (ConfigurationData. JSON), který byl vygenerován pomocí tohoto nástroje, nebo ty, které byly vytvořeny v předchozí verzi listu nasazení. Při provádění importu se resetují formuláře a odstraní se všechna dříve zadaná nastavení nebo generovaná data.

**Export**: ověří aktuálně zadaná data do formulářů, vygeneruje podsítě IP a přiřazení a pak obsah uloží jako konfigurační soubory ve formátu JSON. Pak můžete tyto soubory použít k vygenerování konfigurace sítě a instalaci centra Azure Stack.

**Generate**: ověří aktuálně zadaná data a vygeneruje mapu sítě bez exportu souborů JSON nasazení. Pokud je **generování** úspěšné, vytvoří se dvě nové karty: **Souhrn podsítě** a **přiřazení IP adresy**. Data na těchto kartách můžete analyzovat, abyste zajistili, že budou přiřazení sítě očekávaná.

**Clear All**: vymaže všechna aktuálně zadaná data ve formulářích a vrátí je do výchozích hodnot.

**Uložte nebo otevřete svůj nedokončený průběh**: můžete uložit a otevřít částečně zadaná data, když na ní pracujete, a to pomocí **souborů >Save** a **soubor->otevřít** nabídky. To se liší od funkcí **importu** a **exportu** , protože to vyžaduje, aby byla všechna data zadávána a ověřena. Otevření nebo uložení neověřuje a nevyžaduje, aby byla zadávána všechna pole, aby bylo možné svou nedokončenou práci uložit.

**Protokolovací zprávy a upozornění**: při použití formuláře se v okně PowerShellu můžou zobrazit nekritické zprávy s upozorněním. Kritické chyby se zobrazují jako místní zpráva. Volitelné podrobné protokolování, včetně protokolu zapsaného na disk, může být umožněno pro pomoc při řešení problémů.

Postup spuštění nástroje s podrobným protokolováním:

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

Uložený protokol najdete v **dočasném** adresáři aktuálního uživatele. například: **c:\users\me\appdata\local\temp\ Microsoft_AzureStack \ DeploymentWorksheet_Log. txt**.

## <a name="next-steps"></a>Další kroky

* [Modely připojení nasazení centra Azure Stack](azure-stack-connection-models.md)
