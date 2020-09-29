---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 8b8f26eefd70e54629a1505a01e1306cab2eda1d
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609769"
---
Svůj virtuální pevný disk můžete nahrát pomocí portálu nebo pomocí kontejneru, který jste vytvořili na portálu, použijte AzCopy.

### <a name="portal-to-generate-sas-url-and-upload-vhd"></a>Portál pro vygenerování adresy URL SAS a nahrání virtuálního pevného disku

1. Přihlaste se k portálu pro uživatele centra Azure Stack.

2. Vyberte **účty úložiště** a vyberte existující účet úložiště nebo vytvořte nový účet úložiště.

3. V okně účet úložiště pro váš účet úložiště vyberte **objekty blob** . Vyberte kontejner pro vytvoření nového kontejneru.

4. Zadejte název svého kontejneru a pak vyberte **objekt BLOB (anonymní přístup pro čtení jenom pro objekty BLOB)**.

5. Pokud budete místo portálu používat AzCopy k nahrání image, vytvořte token SAS. V účtu úložiště vyberte **sdílený přístupový podpis** a pak vyberte **Generovat SAS a připojovací řetězec**. Zkopírujte a poznamenejte si **adresu URL BLOB Service SAS**. Tuto adresu URL použijete při použití AzCopy k nahrání virtuálního pevného disku.

6. Vyberte svůj kontejner a pak vyberte **nahrát**. Nahrajte virtuální pevný disk.

### <a name="azcopy-vhd"></a>AzCopy VHD

Pomocí Průzkumník služby Azure Storage nebo AzCopy můžete snížit pravděpodobnost, že váš virtuální pevný disk bude v procesu nahrávání poškozený, a vaše nahrávání bude rychlejší. Následující kroky používají AzCopy na počítači s Windows 10. AzCopy je nástroj příkazového řádku, pomocí kterého můžete kopírovat objekty blob nebo soubory do nebo z účtu úložiště.

1. Pokud nemáte AzCopy nainstalované, nainstalujte AzCopy. Pokyny najdete v článku [Začínáme s AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10), najdete v článku o tom, jak stáhnout a začít s AzCopy. Poznamenejte si, kam ukládáte binární soubor. Do [své cesty můžete přidat AzCopy](https://www.architectryan.com/2018/03/17/add-to-the-path-on-windows-10/) , abyste ji mohli použít z příkazového řádku PowerShellu.

2. Otevřete PowerShell a použijte AzCopy z prostředí.

3. Pomocí AzCopy nahrajte virtuální pevný disk do svého kontejneru v účtu úložiště.

    ```powershell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    azcopy cp "/path/to/file.vhd" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS] --blob-type=PageBlob
    ```

> [!NOTE]  
> Nahrajte virtuální pevný disk pomocí syntaxe podobně jako nahrání jednoho souboru do virtuálního adresáře. Přidejte `--blob-type=PageBlob` a ujistěte se, že virtuální pevný disk se nahrává jako **objekt blob stránky**místo ve výchozím nastavení. **Block**

Další informace o používání AzCopy a dalších nástrojů úložiště najdete v tématu [použití nástrojů pro přenos dat v Azure Stack hub Storage](/azure-stack/user/azure-stack-storage-transfer).