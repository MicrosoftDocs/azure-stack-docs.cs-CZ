---
title: Přidat klienty pro využití a fakturace Azure Stack | Microsoft Docs
description: Tento postup vyžaduje přidání koncového uživatele, který Azure Stack spravuje poskytovatel cloudových služeb (CSP).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 06/07/2019
ms.openlocfilehash: 9f35a2bef6e5aa3b9ae1866927be007d58532b74
ms.sourcegitcommit: 5703255b4647ff0ebec23658a3f5c25d67f076a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70749967"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Přidat tenanta pro použití a fakturaci na Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje kroky potřebné k přidání koncového uživatele do nasazení Azure Stack spravovaného poskytovatelem cloudových služeb (CSP). Když nový tenant používá prostředky, Azure Stack sestavy využití jejich předplatného CSP.

CSP často nabízí služby pro různé koncové zákazníky (klienty) na svém nasazení Azure Stack. Přidání tenantů do registrace Azure Stack zajistí, že bude využití každého tenanta hlášené a bude účtované na odpovídající předplatné CSP. Pokud nedokončíte kroky v tomto článku, bude využití tenanta účtováno na předplatné používané při počáteční registraci Azure Stack. Než budete moct přidat koncového zákazníka do Azure Stack pro sledování využití a spravovat svého tenanta, musíte nakonfigurovat Azure Stack jako CSP. Postup a prostředky najdete v tématu [Správa využití a fakturace pro Azure Stack jako poskytovatel cloudových služeb](azure-stack-add-manage-billing-as-a-csp.md).

Následující obrázek znázorňuje kroky, které zprostředkovatel CSP potřebuje k tomu, aby mohl novému zákazníkovi použít Azure Stack, a nastavit sledování využívání pro zákazníka. Přidáním koncového zákazníka také můžete spravovat prostředky v Azure Stack. Pro správu svých prostředků máte dvě možnosti:

- Můžete zachovat koncového zákazníka a zadat přihlašovací údaje pro místní předplatné Azure Stack koncovému zákazníkovi.  
- Koncový zákazník může pracovat s předplatným místně a přidat CSP jako hosta s oprávněním vlastníka.  

## <a name="add-an-end-customer"></a>Přidat koncového zákazníka

Chcete-li přidat koncového zákazníka, jak je znázorněno na následujícím obrázku, proveďte následující kroky:

![Nastavení poskytovatele cloudových služeb pro sledování využití a správu účtu koncového zákazníka](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Vytvoření nového zákazníka v partnerském centru

V partnerském centru vytvořte pro zákazníka nové předplatné Azure. Pokyny najdete v tématu [Přidání nového zákazníka](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Vytvoření předplatného Azure pro koncového zákazníka

Jakmile vytvoříte záznam o zákazníkovi v partnerském centru, můžete si ho prodávat do produktů v katalogu. Pokyny najdete v tématu [Vytvoření, pozastavení nebo zrušení zákaznických předplatných](/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Vytvoření uživatele typu Host v adresáři koncového zákazníka

Ve výchozím nastavení vám jako CSP nebude mít přístup k předplatnému Azure Stack koncového zákazníka. Pokud ale zákazník chce spravovat svoje prostředky, může přidat svůj účet jako vlastníka nebo přispěvatele ke svému předplatnému Azure Stack. Aby to bylo možné, bude nutné přidat svůj účet jako uživatel typu Host do tenanta AAD. Doporučujeme použít jiný účet z vašeho účtu Azure CSP ke správě předplatného Azure Stack zákazníka, abyste se ujistili, že nepřijdete o přístup k předplatnému Azure zákazníka.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Aktualizace registrace pomocí předplatného koncového zákazníka

Aktualizujte svou registraci pomocí nového zákaznického předplatného. Azure oznamuje využívání zákazníků pomocí zákaznické identity z partnerského centra. Tento krok zajistí, že se využití každého zákazníka hlásí v rámci příslušného předplatného zprostředkovatele CSP daného zákazníka. Díky tomu je sledování využití a fakturace uživatelů jednodušší.

> [!NOTE]  
> Chcete-li provést tento krok, je nutné nejprve [zaregistrovat Azure Stack](azure-stack-registration.md).

1. Otevřete prostředí Windows PowerShell s výzvou se zvýšenými oprávněními a spusťte příkaz:  
    `Add-AzureRmAccount`
2. Zadejte svoje přihlašovací údaje Azure.
3. V relaci PowerShellu spusťte příkaz:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Parametry prostředí PowerShell pro New-AzureRmResource

V následující části jsou popsány parametry pro rutinu **New-AzureRmResource** :

| Parametr | Popis |
| --- | --- |
|registrationSubscriptionID | Předplatné Azure, které se použilo při prvotní registraci Azure Stack.|
| customerSubscriptionID | Předplatné Azure (není Azure Stack) patřící zákazníkovi k registraci. Musí být vytvořen v nabídce CSP; v praxi to znamená prostřednictvím partnerského centra. Pokud má zákazník více než jednoho klienta Azure Active Directory, musí být toto předplatné vytvořeno v tenantovi, které se bude používat pro přihlášení k Azure Stack. ID předplatného zákazníka musí používat malá písmena. |
| resourceGroup | Skupina prostředků v Azure, ve které je uložená vaše registrace. |
| registrationName | Název registrace Azure Stack. Jedná se o objekt uložený v Azure. |
| Vlastnosti | Určuje vlastnosti prostředku. Pomocí tohoto parametru můžete zadat hodnoty vlastností, které jsou specifické pro daný typ prostředku.

> [!NOTE]  
> Klienty musí být zaregistrované u každé Azure Stack, kterou používají. Pokud máte dvě Azure Stack nasazení a tenant používá oba, musíte aktualizovat počáteční registraci každého nasazení s předplatným tenanta.

### <a name="onboard-tenant-to-azure-stack"></a>Připojit klienta k Azure Stack

Nakonfigurujte Azure Stack pro podporu uživatelů z více tenantů Azure AD, aby používaly služby v Azure Stack. Pokyny najdete v tématu [Povolení víceklientské architektury v Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Vytvoření místního prostředku v klientovi koncového zákazníka v Azure Stack

Po přidání nového zákazníka do Azure Stack nebo koncový tenant zákazníka povolil váš účet Guest s oprávněními vlastníka, ověřte, že můžete vytvořit prostředek ve svém tenantovi. Například mohou [vytvořit virtuální počítač s Windows pomocí portálu Azure Stack](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Další postup

- Pokud chcete zkontrolovat chybové zprávy, pokud se spouštějí v procesu registrace, přečtěte si téma [chybové zprávy registrace klienta](azure-stack-registration-errors.md).
- Další informace o tom, jak načíst informace o využití prostředků z Azure Stack, najdete [v tématu využití a fakturace v Azure Stack](azure-stack-billing-and-chargeback.md).
- Pokud chcete zjistit, jak může koncový zákazník přidat vámi, jako jako správce pro svého tenanta Azure Stack, přečtěte si téma [povolení poskytovatele cloudové služby ke správě předplatného Azure Stack](../user/azure-stack-csp-enable-billing-usage-tracking.md).
