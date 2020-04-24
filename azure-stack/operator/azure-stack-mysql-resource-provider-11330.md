---
title: 1.1.33.0 zpráva k vydání verze poskytovatele prostředků MySQL centra pro Azure Stack
description: Podívejte se na poznámky k verzi a zjistěte, co je nového ve 1.1.33.0 aktualizace poskytovatele prostředků MySQL centra pro Azure Stack.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 4cab915bc4b824c1a178d94aaa571ca71c9c948b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698908"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Poznámky k verzi pro poskytovatele prostředků MySQL 1.1.33.0

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků MySQL verze 1.1.33.0.

## <a name="build-reference"></a>Odkaz na sestavení
Stáhněte si binární soubor poskytovatele prostředků MySQL a potom spusťte samočinného extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků MySQL, je uvedena níže:

> |Minimální verze centra Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Verze 1808 (1.1808.0.97)|[MySQL RP verze 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Než nasadíte nejnovější verzi poskytovatele prostředků MySQL, použijte pro integrovaný systém centra Azure Stack minimálně podporu Azure Stack centra nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků centra Azure Stacku MySQL zahrnuje následující vylepšení a opravy:

### <a name="fixes"></a>Opravy

- **Rozšíření portálu poskytovatele prostředků MySQL může zvolit špatné předplatné**. Poskytovatel prostředků MySQL používá Azure Resource Manager volání k určení prvního předplatného Správce služby, které se používá, což nemusí být *výchozí předplatné poskytovatele*. Pokud k tomu dojde, poskytovatel prostředků MySQL nepracuje normálně.

- **Hostující Server MySQL neuvádí hostované databáze.** Uživatelem vytvořené databáze nemusí být vypsány při zobrazení prostředků tenanta pro hostitelské servery MySQL.

- **Předchozí nasazení poskytovatele prostředků MySQL (1.1.30.0) by mohlo selhat, pokud není povolený protokol TLS 1,2**. Aktualizovali jsme poskytovatele prostředků MySQL 1.1.33.0, aby při nasazování poskytovatele prostředků, aktualizaci poskytovatele prostředků nebo střídání tajných klíčů povolil protokol TLS 1,2.

- **Rotace tajného kódu poskytovatele prostředků MySQL se nezdařila**. Opravili jsme problém, který při střídání tajných kódů vrátil následující chybový kód:`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Známé problémy

- **SKU MySQL můžou trvat až hodinu,** než se na portálu zobrazí. Může trvat až hodinu, než se nově vytvořené skladové položky zobrazí pro použití při vytváření nových databází MySQL. 

    **Alternativní řešení**: žádné.

- **Znovu se využívala přihlášení MySQL**. Při pokusu o vytvoření nového přihlášení MySQL se stejným uživatelským jménem, jako je existující přihlášení v rámci stejného předplatného, bude použito stejné přihlašovací jméno a stávající heslo.

    **Alternativní řešení**: při vytváření nových přihlášení v rámci stejného předplatného použijte jiná uživatelská jména nebo vytvořte přihlášení se stejným uživatelským jménem v různých předplatných.

- **Sdílená přihlášení MySQL způsobují nekonzistenci dat**. Pokud je přihlášení MySQL sdíleno pro více databází MySQL v rámci stejného předplatného, Změna přihlašovacího hesla způsobí nekonzistenci dat.

    **Alternativní řešení**: pro různé databáze v rámci stejného předplatného používejte vždycky jiná přihlášení.


### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Známé problémy pro cloudová správce, kteří pracují Azure Stack hub
Informace najdete v dokumentaci k [verzi centra Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md). 