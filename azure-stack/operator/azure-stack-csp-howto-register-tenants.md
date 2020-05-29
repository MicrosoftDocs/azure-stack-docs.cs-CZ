---
title: Přidání tenantů pro použití a fakturaci do centra Azure Stack
description: Naučte se, jak přidat tenanta pro využití a fakturace do centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 5/28/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 5/28/2020
ms.openlocfilehash: 08185a25c608c735aa99ca7f7d2b060c8b67042b
ms.sourcegitcommit: 804f94f288859027b8249d138b14e8bc1501e009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84158380"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack-hub"></a>Přidat tenanta pro využití a fakturace do centra Azure Stack

Tento článek popisuje, jak přidat tenanta do nasazení centra Azure Stack spravovaného poskytovatelem Cloud Solution Provider (CSP). Když nový tenant používá prostředky, Azure Stack Centrum sestav využití jejich předplatného CSP.

CSP často nabízí služby pro různé koncové zákazníky (klienty) na svém nasazení centra Azure Stack. Přidání tenantů do registrace centra Azure Stack zajistí, že bude využití každého tenanta hlášené a bude účtované na odpovídající předplatné CSP. Pokud neprovedete kroky v tomto článku, bude využití tenanta účtováno v rámci předplatného používaného při prvotní registraci centra Azure Stack. Než budete moct přidat koncového zákazníka do centra Azure Stack pro sledování využití a spravovat svého tenanta, musíte nakonfigurovat Azure Stack hub jako CSP. Postup a prostředky najdete v tématu [Správa využití a fakturace pro centra Azure Stack jako poskytovatele Cloud Solution Provider](azure-stack-add-manage-billing-as-a-csp.md).

Následující obrázek znázorňuje kroky, které zprostředkovatel CSP potřebuje k tomu, aby mohl novému koncovému zákazníkovi používat centrum Azure Stack a k nastavení sledování využívání pro zákazníka. Přidáním koncového zákazníka také můžete spravovat prostředky v centru Azure Stack. Pro správu svých prostředků máte dvě možnosti:

- Můžete zachovat koncového zákazníka a zadat přihlašovací údaje pro místní předplatné centra Azure Stack pro koncového zákazníka.  
- Koncový zákazník může pracovat s předplatným místně a přidat CSP jako hosta s oprávněním vlastníka.

## <a name="add-an-end-customer"></a>Přidat koncového zákazníka

Před přidáním koncového zákazníka musíte povolit u registrace více tenantů. Aby bylo možné povolit účtování více tenantů, odešlete ID předplatného registrace, název skupiny prostředků a název registrace do `azstcsp@microsoft.com` . K povolení víceklientské architektury obvykle trvá 1-2 pracovních dnů.

Chcete-li přidat koncového zákazníka, jak je znázorněno na následujícím obrázku, proveďte následující kroky:

![Nastavení poskytovatele Cloud Solution Provider pro sledování využití a Správa účtu koncového zákazníka](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Vytvoření nového zákazníka v partnerském centru

V partnerském centru vytvořte pro zákazníka nové předplatné Azure. Pokyny najdete v tématu [Přidání nového zákazníka](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Vytvoření předplatného Azure pro koncového zákazníka

Jakmile vytvoříte záznam o zákazníkovi v partnerském centru, můžete si ho prodávat do produktů v katalogu. Pokyny najdete v tématu [Vytvoření, pozastavení nebo zrušení zákaznických předplatných](/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Vytvoření uživatele typu Host v adresáři koncového zákazníka

Ve výchozím nastavení jste jako CSP nemuseli mít přístup k předplatnému centra Azure Stack pro koncové zákazníky. Pokud ale zákazník chce spravovat svoje prostředky, může přidat svůj účet jako vlastníka nebo přispěvatele do svého předplatného centra Azure Stack. Aby to bylo možné, musí přidat váš účet jako uživatel typu Host do svého tenanta služby Azure AD. Doporučujeme použít jiný účet z vašeho účtu Azure CSP ke správě předplatného centra Azure Stack zákazníka, abyste se ujistili, že nepřijdete o přístup k předplatnému Azure vašeho zákazníka.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualizace registrace pomocí předplatného koncového zákazníka

Aktualizujte svou registraci pomocí nového zákaznického předplatného. Azure oznamuje využívání zákazníků pomocí zákaznické identity z partnerského centra. Tento krok zajistí, že se využití každého zákazníka hlásí v rámci příslušného předplatného zprostředkovatele CSP daného zákazníka. Díky tomu je sledování využití a fakturace jednodušší. Chcete-li provést tento krok, je třeba nejprve [zaregistrovat Azure Stack hub](azure-stack-registration.md).

1. Otevřete prostředí Windows PowerShell v příkazovém řádku se zvýšenými oprávněními a spusťte příkaz:  

   ```powershell
   Add-AzureRmAccount
   ```

   >[!NOTE]
   > Pokud vaše relace vyprší, vaše heslo se změnilo nebo pokud chcete jednoduše přepnout účty, spusťte následující rutinu ještě před přihlášením pomocí rutiny **Add-AzureRmAccount**: `Remove-AzureRmAccount-Scope Process` .

2. Zadejte svoje přihlašovací údaje Azure.
3. V relaci PowerShellu spusťte příkaz:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Parametry prostředí PowerShell pro New-AzureRmResource

V následující části jsou popsány parametry pro rutinu **New-AzureRmResource** :

| Parametr | Popis |
| --- | --- |
|registrationSubscriptionID | Předplatné Azure, které se použilo při prvotní registraci centra Azure Stack.|
| customerSubscriptionID | Předplatné Azure (ne Azure Stack centrum) patřící zákazníkovi, který se má zaregistrovat Musí být vytvořen v nabídce CSP. V praxi to znamená prostřednictvím partnerského centra. Pokud má zákazník více než jednoho klienta Azure Active Directory, musí být toto předplatné vytvořeno v tenantovi, které se bude používat k přihlášení do centra Azure Stack. ID předplatného zákazníka rozlišuje velká a malá písmena. |
| resourceGroup | Skupina prostředků v Azure, ve které je uložená vaše registrace. |
| registrace | Název registrace centra Azure Stack. Je to objekt uložený v Azure. 

> [!NOTE]  
> Klienti musí být zaregistrované u každého Azure Stackho centra, které používají. Pokud máte dvě nasazení centra Azure Stack a klient používá oba z nich, je nutné aktualizovat počáteční registraci každého nasazení u předplatného tenanta.

### <a name="onboard-tenant-to-azure-stack-hub"></a>Začlenit tenanta do centra Azure Stack

Nakonfigurujte centrum Azure Stack tak, aby podporovalo uživatele z více tenantů Azure AD, aby mohli používat služby v centru Azure Stack. Pokyny najdete v tématu [Povolení víceklientské architektury v centru Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack-hub"></a>Vytvoření místního prostředku v tenantovi koncového zákazníka v centru Azure Stack

Jakmile přidáte nového zákazníka do centra Azure Stack nebo koncový tenant zákazníka povolil váš účet Guest s oprávněními vlastníka, ověřte, že můžete vytvořit prostředek ve svém tenantovi. Například mohou [vytvořit virtuální počítač s Windows pomocí portálu Azure Stack hub](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Další kroky

- Pokud chcete zkontrolovat chybové zprávy, pokud se aktivují v procesu registrace, přečtěte si téma [chybové zprávy registrace tenanta](azure-stack-registration-errors.md).
- Další informace o tom, jak načíst informace o využití prostředků z centra Azure Stack, najdete [v tématu využití a fakturace v centru Azure Stack](azure-stack-billing-and-chargeback.md).
- Pokud chcete zjistit, jak může koncový zákazník přidat vás, CSP jako správce pro svého tenanta centra Azure Stack, přečtěte si téma [povolení poskytovatele Cloud Solution pro správu předplatného centra Azure Stack](../user/azure-stack-csp-enable-billing-usage-tracking.md).
