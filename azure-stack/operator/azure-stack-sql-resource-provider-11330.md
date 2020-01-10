---
title: 1\.1.33.0 zpráva k vydání verze poskytovatele prostředků SQL centra pro Azure Stack
titleSuffix: Azure Stack Hub
description: Prohlédněte si poznámky k vydání pro poskytovatele prostředků SQL centra Azure Stack 1.1.33.0 Update.
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
ms.openlocfilehash: 1920e699704c9c5343885b86a38b589ddc6e13a5
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814639"
---
# <a name="sql-resource-provider-11330-release-notes"></a>1\.1.33.0 poznámky k verzi poskytovatele prostředků SQL

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi 1.1.33.0 poskytovatele prostředků SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků SQL, je uvedena níže:

> |Minimální verze centra Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 1808 (1.1808.0.97)|[SQL RP verze 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků SQL použijte minimální podporovanou aktualizaci centra Azure Stack do integrovaného systému služby Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků SQL centra Azure Stack zahrnuje následující vylepšení a opravy:

### <a name="fixes"></a>Opravy

- **Rozšíření portálu poskytovatele prostředků SQL může zvolit špatné předplatné**. Poskytovatel prostředků SQL používá Azure Resource Manager volání k určení prvního předplatného Správce služby, které se používá, což nemusí být *výchozí předplatné poskytovatele*. Pokud k tomu dojde, poskytovatel prostředků SQL nepracuje normálně.

- **Hostitelský server SQL neuvádí hostované databáze.** Uživatelem vytvořené databáze nemusí být uvedeny při zobrazení prostředků tenanta pro hostitelské servery SQL.

- **Předchozí nasazení poskytovatele prostředků SQL (1.1.30.0) by mohlo selhat, pokud není povolený protokol TLS 1,2**. Aktualizovaný poskytovatel prostředků SQL 1.1.33.0, aby povoloval TLS 1,2 při nasazování poskytovatele prostředků, aktualizaci poskytovatele prostředků nebo střídání tajných klíčů.

- **Rotace tajného kódu poskytovatele prostředků SQL se nezdařila**. Při střídání tajných klíčů došlo k problému, který má za následek následující kód chyby: `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>Známé problémy

- Zobrazení **SKU SQL může trvat až hodinu**, než se na portálu zobrazí. Může trvat až hodinu, než se nově vytvořené skladové položky zobrazí pro použití při vytváření nových databází SQL.

    **Řešení**: Žádné

- **Znovu použita přihlášení SQL**. Při pokusu o vytvoření nového přihlášení SQL se stejným uživatelským jménem, jako má existující přihlašovací jméno v rámci stejného předplatného, bude použito stejné přihlášení a stávající heslo.

    **Alternativní řešení**: při vytváření nových přihlášení v rámci stejného předplatného použijte jiná uživatelská jména nebo vytvořte přihlášení se stejným uživatelským jménem v různých předplatných.

- **Sdílená přihlášení SQL způsobují nekonzistenci dat**. Pokud je přihlašovací jméno SQL sdílené pro více databází SQL v rámci stejného předplatného, Změna přihlašovacího hesla způsobí nekonzistenci dat.

    **Alternativní řešení**: pro různé databáze v rámci stejného předplatného používejte vždycky jiná přihlášení.

- **Poskytovatel prostředků SQL nemůže přidat SQL Server vždy na naslouchací proces**. Při použití IP adresy naslouchacího procesu pro naslouchací proces služby SQL Server Always On nemůže virtuální počítač poskytovatele prostředků SQL přeložit název hostitele naslouchacího procesu.

    **Alternativní řešení**: Ujistěte se, že služba DNS správně funguje pro překlad IP adresy naslouchacího procesu na název hostitele naslouchacího procesu.

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Známé problémy pro cloudová správce, kteří pracují Azure Stack hub
Informace najdete v dokumentaci k [verzi centra Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md).