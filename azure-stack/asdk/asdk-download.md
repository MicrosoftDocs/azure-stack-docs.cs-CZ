---
title: Stažení a extrahování ASDK
description: Přečtěte si, jak stáhnout a extrahovat Azure Stack Development Kit (ASDK).
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/10/2018
ms.openlocfilehash: 99c43848af1d140c6698e1f4d1ae992665c0389a
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873834"
---
# <a name="download-and-extract-the-asdk"></a>Stažení a extrahování ASDK
Až ověříte, že váš hostitelský počítač pro vývojovou sadu splňuje základní požadavky na instalaci Azure Stack Development Kit (ASDK), je dalším krokem stažení a extrakce balíčku pro nasazení ASDK, který získá Cloudbuilder. vhdx.

## <a name="download-the-asdk"></a>Stáhnout ASDK
1. Než začnete s stažením, ujistěte se, že váš počítač splňuje následující požadavky:

   - Počítač musí mít k dispozici minimálně 60 GB volného místa na disku na čtyřech samostatných a identických logických pevných discích (kromě disku operačního systému).
   - Musí být nainstalovaná [verze .NET Framework 4,6 (nebo novější)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) .

2. [Přejděte na stránku](https://azure.microsoft.com/overview/azure-stack/try/?v=try) Začínáme, kde si můžete stáhnout ASDK, zadejte podrobnosti a pak klikněte na **Odeslat**.
3. Stáhněte a spusťte [kontrolu nasazení skriptu pro](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) kontrolu požadovaných součástí ASDK. Tento samostatný skript projde kontrolami požadavků provedenými instalačním programem pro ASDK. Poskytuje způsob, jak potvrdit, že splňujete požadavky na hardware a software před stažením většího balíčku pro ASDK.
4. V části **Stáhnout software**klikněte na **Azure Stack Development Kit**.

   > [!NOTE]
   > Stahování ASDK (AzureStackDevelopmentKit. exe) je přibližně 10 GB.

## <a name="extract-the-asdk"></a>Extrahovat ASDK
1. Po dokončení stahování kliknutím na **Spustit** spusťte samočinný extrahování ASDK (AzureStackDevelopmentKit. exe).
2. Přečtěte si a přijměte zobrazenou licenční smlouvu na stránce **Licenční smlouva** průvodce pro samoobslužné extrakce a potom klikněte na tlačítko **Další**.
3. Přečtěte si informace v prohlášení o zásadách ochrany osobních údajů, které se zobrazují na stránce **důležité oznámení** průvodce pro samoobslužné extrakce, a potom klikněte na **Další**.
4. Na stránce **Vyberte cílové umístění** v průvodci pro samoobslužné extrakce vyberte umístění Azure Stack pro soubory instalačního programu, které se mají extrahovat, a pak klikněte na **Další**. Výchozím umístěním je *aktuální složka*\Azure Stack Development Kit. 
5. Zkontrolujte souhrn cílového umístění na stránce **připraveno k extrakci** průvodce pro samoobslužné extrakce a potom kliknutím na **extrahovat** rozbalte soubory CloudBuilder. vhdx (přibližně 28GB) a ThirdPartyLicenses. RTF. Dokončení tohoto procesu trvá nějakou dobu.
6. Kopírování nebo přesun souboru CloudBuilder. vhdx do kořenového adresáře C:\ jednotka (`C:\CloudBuilder.vhdx`) na hostitelském počítači s ASDK.

> [!NOTE]
> Po extrahování souborů můžete odstranit. EXE a. Soubory BIN pro obnovení místa na pevném disku. Nebo můžete tyto soubory zálohovat, aby nemuseli soubory stahovat znovu, pokud potřebujete znovu nasadit ASDK.


## <a name="next-steps"></a>Další kroky
[Příprava hostitelského počítače s ASDK](asdk-prepare-host.md)