---
title: 1\.1.30.0 zpráva k vydání verze poskytovatele prostředků MySQL centra pro Azure Stack
description: Podívejte se na poznámky k verzi a zjistěte, co je nového ve 1.1.30.0 aktualizace poskytovatele prostředků MySQL centra pro Azure Stack.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 710b5d48e4ce906eb357c5e17cff4132bcd747ba
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698925"
---
# <a name="mysql-resource-provider-11300-release-notes"></a>Poznámky k verzi poskytovatele 1.1.30.0 prostředků MySQL

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi poskytovatele prostředků MySQL verze 1.1.30.0.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků MySQL a potom spusťte samočinného extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků MySQL, je uvedena níže:

> |Minimální verze centra Azure Stack|Verze poskytovatele prostředků MySQL|
> |-----|-----|
> |Aktualizace centra Azure Stack 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Než nasadíte nejnovější verzi poskytovatele prostředků MySQL, použijte pro integrovaný systém centra Azure Stack minimálně podporu Azure Stack centra nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků centra Azure Stacku MySQL zahrnuje následující vylepšení a opravy:

- **Telemetrie se povolila pro nasazení poskytovatele prostředků MySQL**. Kolekce telemetrie je povolená pro nasazení poskytovatele prostředků MySQL. Shromážděná telemetrie zahrnuje nasazení poskytovatele prostředků, časy spuštění a zastavení, stav ukončení, zprávy ukončení a podrobnosti o chybě (Pokud je k dispozici).

- **Aktualizace šifrování TLS 1,2**. Povoleno TLS 1,2 – Podpora pouze pro komunikaci poskytovatele prostředků s interními komponentami centra Azure Stack. 

### <a name="fixes"></a>Opravy

- **Poskytovatel prostředků MySQL Azure Stack kompatibilitou prostředí PowerShell centra**. Poskytovatel prostředků MySQL se aktualizoval tak, aby fungoval s profilem Azure Stack hub 2018-03-01 – Hybrid PowerShellu a zajistil kompatibilitu s AzureRM 1.3.0 a novějším.

- **Okno pro změnu přihlašovacího hesla MySQL** Opravili jsme problém, kdy se heslo nedá změnit v okně změnit heslo. Odebrali jsme odkazy z oznámení o změně hesla.

## <a name="known-issues"></a>Známé problémy

- **SKU MySQL můžou trvat až hodinu,** než se na portálu zobrazí. Může trvat až hodinu, než se nově vytvořené skladové položky zobrazí pro použití při vytváření nových databází MySQL.

    **Alternativní řešení**: žádné.

- **Znovu se využívala přihlášení MySQL**. Při pokusu o vytvoření nového přihlášení MySQL se stejným uživatelským jménem, jako je existující přihlášení v rámci stejného předplatného, bude použito stejné přihlašovací jméno a stávající heslo.

    **Alternativní řešení**: při vytváření nových přihlášení v rámci stejného předplatného použijte jiná uživatelská jména nebo vytvořte přihlášení se stejným uživatelským jménem v různých předplatných.

- **Požadavek na podporu TLS 1,2**. Pokud se pokusíte nasadit nebo aktualizovat poskytovatele prostředků MySQL z počítače, kde není povolený protokol TLS 1,2, může operace selhat. Spusťte následující příkaz PowerShellu na počítači, který se používá k nasazení nebo aktualizaci poskytovatele prostředků, aby se ověřilo, že se TLS 1,2 vrátí jako podporovaný:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Pokud **Tls12** není součástí výstupu příkazu, TLS 1,2 není v počítači povolen.

    **Alternativní řešení**: spusťte následující příkaz PowerShellu pro povolení TLS 1,2 a potom spusťte nasazení poskytovatele prostředků nebo aktualizujte skript ze stejné relace PowerShellu:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Známé problémy pro cloudová správce, kteří pracují Azure Stack hub
Informace najdete v dokumentaci k [verzi centra Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků MySQL](azure-stack-mysql-resource-provider.md).

[Připravte se na nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků MySQL z předchozí verze](azure-stack-mysql-resource-provider-update.md). 