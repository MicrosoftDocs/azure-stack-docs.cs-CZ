---
title: Ověření balíčků OEM (Original Equipment Manufacturer) v Azure Stack ověřování jako služby | Microsoft Docs
description: Naučte se, jak ověřit balíčky OEM (Original Equipment Manufacturer) pomocí ověřování jako služby.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 69bb9c89793789280debe13a142f4c96470f7c31
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418316"
---
# <a name="validate-oem-packages"></a>Ověřit balíčky OEM

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Můžete otestovat nový balíček OEM, pokud došlo ke změně firmwaru nebo ovladačů pro dokončené ověření řešení. Po úspěšném ověření balíčku je tento test podepsán společností Microsoft. Test musí obsahovat aktualizovaný balíček rozšíření OEM s ovladači a firmwarem, kteří prošli testováním loga a počítačů s Windows serverem.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

> [!IMPORTANT]
> Před odesláním nebo odesláním balíčků si přečtěte téma [Vytvoření balíčku OEM](azure-stack-vaas-create-oem-package.md) pro očekávaný formát a obsah balíčku.

## <a name="managing-packages-for-validation"></a>Správa balíčků pro ověření

Při ověřování balíčku pomocí pracovního postupu **ověření balíčku** budete muset zadat adresu URL **Azure Storage objektu BLOB**. Tento objekt BLOB je test podepsaný balíčkem OEM, který se nainstaluje jako součást procesu aktualizace. Vytvořte objekt BLOB pomocí účtu Azure Storage, který jste vytvořili během instalace (viz [nastavení ověřování jako prostředků služby](azure-stack-vaas-set-up-resources.md)).

### <a name="prerequisite-provision-a-storage-container"></a>Požadovaných součástí Zřízení kontejneru úložiště

Vytvořte kontejner v účtu úložiště pro objekty blob balíčku. Tento kontejner se dá použít pro všechna vaše spuštění ověření balíčku.

1. V [Azure Portal](https://portal.azure.com)v části vyberte účet úložiště, který jste vytvořili v části [nastavení ověřování jako prostředků služby](azure-stack-vaas-set-up-resources.md).

2. V levém okně v části **BLOB Service**vyberte **kontejnery**.

3. Z panelu nabídek vyberte **+ kontejner** .
    1. Zadejte název kontejneru, `vaaspackages`například.
    1. Vyberte požadovanou úroveň přístupu pro neověřené klienty, například VaaS. Podrobnosti o tom, jak udělit přístup VaaS k balíčkům v jednotlivých scénářích, najdete v tématu [zpracování úrovně přístupu kontejneru](#handling-container-access-level).

### <a name="upload-package-to-storage-account"></a>Nahrát balíček do účtu úložiště

1. Připravte balíček, který chcete ověřit. Jedná se o `.zip` soubor, jehož obsah se musí shodovat s strukturou popsanou v tématu [Vytvoření balíčku OEM](azure-stack-vaas-create-oem-package.md).

    > [!NOTE]
    > Ujistěte se prosím, `.zip` že obsah je umístěný v kořenu `.zip` souboru. V balíčku by neměly být žádné podadresáře.

1. V [Azure Portal](https://portal.azure.com)vyberte kontejner balíčku a nahrajte balíček výběrem možnosti **nahrát** na panelu nabídek.

1. Vyberte soubor balíčku `.zip` , který se má nahrát. Nechejte výchozí hodnoty pro **typ objektu BLOB** (tj. **objekt blob bloku**) a **velikost bloku**.

### <a name="generate-package-blob-url-for-vaas"></a>Generovat adresu URL objektu BLOB balíčku pro VaaS

Při vytváření pracovního postupu **ověření balíčku** na portálu VaaS budete muset zadat adresu URL objektu blob Azure Storage, který obsahuje váš balíček. Některé *interaktivní* testy, včetně **měsíčního ověření aktualizací AzureStack** a **ověření balíčku rozšíření OEM**, vyžadují také adresu URL pro balíčky objektů BLOB.

#### <a name="handling-container-access-level"></a>Zpracování úrovně přístupu kontejneru

Minimální úroveň přístupu požadovaná nástrojem VaaS závisí na tom, zda vytváříte pracovní postup ověření balíčku nebo plánujete *interaktivní* test.

V případě úrovní přístupu **Private** a **BLOB** musíte dočasně udělit přístup k objektu BLOB balíčku poskytnutím VaaS [sdíleného přístupového podpisu](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1?) (SAS). Úroveň přístupu **kontejneru** nevyžaduje, abyste vygenerovali adresy URL SAS, ale umožňuje neověřený přístup ke kontejneru a jeho objektům blob.

|Úroveň přístupu | Požadavek pracovního postupu | Požadavek testu |
|---|---------|---------|
|Soukromé | Vygenerujte adresu URL SAS na jeden objekt BLOB balíčku ([možnost 1](#option-1-generate-a-blob-sas-url)). | Vygenerujte adresu URL SAS na úrovni účtu a ručně přidejte název objektu BLOB balíčku ([možnost 2](#option-2-construct-a-container-sas-url)). |
|Blob | Zadejte vlastnost URL objektu BLOB ([možnost 3](#option-3-grant-public-read-access)). | Vygenerujte adresu URL SAS na úrovni účtu a ručně přidejte název objektu BLOB balíčku ([možnost 2](#option-2-construct-a-container-sas-url)). |
|Kontejner | Zadejte vlastnost URL objektu BLOB ([možnost 3](#option-3-grant-public-read-access)). | Zadejte vlastnost URL objektu BLOB ([možnost 3](#option-3-grant-public-read-access)).

Možnosti pro udělení přístupu k vašim balíčkům jsou seřazené z minimálního přístupu k největšímu přístupu.

#### <a name="option-1-generate-a-blob-sas-url"></a>Možnost 1: Vygenerovat adresu URL SAS objektu BLOB

Tuto možnost použijte, pokud je úroveň přístupu kontejneru úložiště nastavená na **Private**, kde kontejner nepovoluje veřejný přístup pro čtení kontejneru nebo jeho objektů BLOB.

> [!NOTE]
> Tato metoda nebude pro *interaktivní* testy fungovat. Viz [možnost 2: Sestavte adresu URL](#option-2-construct-a-container-sas-url)SAS kontejneru.

1. V [Azure Portal](https://portal.azure.com/)přejděte na svůj účet úložiště a přejděte do souboru. zip, který obsahuje váš balíček.

2. V místní nabídce vyberte **Generovat SAS** .

3. Vyberte **číst** z **oprávnění**.

4. Nastavte **čas zahájení** na aktuální čas a **čas ukončení** na nejméně 48 hodin od **počátečního času**. Pokud budete vytvářet jiné pracovní postupy se stejným balíčkem, zvažte zvýšení **času ukončení** pro délku testování.

5. Vyberte **vygenerujte token SAS objektů blob a adresa URL**.

Při poskytování adres URL objektů BLOB balíčku na portál použijte **adresu URL SAS objektu BLOB** .

#### <a name="option-2-construct-a-container-sas-url"></a>Možnost 2: Vytvoření adresy URL SAS kontejneru

Tuto možnost použijte, pokud je úroveň přístupu kontejneru úložiště nastavená na **Private** a potřebujete zadat adresu URL objektu BLOB balíčku k interaktivnímu testu  . Tuto adresu URL lze také použít na úrovni pracovního postupu.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Z **možností povolených služeb**vyberte **objekt BLOB** . Zrušte výběr všech zbývajících možností.

1. Vyberte **kontejner** a **objekt** z **povolených typů prostředků**.

1. Vyberte **číst** a **seznam** z **povolených oprávnění**. Zrušte výběr všech zbývajících možností.

1. Vyberte možnost **čas spuštění** jako aktuální a **koncový čas** do alespoň 14 dnů od **počátečního času**. Pokud budete spouštět další testy se stejným balíčkem, zvažte zvýšení **času ukončení** pro délku testování. Jakékoli testy naplánované prostřednictvím VaaS po **čase ukončení** selžou a bude nutné vygenerovat nové SAS.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]
    Formát by měl vypadat takto:`https://storageaccountname.blob.core.windows.net/?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

1. Upravte vygenerovanou adresu URL SAS tak, aby zahrnovala `{containername}`kontejner balíčku, a název `{mypackage.zip}`objektu BLOB balíčku, a to následujícím způsobem:`https://storageaccountname.blob.core.windows.net/{containername}/{mypackage.zip}?sv=2016-05-31&ss=b&srt=co&sp=rl&se=2017-05-11T21:41:05Z&st=2017-05-11T13:41:05Z&spr=https`

    Tuto hodnotu použijte při poskytování adres URL objektů BLOB balíčku na portál.

#### <a name="option-3-grant-public-read-access"></a>Možnost 3: Udělit veřejný přístup pro čtení

Tuto možnost použijte, pokud je přijatelné, aby povolovaly neověřené klienty přístup k jednotlivým objektům blob,  nebo v případě interaktivních testů, kontejneru.

> [!CAUTION]
> Tato možnost otevře objekty blob pro anonymní přístup s oprávněním jen pro čtení.

1. Podle pokynů v části [udělení anonymních uživatelů oprávnění kontejnerů a objektů BLOB](https://docs.microsoft.com/azure/storage/storage-manage-access-to-resources#grant-anonymous-users-permissions-to-containers-and-blobs)nastavte úroveň přístupu kontejneru balíčku na **objekt BLOB** nebo **kontejner** .

    > [!NOTE]
    > Pokud poskytujete URL balíčku pro *interaktivní* test, je nutné všem kontejnerům udělit **úplný veřejný přístup pro čtení** , aby bylo možné pokračovat v testování.

1. V kontejneru balíčku vyberte objekt BLOB balíčku pro otevření podokna Vlastnosti.

1. Zkopírujte **adresu URL**. Tuto hodnotu použijte při poskytování adres URL objektů BLOB balíčku na portál.

## <a name="create-a-package-validation-workflow"></a>Vytvoření pracovního postupu ověření balíčku

1. Přihlaste se k [portálu VaaS](https://azurestackvalidation.com).

2. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Na dlaždici **ověření balíčku** vyberte **Spustit** .

    ![Dlaždice pracovního postupu pro ověření balíčku](media/tile_validation-package.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Zadejte adresu URL objektu blob Azure Storage k testovanému balíčku OEM s podpisem, který vyžaduje podpis od Microsoftu. Pokyny najdete v tématu [generování adresy URL objektu BLOB balíčku pro VaaS](#generate-package-blob-url-for-vaas).

6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Po vytvoření pracovního postupu nelze změnit parametry prostředí.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]

9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Budete přesměrováni na stránku souhrn testů.

## <a name="required-tests"></a>Požadované testy

Pro ověření balíčku OEM jsou vyžadovány následující testy:

- Ověření balíčku rozšíření OEM
- Modul pro simulaci cloudu

## <a name="run-package-validation-tests"></a>Spustit testy pro ověření balíčku

1. Na stránce **Souhrn testů pro ověření balíčku** spustíte podmnožinu uvedených testů, které jsou vhodné pro váš scénář.

    **Plánování** testu v pracovních postupech ověřování používá společné parametry na úrovni pracovního postupu, které jste zadali během vytváření pracovního postupu (viz [společné parametry pracovních postupů pro Azure Stack ověřování jako služby](azure-stack-vaas-parameters.md)). Pokud se některá z hodnot parametrů testu stane neplatných, musíte je znovu zadat podle pokynů v části [Úprava parametrů pracovního postupu](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

    > [!NOTE]
    > Plánování ověřovacího testu přes existující instanci vytvoří na portálu novou instanci místo staré instance. Protokoly pro starou instanci budou zachovány, ale nebudou přístupné z portálu.  
    > Po úspěšném dokončení testu se akce **plánu** zakáže.

2. Vyberte agenta, který spustí test. Informace o přidávání místních agentů spuštění testů najdete v tématu [nasazení místního agenta](azure-stack-vaas-local-agent.md).

3. Chcete-li dokončit ověření balíčku rozšíření OEM, v místní nabídce vyberte možnost **plán** . otevře se výzva k naplánování testovací instance.

4. Zkontrolujte parametry testu a pak vyberte **Odeslat** a naplánujte ověření balíčku rozšíření OEM pro spuštění.

    Ověření balíčku rozšíření OEM je rozděleno do dvou ručních kroků: Aktualizace Azure Stack a OEM Update.

   1. **Vybrat** "Run" v uživatelském rozhraní pro spuštění skriptu pro kontrolu. Toto je automatizovaný test, který dokončení trvá přibližně 5 minut a nevyžaduje žádnou akci.

   1. Po dokončení skriptu předkontroly proveďte ruční krok: **nainstalujte** nejnovější dostupnou Azure Stack aktualizaci pomocí portálu Azure Stack.

   1. **Spustit příkaz** Test-AzureStack razítka. Pokud dojde k nějaké chybě, nepokračujte v testování a kontaktujte [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

       Informace o tom, jak spustit příkaz Test-AzureStack, najdete v tématu [ověření stavu systému Azure Stack](../operator/azure-stack-diagnostic-test.md).

   1. **Vybrat** "Next" pro spuštění skriptu postcheck. Toto je automatizovaný test a označuje konec procesu aktualizace Azure Stack.

   1. **Vybrat** Run (spustit) pro spuštění předkontrolního skriptu pro aktualizaci OEM.

   1. Po dokončení předkontroly proveďte ruční krok: **instalace** balíčku rozšíření OEM prostřednictvím portálu.

   1. **Spustit příkaz** Test-AzureStack razítka.

      > [!NOTE]
      > Stejně jako dřív nepokračujte v testu a v případě neúspěchu kontaktujte [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) . Tento krok je velmi důležitý, protože vám ušetří opětovné nasazení.

   1. **Vybrat** "Next" pro spuštění skriptu postcheck. Tím se označí konec kroku aktualizace OEM.

   1. Odpovězte na všechny zbývající otázky na konci testu a **Vyberte** odeslat.

   1. Tato značka označuje konec interaktivního testu.

5. Prohlédněte si výsledek ověření balíčku rozšíření OEM. Po úspěšném dokončení testu Naplánujte modul simulace cloudu pro spuštění.

Pokud chcete odeslat žádost o podepsání balíčku [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) , odešlete název řešení a název balíčku, který je přidružený k tomuto spuštění.

## <a name="next-steps"></a>Další postup

- [Monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)
