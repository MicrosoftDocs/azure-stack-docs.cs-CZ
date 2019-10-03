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
ms.openlocfilehash: f17c2ba41097d5b9bda903ae5d95c62e0ac9f53a
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829359"
---
# <a name="sql-resource-provider-11300-release-notes"></a>Poznámky k verzi 1.1.30.0 poskytovatele prostředků SQL

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a známé problémy ve verzi 1.1.30.0 poskytovatele prostředků SQL.

## <a name="build-reference"></a>Referenční informace o buildu
Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení Azure Stack. Minimální verze Azure Stack vydaná pro instalaci této verze poskytovatele prostředků SQL je uvedená níže:

> |Minimální verze Azure Stack|Verze zprostředkovatele prostředků SQL|
> |-----|-----|
> |Verze 1808 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> Před nasazením nejnovější verze poskytovatele prostředků SQL použijte minimální podporovanou aktualizaci Azure Stack v integrovaném systému Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato verze poskytovatele prostředků Azure Stack SQL zahrnuje následující vylepšení a opravy:

- **Telemetrie se povolila pro nasazení poskytovatele prostředků SQL**. Kolekce telemetrie je povolená pro nasazení poskytovatele prostředků SQL. Shromážděná telemetrie zahrnuje nasazení poskytovatele prostředků, časy spuštění a zastavení, stav ukončení, zprávy ukončení a podrobnosti o chybě (Pokud je k dispozici).

- **Aktualizace šifrování TLS 1,2**. Povoleno TLS 1,2 – Podpora pouze pro komunikaci poskytovatele prostředků s interními součástmi Azure Stack. 

### <a name="fixes"></a>Opravy

- **Poskytovatel prostředků SQL Azure Stack kompatibility PowerShellu**. Poskytovatel prostředků SQL se aktualizoval tak, aby fungoval s Azure Stack 2018-03-01-hybridním profilem PowerShellu a zajistil kompatibilitu s AzureRM 1.3.0 a novějším.

- **Okno heslo pro změnu přihlašovacího údaje SQL** Opravili jsme problém, kdy se heslo nedá změnit v okně změnit heslo. Odebrali jsme odkazy z oznámení o změně hesla.

- **Aktualizuje se okno nastavení hostitelského serveru SQL**. Opravili jsme problém, kdy se okno nastavení nesprávně vyvolalo jako heslo.

## <a name="known-issues"></a>Známé problémy 

- Zobrazení **SKU SQL může trvat až hodinu**, než se na portálu zobrazí. Může trvat až hodinu, než se nově vytvořené skladové položky zobrazí pro použití při vytváření nových databází SQL. 

    **Alternativní řešení**: Žádné.

- **Znovu použita přihlášení SQL**. Při pokusu o vytvoření nového přihlášení SQL se stejným uživatelským jménem, jako má existující přihlašovací jméno v rámci stejného předplatného, bude použito stejné přihlášení a stávající heslo. 

    **Alternativní řešení**: Při vytváření nových přihlašovacích údajů v rámci stejného předplatného nebo při vytváření přihlašovacích údajů se stejným uživatelským jménem v různých předplatných použijte jiná uživatelská jména.

- **Sdílená přihlášení SQL způsobují nekonzistenci dat**. Pokud je přihlašovací jméno SQL sdílené pro více databází SQL v rámci stejného předplatného, Změna přihlašovacího hesla způsobí nekonzistenci dat.

    **Alternativní řešení**: Pro různé databáze v rámci stejného předplatného používejte vždycky jiná přihlášení.

- **Požadavek na podporu TLS 1,2**. Pokud se pokusíte nasadit nebo aktualizovat poskytovatele prostředků SQL z počítače, kde není povolený protokol TLS 1,2, může operace selhat. Spusťte následující příkaz PowerShellu na počítači, který se používá k nasazení nebo aktualizaci poskytovatele prostředků, aby se ověřilo, že se TLS 1,2 vrátí jako podporovaný:

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  Pokud **Tls12** není součástí výstupu příkazu, TLS 1,2 není v počítači povolen.

    **Alternativní řešení**: Spuštěním následujícího příkazu PowerShellu povolte TLS 1,2 a potom spusťte nasazení poskytovatele prostředků nebo aktualizujte skript ze stejné relace PowerShellu:

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
- **Poskytovatel prostředků SQL nemůže přidat SQL Server vždy na naslouchací proces**. Při použití IP adresy naslouchacího procesu pro naslouchací proces služby SQL Server Always On nemůže virtuální počítač poskytovatele prostředků SQL přeložit název hostitele naslouchacího procesu.

    **Alternativní řešení**: Ujistěte se, že služba DNS správně funguje pro překlad IP adresy naslouchacího procesu na název hostitele naslouchacího procesu.
    
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Známé problémy pro cloudové správce, kteří pracují Azure Stack
Informace najdete v dokumentaci k [verzi Azure Stack](azure-stack-servicing-policy.md).

## <a name="next-steps"></a>Další kroky
[Přečtěte si další informace o poskytovateli prostředků SQL](azure-stack-sql-resource-provider.md).

[Příprava na nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md#prerequisites).

[Upgradujte poskytovatele prostředků SQL z předchozí verze](azure-stack-sql-resource-provider-update.md). 