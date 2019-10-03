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
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 7679589090022f6f07c5e1fddb9c768716628865
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829437"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>Poznámky k verzi pro poskytovatele prostředků MySQL 1.1.30.0

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků MySQL verze 1.1.30.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků MySQL a potom spusťte samočinného extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení Azure Stack. Minimální verze Azure Stack vydaná pro instalaci této verze poskytovatele prostředků MySQL je uvedena níže:

> |Minimální verze Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Aktualizace 1808 Azure Stack (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků MySQL použijte minimální podporovanou aktualizaci Azure Stack v integrovaném systému Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack MySQL zahrnuje následující vylepšení a opravy:

- **Telemetrie se povolila pro nasazení poskytovatele prostředků MySQL**. Kolekce telemetrie je povolená pro nasazení poskytovatele prostředků MySQL. Shromážděná telemetrie zahrnuje nasazení poskytovatele prostředků, časy spuštění a zastavení, stav ukončení, zprávy ukončení a podrobnosti o chybě (Pokud je k dispozici).

- **Aktualizace šifrování TLS 1,2**. Povoleno TLS 1,2 – Podpora pouze pro komunikaci poskytovatele prostředků s interními součástmi Azure Stack. 

### <a name="fixes"></a>Opravy

- **Poskytovatel prostředků MySQL Azure Stack kompatibility PowerShellu**. Poskytovatel prostředků MySQL se aktualizoval tak, aby fungoval s Azure Stack 2018-03-01-hybridním profilem PowerShellu a zajistil kompatibilitu s AzureRM 1.3.0 a novějším.

- **Okno pro změnu přihlašovacího hesla MySQL** Opravili jsme problém, kdy se heslo nedá změnit v okně změnit heslo. Odebrali jsme odkazy z oznámení o změně hesla.

## <a name="known-issues"></a>Známé problémy 

- **SKU MySQL můžou trvat až hodinu,** než se na portálu zobrazí. Může trvat až hodinu, než se nově vytvořené skladové položky zobrazí pro použití při vytváření nových databází MySQL. 

    **Alternativní řešení**: Žádné.

- **Znovu se využívala přihlášení MySQL**. Při pokusu o vytvoření nového přihlášení MySQL se stejným uživatelským jménem, jako je existující přihlášení v rámci stejného předplatného, bude použito stejné přihlašovací jméno a stávající heslo. 

    **Alternativní řešení**: Při vytváření nových přihlašovacích údajů v rámci stejného předplatného nebo při vytváření přihlašovacích údajů se stejným uživatelským jménem v různých předplatných použijte jiná uživatelská jména.

- **Požadavek na podporu TLS 1,2**. Pokud se pokusíte nasadit nebo aktualizovat poskytovatele prostředků MySQL z počítače, kde není povolený protokol TLS 1,2, může operace selhat. Spusťte následující příkaz PowerShellu na počítači, který se používá k nasazení nebo aktualizaci poskytovatele prostředků, aby se ověřilo, že se TLS 1,2 vrátí jako podporovaný:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Pokud **Tls12** není součástí výstupu příkazu, TLS 1,2 není v počítači povolen.

    **Alternativní řešení**: Spuštěním následujícího příkazu PowerShellu povolte TLS 1,2 a potom spusťte nasazení poskytovatele prostředků nebo aktualizujte skript ze stejné relace PowerShellu:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro cloudové správce, kteří pracují Azure Stack
Informace najdete v dokumentaci k [verzi Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md). 