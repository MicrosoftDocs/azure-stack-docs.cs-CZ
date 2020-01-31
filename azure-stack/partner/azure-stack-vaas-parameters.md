---
title: Společné parametry pracovního postupu v Azure Stack ověřování jako služba
description: Společné parametry pracovního postupu pro ověřování Azure Stack jako služby
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ee72aca1cbba27c75d2811dac96d4b0a78a87617
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885039"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Společné parametry pracovního postupu pro ověřování Azure Stack jako služby

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Mezi běžné parametry patří hodnoty, jako jsou proměnné prostředí a přihlašovací údaje uživatele vyžadované všemi testy v rámci ověřování jako služby (VaaS). Tyto hodnoty jsou definovány na úrovni pracovního postupu při vytváření nebo změně pracovního postupu. Při plánování testů jsou tyto hodnoty předány jako parametry do každého testu v rámci pracovního postupu.

> [!NOTE]
> Každý test definuje svou vlastní sadu parametrů. V době plánování může test vyžadovat, abyste zadali hodnotu nezávisle na běžných parametrech, nebo vám může být umožněno přepsat společnou hodnotu parametru.

## <a name="environment-parameters"></a>Parametry prostředí

Parametry prostředí popisují Azure Stack prostředí pod testem. Tyto hodnoty musí být k dispozici generováním a nahráním souboru s informacemi o Azure Stack razítka pro konkrétní instanci, kterou testujete.

> [!NOTE]
> V oficiálních ověřovacích pracovních postupech nelze parametry prostředí upravovat po vytvoření pracovního postupu.

### <a name="generate-the-stamp-information-file"></a>Vygenerovat soubor s informacemi o razítku

1. Přihlaste se k DVM nebo jakémukoli počítači, který má přístup k prostředí Azure Stack.
2. V okně PowerShellu se zvýšenými oprávněními spusťte následující příkazy:

    ```powershell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Vyhledat hodnoty v konfiguračním souboru EHK

Hodnoty parametrů prostředí se taky dají ručně umístit do **konfiguračního souboru EHK** , který najdete na adrese `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` DVM.

## <a name="test-parameters"></a>Parametry testu

Mezi běžné parametry testu patří citlivé informace, které nelze uložit v konfiguračních souborech. Ty je nutné zadat ručně.

Parametr    | Popis
-------------|-----------------
Správce klienta uživatel                            | Azure Active Directory Správce klienta zřízené správcem služby v adresáři AAD. Tento uživatel provádí akce na úrovni tenanta, jako je nasazení šablon, k nastavení prostředků (virtuálních počítačů, účtů úložiště atd.) a provádění úloh. Podrobnosti o zřízení účtu tenanta najdete v tématu [Přidání nového tenanta Azure Stack](../operator/azure-stack-add-new-user-aad.md).
Uživatel správce služeb             | Azure Active Directory správce tenanta služby Azure AD, kterého jste zadali během nasazování Azure Stack. Vyhledejte `AADTenant` v konfiguračním souboru EHK a vyberte hodnotu v prvku `UniqueName`.
Uživatel s oprávněním správce cloudu               | Azure Stack účet správce domény (například `contoso\cloudadmin`). Vyhledejte `User Role="CloudAdmin"` v konfiguračním souboru EHK a vyberte hodnotu v prvku `UserName`.
Připojovací řetězec diagnostiky          | Adresa URL SAS na účet Azure Storage, do kterého budou při spuštění testu kopírovány diagnostické protokoly. Pokyny k vygenerování adresy URL SAS najdete v tématu [generování připojovacího řetězce pro diagnostiku](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> Před pokračováním musí být **připojovací řetězec diagnostiky** platný.

### <a name="generate-the-diagnostics-connection-string"></a>Generovat připojovací řetězec diagnostiky

Připojovací řetězec diagnostiky je vyžadován pro ukládání diagnostických protokolů během provádění testu. Použijte účet Azure Storage vytvořený během instalace (viz [nastavení ověřování jako prostředků služby](azure-stack-vaas-set-up-resources.md)), abyste vytvořili adresu URL sdíleného přístupového podpisu (SAS), která VaaS přístup k odesílání protokolů do svého účtu úložiště.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Z **možností povolených služeb**vyberte **objekt BLOB** . Zrušte výběr všech zbývajících možností.

1. Vyberte **službu**, **kontejner**a **objekt** z **povolených typů prostředků**.

1. Vyberte **číst**, **zapsat**, **vypsat**, **Přidat**a **vytvořit** z **povolených oprávnění**. Zrušte výběr všech zbývajících možností.

1. Nastaví **počáteční čas** na aktuální čas a **koncový čas** na tři měsíce od aktuálního času.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> Adresa URL SAS vyprší v době ukončení zadanou při vygenerování adresy URL.  
Při plánování testů zajistěte, aby byla adresa URL platná nejméně 30 dní, a čas potřebný k provedení testu (navrhované tři měsíce).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [principech ověření jako Key služby](azure-stack-vaas-key-concepts.md)