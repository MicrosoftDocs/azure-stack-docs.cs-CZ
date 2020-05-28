---
title: Nastavení prostředků Azure AD a úložiště pro VaaS
titleSuffix: Azure Stack Hub
description: Naučte se nastavit prostředky Azure AD a úložiště pro Azure Stack ověřování pomocí centra jako služby.
author: mattbriggs
ms.topic: tutorial
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 3b3cbaff12c4d4decb0d7204fa558dd5690fe638
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84112012"
---
# <a name="tutorial-set-up-resources-for-validation-as-a-service"></a>Kurz: nastavení prostředků pro ověřování jako služby

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Ověřování jako služba (VaaS) je služba Azure, která slouží k ověřování a podpoře Azure Stackch řešení centra na trhu. Před použitím této služby k ověření vašeho řešení postupujte podle tohoto článku.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Připravte se na použití VaaS nastavením Azure Active Directory (AD).
> * Vytvoření účtu úložiště

## <a name="configure-an-azure-ad-tenant"></a>Konfigurace tenanta Azure AD

Tenant Azure AD se používá k registraci organizace a ověřování uživatelů pomocí VaaS. Partner bude pomocí funkcí řízení přístupu na základě rolí (RBAC) tenanta spravovat, kdo v partnerské organizaci může používat VaaS. Další informace najdete v tématu [Co je Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis).

### <a name="create-a-tenant"></a>Vytvoření tenanta

Vytvořte tenanta, který bude vaše organizace používat pro přístup ke službám VaaS Services. Použijte popisný název (například `ContosoVaaS@onmicrosoft.com` ).

1. Vytvořte ve [Azure Portal](https://portal.azure.com)TENANTA Azure AD, nebo použijte existujícího tenanta. <!-- For instructions on creating new Azure AD tenants, see [Get started with Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). -->

2. Přidejte do tenanta členy vaší organizace. Tito uživatelé budou odpovědni za používání služby k zobrazení nebo plánování testů. Po dokončení registrace definujete úrovně přístupu uživatelů.

    Udělte uživatelům ve vašem tenantovi, aby spouštěli akce v VaaS přiřazením jedné z následujících rolí:

    | Název role | Description |
    |---------------------|------------------------------------------|
    | Vlastník | Má úplný přístup ke všem prostředkům. |
    | Čtenář | Může zobrazit všechny prostředky, ale nemůže vytvářet ani spravovat. |
    | Přispěvatel testů | Může vytvářet a spravovat prostředky testu. |

    Přiřazení rolí v aplikaci **služby ověřování centra Azure Stack** :

   1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
   2. V části Identita vyberte **všechny služby**  >  **Azure Active Directory** . **Identity**
   3. Vyberte **podnikové aplikace**  >  **Azure Stack aplikaci služby ověřování centra** .
   4. Vyberte **Uživatelé a skupiny**. Okno **Azure Stack služby ověřování centra – uživatelé a skupiny** zobrazí seznam uživatelů, kteří mají oprávnění k používání aplikace.
   5. Vyberte **+ Přidat uživatele** a přidejte uživatele ze svého tenanta a přiřaďte roli.

      Pokud chcete izolovat VaaS prostředky a akce mezi různými skupinami v rámci organizace, můžete vytvořit více adresářů tenantů Azure AD.

### <a name="register-your-tenant"></a>Registrace tenanta

Tento proces autorizuje vašeho tenanta pomocí aplikace Azure AD **služby Azure Stack hub pro ověřování centra** .

1. Odeslat následující informace o tenantovi společnosti Microsoft na adrese [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) .

    | Data | Description |
    |--------------------------------|---------------------------------------------------------------------------------------------|
    | Název organizace | Oficiální název organizace. |
    | Název adresáře tenanta Azure AD | Název adresáře tenanta Azure AD, který se zaregistruje. |
    | ID adresáře tenanta Azure AD | Identifikátor GUID adresáře klienta služby Azure AD, který je přidružený k adresáři. Informace o tom, jak najít ID adresáře tenanta Azure AD, najdete v tématu [získání ID tenanta](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-values-for-signing-in). |

2. Počkejte na potvrzení od ověřovacího týmu centra Azure Stack, abyste zkontrolovali, jestli váš tenant může používat portál pro ověřování centra Azure Stack.

### <a name="consent-to-the-vaas-app"></a>Vyjádření souhlasu s aplikací VaaS

Jako správce Azure AD udělte aplikaci VaaS Azure AD požadovaná oprávnění jménem vašeho tenanta:

1. Pomocí přihlašovacích údajů globálního správce pro tenanta se přihlaste k [portálu pro ověřování centra Azure Stack](https://azurestackvalidation.com/).

2. Vyberte **můj účet**.

3 Pokud se zobrazí výzva k udělení VaaS oprávnění k Azure AD, přijměte podmínky.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu služby Azure Storage

Během provádění testu VaaS výstupy pro diagnostické protokoly na účet Azure Storage. Kromě protokolů testů se taky může účet úložiště použít k nahrání balíčků rozšíření OEM pro pracovní postup ověření balíčku.

Účet Azure Storage je hostovaný ve veřejném cloudu Azure, ne ve vašem prostředí Azure Stack hub.

1. V Azure Portal vyberte **všechny služby**  >  **úložiště**  >  **účty úložiště**. V okně **účty úložiště** vyberte **Přidat**.

2. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.

3. V části **Skupina prostředků**vyberte **vytvořit novou**. Zadejte název nové skupiny prostředků.

4. Přečtěte si [zásady vytváření názvů](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#storage) pro účty Azure Storage. Zadejte název účtu úložiště.

5. Vyberte oblast **USA – západ** pro váš účet úložiště.

    Aby se zajistilo, že se poplatky za síťové služby neúčtují pro ukládání protokolů, je možné účet Azure Storage nakonfigurovat tak, aby používal jenom **USA – západ** oblast. Funkce replikace dat a Hot úrovně úložiště nejsou pro tato data nutná. Povolení kterékoli součásti významně zvýší vaše náklady.

6. U nastavení ponechte výchozí hodnoty s výjimkou **druhu účtu**:

    - Ve výchozím nastavení je v poli **model nasazení** nastavena hodnota **Správce prostředků** .
    - Pole **Výkon** má výchozí nastavení **Standardní**.
    - Jako **úložiště objektů BLOB**vyberte pole **druh účtu** .
    - Ve výchozím nastavení je **pole replikace** nastaveno na **místně redundantní úložiště (LRS)** .
    - Pole **Úroveň přístupu** má výchozí nastavení **Horká**.

7. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.

## <a name="next-steps"></a>Další kroky

Pokud vaše prostředí nepovoluje příchozí připojení, postupujte podle kurzu nasazení místního agenta a spusťte test na svém hardwaru.

> [!div class="nextstepaction"]
> [Nasazení místního agenta](azure-stack-vaas-local-agent.md)
