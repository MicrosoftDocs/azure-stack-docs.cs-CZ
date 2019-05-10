---
title: Integrované systémy pro nasazení list pro službu Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat a používat nástroj pro nasazení listu pro nasazení Azure Stack.
services: azure-stack
documentationcenter: ''
author: wamota
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: d75915f110b6316f4621f66b1f91b010f735d165
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172668"
---
# <a name="deployment-worksheet-for-azure-stack-integrated-systems"></a>List nasazení pro integrované systémy Azure Stack

List nasazení Azure Stack je aplikace Windows Forms, který agreguje všechny informace potřebné pro zavedení a rozhodnutí, která na jednom místě. Můžete dokončit list nasazení během procesu plánování a projděte si před zahájením nasazení.

Informace vyžadované list obsahuje informace sítě, zabezpečení a identity. Vyžaduje důležité rozhodnutí, které může být nutné znalostní báze v mnoha různých oblastech; Proto můžete chtít poraďte se s týmy, aby bylo možné dokončit list má odborné znalosti v těchto oblastech.

Při vyplňování listu, může být potřeba udělat nějaké změny konfigurace před nasazením do prostředí vaší sítě. To může zahrnovat rezervace adresní prostory IP adres pro Azure Stack řešení a konfigurace směrovače, přepínače a brány firewall, Příprava připojení k nové řešení Azure Stack.

> [!NOTE]
> Další informace o tom, jak dokončit listu nástroj pro nasazení najdete v tématu [Tento článek v dokumentaci k Azure Stack](azure-stack-datacenter-integration.md).

[![List nasazení](media/azure-stack-deployment-worksheet/depworksheet.png "list nasazení")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>Instalace modulu prostředí Windows PowerShell

Pro každou verzi list nasazení je nutné provést jednorázovou instalace modulu prostředí Powershell pro každý počítač, na který chcete použít list nasazení.

> [!NOTE]  
> Musí být počítač připojený k Internetu pro tuto metodu pro práci.

1. Otevřete řádku Powershellu se zvýšenými oprávněními.

2. V okně Powershellu nainstalujte modul z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/):

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

Pokud se zobrazí zpráva o instalaci z nedůvěryhodného úložiště, stiskněte **Y** a pokračujte v instalaci.

## <a name="use-the-deployment-worksheet-tool"></a>Pomocí nástroje deployment list

Spustit a používat list nasazení na počítači, do které jste nainstalovali modul prostředí PowerShell listu nasazení, proveďte následující kroky:

1. Spusťte prostředí Windows PowerShell (nepoužívejte prostředí PowerShell ISE, protože může dojít k neočekávaným výsledkům). Není nutné spustit PowerShell jako správce.

2. Import **AzS.Deployment.Worksheet** modul prostředí PowerShell:

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. Po importu modulu spuštění list nasazení:

   ```PowerShell
   Start-DeploymentWorksheet
   ```

List nasazení se skládá ze samostatných kartách k shromažďování údajů o nastavení prostředí, jako například **nastavení zákazníka**, **nastavení sítě**, a **jednotka škálování #**. Je nutné zadat všechny hodnoty (s výjimkou žádné označené **volitelné**) na všechny karty před jakoukoli konfiguraci je možné generovat datové soubory. Byly zadány všechny požadované hodnoty do nástroje, můžete použít **akce** nabídky **Import**, **exportovat**, a **generovat**. Soubory JSON pro nasazení jsou následující:

**Import**: Umožňuje importovat Azure Stack soubor konfiguračních dat (ConfigurationData.json), který vygeneroval tento nástroj nebo vytvořených jakékoli předchozí verzi deployment list. Provedením importu obnoví formuláře a odstraňuje všechny dříve zadané nastavení nebo vygenerovala data.

**Export**: Ověří údaje, které aktuálně zadali do formuláře, generuje podsítí protokolu IP a přiřazení a pak uloží obsah jako soubory ve formátu JSON konfigurace. Tyto soubory pak můžete vygenerovat konfiguraci sítě a nainstalovat Azure Stack.

**Generovat**: Ověří aktuálně zadané údaje a generuje mapy sítě bez export souborů JSON nasazení. Pokud jsou vytvořeny dva nových kartách **generovat** úspěšné: **Souhrn podsítě** a **přiřazení IP**. Můžete analyzovat data na těchto kartách zajistit přiřazení sítě podle očekávání.

**Vymazat vše**: Vymaže všechny aktuálně zadané ve formulářích data a vrátí na výchozí hodnoty.

**Uložit nebo otevřít vaše pracovní probíhá**: Můžete uložit a otevřít částečně zadaných dat, protože pracujete, pomocí **soubor -> Uložit** a **soubor -> Otevřít** nabídky. Tím se liší od **Import** a **exportovat** funguje, protože vyžadují všechna data pro zadaná a ověřit. Otevřít/Uložit neověřuje a nevyžaduje, aby všechna pole, abyste zadali zahájenou práci uložte.

**Protokolování a upozorněních**: Při formuláře se může zobrazit méně důležité zprávy upozornění se zobrazí v okně Powershellu. Kritické chyby se zobrazují jako místní zpráva. Volitelné podrobné protokolování, včetně protokolu zapisují na disk, můžete povolit jako pomoc při řešení potíží.

Spusťte nástroj pomocí podrobného protokolování:

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

Uložený protokol můžete najít v aktuální uživatel **Temp** directory; například: **C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**.

## <a name="next-steps"></a>Další postup

* [Modely připojení k nasazení služby Azure Stack](azure-stack-connection-models.md)
