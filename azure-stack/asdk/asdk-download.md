---
title: Stažení a extrakci Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: Popisuje, jak stáhnout a extrahovat Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 08/10/2018
ms.openlocfilehash: 90698c6d08d672147a9a7cc93aad4f5f2cfd2619
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617443"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Stažení a extrakci Azure Stack Development Kit (ASDK)
Až se ujistíte, že hostitelského počítače development kit splňuje základní požadavky na instalaci ASDK, dalším krokem je ke stažení a extrahování balíčku pro nasazení ASDK zobrazíte Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Stáhněte si ASDK
1. Před zahájením stahování, ujistěte se, že váš počítač splňuje následující požadavky:

   - Počítač musí mít alespoň 60 GB volného místa k dispozici na čtyřech samostatných, stejné logické pevné disky kromě na disk s operačním systémem.
   - [Rozhraní .NET framework 4.6 (nebo novější)](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) musí být nainstalována.

2. [Přejděte na stránku Začínáme](https://azure.microsoft.com/overview/azure-stack/try/?v=try) kde lze stáhnout Azure Stack Development Kit, zadejte své údaje a klikněte na **odeslat**.
3. Stáhněte a spusťte [kontrola nasazení Azure Stack Development Kit](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) skript požadovaných součástí. Tento skript samostatné prochází kontroly požadavků provádí nastavení pro Azure Stack Development Kit. Poskytuje způsob, jak potvrďte, že jsou splňuje požadavky na hardware a software, než si stáhnete balíček větší pro Azure Stack Development Kit.
4. V části **stáhnout software**, klikněte na tlačítko **Azure Stack Development Kit**.

   > [!NOTE]
   > Stažení ASDK (AzureStackDevelopmentKit.exe) je přibližně 10GB.

## <a name="extract-the-asdk"></a>Extrahovat ASDK
1. Po dokončení stahování, klikněte na tlačítko **spustit** spustit vlastní Extraktor ASDK (AzureStackDevelopmentKit.exe).
2. Přečtěte si a přijměte zobrazená licenční smlouvě se společností **licenční smlouvy** Self-Extractor průvodce a pak klikněte na stránce **Další**.
3. Projděte si informace o prohlášení o ochraně osobních údajů zobrazené na **důležité informace** Self-Extractor průvodce a pak klikněte na stránce **Další**.
4. Vyberte umístění pro instalační soubory služby Azure Stack extrahovat on **vyberte cílové umístění** Self-Extractor průvodce a pak klikněte na stránce **Další**. Výchozí umístění je *aktuální složce*\Azure Stack Development Kit. 
5. Zkontrolujte souhrn na cílové umístění **připraveno k extrahování** Self-Extractor průvodce a pak klikněte na stránce **extrahovat** extrahovat CloudBuilder.vhdx (přibližně 28 GB) a ThirdPartyLicenses.rtf soubory. Tento proces trvá delší dobu.
6. Zkopírovat nebo přesunout soubor CloudBuilder.vhdx kořen jednotky C:\ (C:\CloudBuilder.vhdx) na hostitelském počítači ASDK.

> [!NOTE]
> Po extrahování souborů můžete odstranit. Soubor EXE a. Soubory BIN obnovení místa na pevném disku. Nebo můžete tyto soubory můžete zálohovat, takže nemusíte stahovat soubory znovu, pokud budete muset znovu nasadit ASDK.


## <a name="next-steps"></a>Další postup
[Příprava hostitelském počítači ASDK](asdk-prepare-host.md)