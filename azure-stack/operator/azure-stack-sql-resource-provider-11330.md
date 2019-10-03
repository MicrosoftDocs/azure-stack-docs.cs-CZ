---
title: Azure Stack 1.1.30.0 zpráva k vydání verze poskytovatele prostředků SQL | Microsoft Docs
description: Přečtěte si o tom, co je v nejnovější aktualizaci poskytovatele prostředků SQL Azure Stack, včetně všech známých problémů a místa, kde si je můžete stáhnout.
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
ms.openlocfilehash: 69ea42a9efbf57cfdeb589cc221eae8a9f21913c
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829325"
---
# <a name="sql-resource-provider-11330-release-notes"></a>1\.1.33.0 poznámky k verzi poskytovatele prostředků SQL

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi 1.1.33.0 poskytovatele prostředků SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení Azure Stack. Minimální verze Azure Stack vydaná pro instalaci této verze poskytovatele prostředků SQL je uvedená níže:

> |Minimální verze Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 1808 (1.1808.0.97)|[SQL RP verze 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků SQL použijte minimální podporovanou aktualizaci Azure Stack v integrovaném systému Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack SQL zahrnuje následující vylepšení a opravy:

### <a name="fixes"></a>Opravy
- **Rozšíření portálu poskytovatele prostředků SQL může zvolit špatné předplatné**. Poskytovatel prostředků SQL používá Azure Resource Manager volání k určení prvního předplatného Správce služby, které se používá, což nemusí být *výchozí předplatné poskytovatele*. Pokud k tomu dojde, poskytovatel prostředků SQL nepracuje normálně. 

- **Hostitelský server SQL nezobrazuje seznam hostovaných databází.** Uživatelem vytvořené databáze nemusí být uvedeny při zobrazení prostředků tenanta pro hostitelské servery SQL.

- **Předchozí nasazení poskytovatele prostředků SQL (1.1.30.0) může selhat, pokud není TLS 1,2 povolený**. Aktualizovaný poskytovatel prostředků SQL 1.1.33.0, aby povoloval TLS 1,2 při nasazování poskytovatele prostředků, aktualizaci poskytovatele prostředků nebo střídání tajných klíčů. 

- **Rotace tajného kódu poskytovatele prostředků SQL**se nezdařila. Při střídání tajných klíčů došlo k problému, který má za následek následující kód chyby:`New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Známé problémy 

- Zobrazení **SKU SQL může trvat až hodinu**, než se na portálu zobrazí. Může trvat až hodinu, než se nově vytvořené skladové položky zobrazí pro použití při vytváření nových databází SQL. 

    **Alternativní řešení**: Žádné.

- **Znovu použita přihlášení SQL**. Při pokusu o vytvoření nového přihlášení SQL se stejným uživatelským jménem, jako má existující přihlašovací jméno v rámci stejného předplatného, bude použito stejné přihlášení a stávající heslo. 

    **Alternativní řešení**: Při vytváření nových přihlašovacích údajů v rámci stejného předplatného nebo při vytváření přihlašovacích údajů se stejným uživatelským jménem v různých předplatných použijte jiná uživatelská jména.

- **Sdílená přihlášení SQL způsobují nekonzistenci dat**. Pokud je přihlašovací jméno SQL sdílené pro více databází SQL v rámci stejného předplatného, Změna přihlašovacího hesla způsobí nekonzistenci dat.

    **Alternativní řešení**: Pro různé databáze v rámci stejného předplatného používejte vždycky jiná přihlášení.

- **Poskytovatel prostředků SQL nemůže přidat SQL Server vždy na naslouchací proces**. Při použití IP adresy naslouchacího procesu pro naslouchací proces služby SQL Server Always On nemůže virtuální počítač poskytovatele prostředků SQL přeložit název hostitele naslouchacího procesu.

    **Alternativní řešení**: Ujistěte se, že služba DNS správně funguje pro překlad IP adresy naslouchacího procesu na název hostitele naslouchacího procesu.

### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro cloudové správce, kteří pracují Azure Stack
Informace najdete v dokumentaci k [verzi Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md). 