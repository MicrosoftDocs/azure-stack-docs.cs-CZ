---
title: 1\.1.30.0 zpráva k vydání verze poskytovatele prostředků SQL centra pro Azure Stack
titleSuffix: Azure Stack Hub
description: Prohlédněte si poznámky k vydání pro poskytovatele prostředků SQL centra Azure Stack 1.1.30.0 Update.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 1b070538171024a34f3ba041ba756220e38d44b9
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491879"
---
# <a name="sql-resource-provider-11300-release-notes"></a>Poznámky k verzi 1.1.30.0 poskytovatele prostředků SQL

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi 1.1.30.0 poskytovatele prostředků SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack. Minimální verze Azure Stack centra pro vydání, která je vyžadována pro instalaci této verze poskytovatele prostředků SQL, je uvedena níže:

> |Minimální verze centra Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků SQL použijte pro integrovaný systém služby Azure Stack hub minimální podporovanou aktualizaci centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků SQL centra Azure Stack zahrnuje následující vylepšení a opravy:

- **Telemetrie se povolila pro nasazení poskytovatele prostředků SQL**. Kolekce telemetrie je povolená pro nasazení poskytovatele prostředků SQL. Shromážděná telemetrie zahrnuje nasazení poskytovatele prostředků, časy spuštění a zastavení, stav ukončení, zprávy ukončení a podrobnosti o chybě (Pokud je k dispozici).

- **Aktualizace šifrování TLS 1,2**. Povoleno TLS 1,2 – Podpora pouze pro komunikaci poskytovatele prostředků s interními komponentami centra Azure Stack. 

### <a name="fixes"></a>Opravy

- **Poskytovatel prostředků SQL Azure Stack kompatibility PowerShellu centra**. Poskytovatel prostředků SQL se aktualizoval tak, aby fungoval s profilem Azure Stack hub 2018-03-01 – Hybrid PowerShellu a zajistil kompatibilitu s AzureRM 1.3.0 a novějším.

- **Okno heslo pro změnu přihlašovacího údaje SQL** Opravili jsme problém, kdy se heslo nedá změnit v okně změnit heslo. Odebrali jsme odkazy z oznámení o změně hesla.

- **Aktualizuje se okno nastavení hostitelského serveru SQL**. Opravili jsme problém, kdy se okno nastavení nesprávně vyvolalo jako heslo.

## <a name="known-issues"></a>Známé problémy

- Zobrazení **SKU SQL může trvat až hodinu**, než se na portálu zobrazí. Může trvat až hodinu, než se nově vytvořené skladové položky zobrazí pro použití při vytváření nových databází SQL.

    **Alternativní řešení**: žádné.

- **Znovu použita přihlášení SQL**. Při pokusu o vytvoření nového přihlášení SQL se stejným uživatelským jménem, jako má existující přihlašovací jméno v rámci stejného předplatného, bude použito stejné přihlášení a stávající heslo.

    **Alternativní řešení**: při vytváření nových přihlášení v rámci stejného předplatného použijte jiná uživatelská jména nebo vytvořte přihlášení se stejným uživatelským jménem v různých předplatných.

- **Sdílená přihlášení SQL způsobují nekonzistenci dat**. Pokud je přihlašovací jméno SQL sdílené pro více databází SQL v rámci stejného předplatného, Změna přihlašovacího hesla způsobí nekonzistenci dat.

    **Alternativní řešení**: pro různé databáze v rámci stejného předplatného používejte vždycky jiná přihlášení.

- **Požadavek na podporu TLS 1,2**. Pokud se pokusíte nasadit nebo aktualizovat poskytovatele prostředků SQL z počítače, kde není povolený protokol TLS 1,2, může operace selhat. Spusťte následující příkaz PowerShellu na počítači, který se používá k nasazení nebo aktualizaci poskytovatele prostředků, aby se ověřilo, že se TLS 1,2 vrátí jako podporovaný:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Pokud **Tls12** není součástí výstupu příkazu, TLS 1,2 není v počítači povolen.

    **Alternativní řešení**: spusťte následující příkaz PowerShellu pro povolení TLS 1,2 a potom spusťte nasazení poskytovatele prostředků nebo aktualizujte skript ze stejné relace PowerShellu:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```

- **Poskytovatel prostředků SQL nemůže přidat SQL Server vždy na naslouchací proces**. Při použití IP adresy naslouchacího procesu pro naslouchací proces služby SQL Server Always On nemůže virtuální počítač poskytovatele prostředků SQL přeložit název hostitele naslouchacího procesu.

    **Alternativní řešení**: Ujistěte se, že služba DNS správně funguje pro překlad IP adresy naslouchacího procesu na název hostitele naslouchacího procesu.

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Známé problémy pro cloudová správce, kteří pracují Azure Stack hub
Informace najdete v dokumentaci k [verzi centra Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md).