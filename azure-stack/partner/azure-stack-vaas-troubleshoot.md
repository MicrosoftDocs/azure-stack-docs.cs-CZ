---
title: Řešení potíží s ověřováním jako službou
titleSuffix: Azure Stack Hub
description: Řešení potíží s ověřováním jako služby pro centrum Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f9a3b4b3ac61447a3dca567ad9ebcb636c8f9bc3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874621"
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

### <a name="failure-occurs-when-uploading-vm-image-in-the-vaasprereq-script"></a>K selhání dojde při nahrávání image virtuálního počítače ve `VaaSPreReq` skriptu.
Informace o **pomalém připojení k síti** najdete v části níže. Nabízí ruční kroky pro nahrání imagí virtuálních počítačů do Azure Stack razítkem.

### <a name="handle-slow-network-connectivity"></a>Zpracování pomalého připojení k síti

#### <a name="1-verify-that-the-environment-is-healthy"></a>1. Ověřte, jestli je prostředí v pořádku.

1. V poli DVM/skočit ověřte, že se můžete úspěšně přihlásit k portálu pro správu pomocí přihlašovacích údajů správce.

2. Potvrďte, že nejsou k dispozici žádná upozornění nebo upozornění.

3. Pokud je prostředí v pořádku, ručně nahrajte image virtuálních počítačů požadované pro testovací běhy VaaS, a to podle kroků v níže uvedené části.

<!-- This is from the appendix to the Deploy local agent topic. -->

#### <a name="2-download-pir-image-to-local-share-in-case-of-slow-network-traffic"></a>2. Stáhněte si image PIR do místního sdílení v případě pomalého síťového provozu.

1. Stáhněte si AzCopy z: [vaasexternaldependencies (AzCopy)](https://vaasexternaldependencies.blob.core.windows.net/prereqcomponents/AzCopy.zip).

2. Extrahujte AzCopy.zip a přejděte do adresáře obsahujícího `AzCopy.exe` .

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

#### <a name="3-verifying-pir-image-file-hash-value"></a>3. ověřování hodnoty hash souboru obrázku PIR

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

#### <a name="4-upload-vm-images-to-a-storage-account"></a>4. nahrání imagí virtuálních počítačů do účtu úložiště

1. Použijte existující účet úložiště nebo vytvořte nový účet úložiště v Azure.

2. Vytvořte kontejner, do kterého se mají nahrát obrázky.

3. Pomocí nástroje AzCopy nahrajte image virtuálních počítačů z [*LocalFileShare*] výše (místo, kam jste stáhli image virtuálních počítačů do) do kontejneru, který jste právě vytvořili.
    > [!IMPORTANT]
    > Změňte úroveň veřejného přístupu kontejneru na BLOB (anonymní přístup pro čtení jenom pro objekty BLOB).

#### <a name="5-upload-vm-images-to-azure-stack-environment"></a>5. nahrání imagí virtuálních počítačů do prostředí Azure Stack

1. Přihlaste se jako správce služby na portál pro správu. Adresu URL portálu pro správu najdete na webu EHK Store nebo v souboru s informacemi o razítku. Pokyny najdete v tématu [parametry prostředí](azure-stack-vaas-parameters.md#environment-parameters).

2. Vyberte **Další služby**  >  **poskytovatelé prostředků**  >  **výpočetních**  >  **imagí virtuálních počítačů**.

3. V horní části okna **image virtuálních počítačů** vyberte tlačítko **+ Přidat** .

4. Upravte nebo ověřte hodnoty následujících polí pro první bitovou kopii virtuálního počítače:

    > [!IMPORTANT]
    > Ne všechny výchozí hodnoty jsou správné pro existující položku Marketplace.

    | Pole  | Hodnota  |
    |---------|---------|
    | Publisher | MicrosoftWindowsServer |
    | Nabídka | WindowsServer |
    | Typ operačního systému | Windows |
    | SKU | 2012-R2-Datacenter |
    | Verze | 1.0.0 |
    | Identifikátor URI objektu BLOB disku s operačním systémem | https://< >/< *název kontejneru* účtu úložiště>/WindowsServer2012R2DatacenterBYOL.VHD |


5. Vyberte tlačítko **Vytvořit**.

6. Opakujte pro zbývající image virtuálních počítačů.

Vlastnosti všech požadovaných imagí virtuálních počítačů jsou následující:

| Publisher  | Nabídka  | Typ operačního systému | SKU | Verze | Identifikátor URI objektu BLOB disku s operačním systémem |
|---------|---------|---------|---------|---------|---------|
| MicrosoftWindowsServer| WindowsServer | Windows | 2012-R2-Datacenter | 1.0.0 | https://[*účet úložiště*]/[*název kontejneru*]/WindowsServer2012R2DatacenterBYOL.VHD |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016 – Datacenter | 1.0.0 | https://[*účet úložiště*]/[*název kontejneru*]/Server2016DatacenterFullBYOL.VHD |
| MicrosoftWindowsServer | WindowsServer | Windows | 2016 – Datacenter – Server – jádro | 1.0.0 | https://[*účet úložiště*]/[*název kontejneru*]/Server2016DatacenterCoreBYOL.VHD |
| Canonical | UbuntuServer | Linux | 14.04.3 – LTS | 1.0.0 | https://[*účet úložiště*]/[*název kontejneru*]/Ubuntu1404LTS.VHD |
| Canonical | UbuntuServer | Linux | 16.04-LTS | 16.04.20170811 | https://[*účet úložiště*]/[*název kontejneru*]/Ubuntu1604-20170619.1.VHD |
| OpenLogic | CentOS | Linux | 6.9 | 1.0.0 | https://[*účet úložiště*]/[*název kontejneru*]/OpenLogic-CentOS-69-20180105.VHD |
| Credativ | Debian | Linux | 8 | 1.0.0 | https://[*účet úložiště*]/[*název kontejneru*]/Debian8_latest. VHD |

## <a name="next-steps"></a>Další kroky

- Přečtěte si [poznámky k verzi pro ověřování jako službu](azure-stack-vaas-release-notes.md) pro změny v nejnovějších verzích.