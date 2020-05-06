---
title: Řešení potíží s ověřováním jako službou
titleSuffix: Azure Stack Hub
description: Řešení potíží s ověřováním jako služby pro centrum Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1bcca404c451190ccf1d0b82e93aea655e069044
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661392"
---
# <a name="troubleshoot-validation-as-a-service"></a>Řešení potíží s ověřováním jako službou

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Níže jsou uvedené běžné problémy, které nesouvisí s verzemi softwaru a jejich řešeními.

## <a name="local-agent"></a>Místní Agent

### <a name="the-portal-shows-local-agent-in-debug-mode"></a>Na portálu se v režimu ladění zobrazuje místní agent.

Tento problém je pravděpodobně způsoben tím, že Agent nemůže odesílat prezenční signály do služby z důvodu nestabilního síťového připojení. Prezenční signál se pošle každých pět minut. Pokud služba neobdrží prezenční signál po dobu 15 minut, služba nepovažuje agenta za neaktivní a testy se na něj už nebudou naplánovat. V souboru *Agenthost. log* , který se nachází v adresáři, ve kterém byl spuštěn Agent, se podívejte na chybovou zprávu.

> [!Note]
> Všechny testy, které jsou již spuštěny v agentovi, budou nadále spuštěny, ale pokud se prezenční signál neobnoví před ukončením testu, Agent nebude moci aktualizovat stav testu nebo Odeslat protokoly. Test se vždycky zobrazí jako **spuštěný** a bude potřeba ho zrušit.

### <a name="agent-process-on-machine-was-shut-down-while-executing-test-what-to-expect"></a>Proces agenta v počítači se při provádění testu vypnul. Co očekávat?

Pokud je proces agenta nekorektně vypnutý, bude se test, který na něm běžel, dál zobrazovat jako **spuštěný**. Příkladem nedarovaného vypnutí je restartování počítače a proces byl ukončen (CTRL + C v okně Agent se považuje za řádné vypnutí). Pokud je agent restartován, Agent aktualizuje stav testu na **zrušeno**. Pokud se agent nerestartuje, test se zobrazí jako **spuštěný** a Vy musíte test ručně zrušit.

> [!Note]
> Testy v rámci pracovního postupu mají naplánované spuštění postupně. **Probíhající** testy se nespustí, dokud testy ve **spuštěném** stavu ve stejném pracovním postupu dokončí.

## <a name="vm-images"></a>Image virtuálních počítačů

### <a name="handle-slow-network-connectivity"></a>Zpracování pomalého připojení k síti

Image PIR si můžete stáhnout do sdílené složky v místním datovém centru. A potom můžete image kontrolovat.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>Stáhnout image PIR do místního sdílení v případě pomalého síťového provozu

1. Stáhněte si AzCopy z: [vaasexternaldependencies (AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Extrahujte soubor AzCopy. zip a přejděte do adresáře `AzCopy.exe`obsahujícího.

3. Otevřete Windows PowerShell z příkazového řádku se zvýšenými oprávněními. Spusťte následující příkazy:

```powershell  
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterFullBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Server2016DatacenterCoreBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'WindowsServer2012R2DatacenterBYOL.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1404LTS.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Ubuntu1604-20170619.1.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'OpenLogic-CentOS-69-20180105.vhd' /NC:12 /V:azcopylog.log /Y
    .\azcopy.exe /Source:'https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container' /Dest:'<LocalFileShare>' /Pattern:'Debian8_latest.vhd' /NC:12 /V:azcopylog.log /Y
```

> [!Note]  
> LocalFileShare je cesta ke sdílené složce nebo místní cesta.

#### <a name="verifying-pir-image-file-hash-value"></a>Ověřuje se hodnota hash souboru obrázku PIR.

Pomocí rutiny **Get-HashFile** můžete získat hodnotu hash pro stažené soubory imagí úložiště veřejných imagí a ověřit integritu imagí.

| Název souboru | SHA256 |
|---------------------------------------|------------------------------------------------------------------|
| Server2016DatacenterFullBYOL. VHD | 6ED58DCA666D530811A1EA563BA509BF9C29182B902D18FCA03C7E0868F733E9 |
| WindowsServer2012R2DatacenterBYOL. VHD | 9792CBF742870B1730B9B16EA814C683A8415EFD7601DDB6D5A76D0964767028 |
| Server2016DatacenterCoreBYOL. VHD | 5E80E1A6721A48A10655E6154C1B90E320DF5558487D6A0D7BFC7DCD32C4D9A5 |
| Ubuntu1404LTS. VHD | B24CDD12352AAEBC612A4558AB9E80F031A2190E46DCB459AF736072742E20E0 |
| Ubuntu1604-20170619.1. VHD | C481B88B60A01CBD5119A3F56632A2203EE5795678D3F3B9B764FFCA885E26CB |
| OpenLogic-CentOS-69-20180105. VHD | C8B874FE042E33B488110D9311AF1A5C7DC3B08E6796610BF18FDD6728C7913C |
| Debian8_latest. VHD | 06F8C11531E195D0C90FC01DFF5DC396BB1DD73A54F8252291ED366CACD996C1 |

### <a name="failure-happens-when-uploading-vm-image-in-the-vaasprereq-script"></a>K selhání dojde při nahrávání image virtuálního počítače `VaaSPreReq` ve skriptu.

Nejdřív ověřte, že je prostředí v pořádku:

1. V poli DVM/skočit ověřte, že se můžete úspěšně přihlásit k portálu správce pomocí přihlašovacích údajů správce.
1. Potvrďte, že nejsou k dispozici žádná upozornění nebo upozornění.

Pokud je prostředí v pořádku, ručně nahrajte pět imagí virtuálních počítačů potřebných pro testovací běhy VaaS:

1. Přihlaste se jako správce služby na portál správce. Adresu URL portálu pro správu najdete na webu EHK Store nebo v souboru s informacemi o razítku. Pokyny najdete v tématu [parametry prostředí](azure-stack-vaas-parameters.md#environment-parameters).
1. Vyberte **Další služby** > **poskytovatelé** > prostředků**výpočetních** > **imagí virtuálních počítačů**.
1. V horní části okna **image virtuálních počítačů** vyberte tlačítko **+ Přidat** .
1. Upravte nebo ověřte hodnoty následujících polí pro první bitovou kopii virtuálního počítače:
    > [!IMPORTANT]
    > Ne všechny výchozí hodnoty jsou správné pro existující položku Marketplace.

    | Pole  | Hodnota  |
    |---------|---------|
    | Vydavatel | MicrosoftWindowsServer |
    | Nabídka | WindowsServer |
    | OS Type | Windows |
    | Skladová jednotka (SKU) | 2012-R2-Datacenter |
    | Verze | 1.0.0 |
    | Identifikátor URI objektu BLOB disku s operačním systémem | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |

1. Vyberte tlačítko **Vytvořit**.
1. Opakujte pro zbývající image virtuálních počítačů.

Vlastnosti všech pěti imagí virtuálních počítačů jsou následující:

| Vydavatel  | Nabídka  | OS Type | Skladová jednotka (SKU) | Verze | Identifikátor URI objektu BLOB disku s operačním systémem |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/WindowsServer2012R2DatacenterBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016 – Datacenter | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterFullBYOL.vhd |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016 – Datacenter – Server – jádro | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Server2016DatacenterCoreBYOL.vhd |
| Canonical | UbuntuServer | Linux | 14.04.3 – LTS | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1404LTS.vhd |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Ubuntu1604-20170619.1.vhd |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/OpenLogic-CentOS-69-20180105.vhd |
| credativ | Debian | Linux | 8 | 1.0.0 | https://azurestacktemplate.blob.core.windows.net/azurestacktemplate-public-container/Debian8_latest.vhd |

## <a name="next-steps"></a>Další kroky

- Přečtěte si [poznámky k verzi pro ověřování jako službu](azure-stack-vaas-release-notes.md) pro změny v nejnovějších verzích.