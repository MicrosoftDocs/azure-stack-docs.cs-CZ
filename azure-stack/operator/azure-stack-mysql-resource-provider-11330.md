---
title: 1\.1.30.0 poznámky k verzi poskytovatele prostředků MySQL | Azure Stack Microsoft Docs
description: Seznamte se s nejnovější aktualizací poskytovatele prostředků MySQL Azure Stack, včetně všech známých problémů a místa, kde ho stáhnout.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: fdac6c099980a1c2cedb3271123908539d18169d
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909166"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>Poznámky k verzi pro poskytovatele prostředků MySQL 1.1.33.0

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků MySQL verze 1.1.33.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků MySQL a potom spusťte samočinného extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení Azure Stack. Minimální verze Azure Stack vydaná pro instalaci této verze poskytovatele prostředků MySQL je uvedena níže:

> |Minimální verze Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Verze 1808 (1.1808.0.97)|[MySQL RP verze 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků MySQL použijte minimální podporovanou aktualizaci Azure Stack v integrovaném systému Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack MySQL zahrnuje následující vylepšení a opravy:

### <a name="fixes"></a>Opravy
- **Rozšíření portálu poskytovatele prostředků MySQL může zvolit špatné předplatné**. Poskytovatel prostředků MySQL používá Azure Resource Manager volání k určení prvního předplatného Správce služby, které se používá, což nemusí být *výchozí předplatné poskytovatele*. Pokud k tomu dojde, poskytovatel prostředků MySQL nepracuje normálně. 

- **Hostující Server MySQL nezobrazuje seznam hostovaných databází.** Uživatelem vytvořené databáze nemusí být vypsány při zobrazení prostředků tenanta pro hostitelské servery MySQL.

- **Předchozí nasazení poskytovatele prostředků MySQL (1.1.30.0) by mohlo selhat, pokud není TLS 1,2 povolený**. Aktualizovali jsme poskytovatele prostředků MySQL 1.1.33.0, aby při nasazování poskytovatele prostředků, aktualizaci poskytovatele prostředků nebo střídání tajných klíčů povolil protokol TLS 1,2. 

- **Rotace tajného kódu poskytovatele prostředků MySQL**se nezdařila. Při střídání tajných klíčů došlo k problému, který má za následek následující kód chyby:`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Známé problémy 

- **SKU MySQL můžou trvat až hodinu,** než se na portálu zobrazí. Může trvat až hodinu, než se nově vytvořené skladové položky zobrazí pro použití při vytváření nových databází MySQL. 

    **Alternativní řešení**: Žádné.

- **Znovu se využívala přihlášení MySQL**. Při pokusu o vytvoření nového přihlášení MySQL se stejným uživatelským jménem, jako je existující přihlášení v rámci stejného předplatného, bude použito stejné přihlašovací jméno a stávající heslo. 

    **Alternativní řešení**: Při vytváření nových přihlašovacích údajů v rámci stejného předplatného nebo při vytváření přihlašovacích údajů se stejným uživatelským jménem v různých předplatných použijte jiná uživatelská jména.

- **Sdílená přihlášení MySQL způsobují nekonzistenci dat**. Pokud je přihlášení MySQL sdíleno pro více databází MySQL v rámci stejného předplatného, Změna přihlašovacího hesla způsobí nekonzistenci dat.

    **Alternativní řešení**: Pro různé databáze v rámci stejného předplatného používejte vždycky jiná přihlášení.


### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro cloudové správce, kteří pracují Azure Stack
Informace najdete v dokumentaci k [verzi Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md). 