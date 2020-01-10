---
title: Vytvoření databáze SQL
titleSuffix: Azure Stack Hub
description: Naučte se vytvářet a spravovat databáze SQL zřízené pomocí adaptéru poskytovatele prostředků SQL.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: f6c030767ba64aa3c8acd47d5b358a4b385785ac
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814537"
---
# <a name="create-sql-databases"></a>Vytvoření databáze SQL

Samoobslužné databáze můžete vytvářet a spravovat na portálu User Portal. Uživatel centra Azure Stack potřebuje předplatné s nabídkou, která zahrnuje službu SQL Database.

1. Přihlaste se k portálu pro uživatele [centra Azure Stack](azure-stack-overview.md) .

2. Vyberte **+ nový** &gt;**Data + úložiště** &gt; **SQL Server Database** &gt; **Přidat**.

3. V části **vytvořit databázi**zadejte požadované informace, například **název databáze** a **maximální velikost v MB**.

   >[!NOTE]
   >Velikost databáze musí být minimálně 64 MB, což se dá po nasazení databáze zvětšit.

   Nakonfigurujte další nastavení podle požadavků vašeho prostředí.

4. V části **vytvořit databázi**vyberte **SKU**. V části **Vybrat SKU**vyberte SKU pro vaši databázi.

   ![Vytvořte databázi na portálu User Portal pro Azure Stack hub.](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >Jelikož se hostitelské servery přidávají do centra Azure Stack, přiřadí se mu SKU. Databáze se vytvářejí ve fondu hostitelských serverů v SKU.

5. Vyberte **Přihlásit**se.

6. V části **Vybrat přihlášení**zvolte existující přihlášení nebo vyberte **+ vytvořit nové přihlášení**.

7. V části **nové přihlášení**zadejte jméno a **heslo**pro **přihlášení k databázi** .

   >[!NOTE]
   >Tato nastavení jsou přihlašovací údaje pro ověřování SQL, které jsou vytvořené pro váš přístup jenom k této databázi. Přihlašovací uživatelské jméno musí být globálně jedinečné. Můžete znovu použít nastavení přihlášení pro další databáze, které používají stejnou SKU.

   ![Vytvoření nového přihlášení k databázi na portálu Azure Stack User Portal](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Kliknutím na **OK** dokončete nasazení databáze.

V části **Essentials**, která se zobrazí po nasazení databáze, si poznamenejte **připojovací řetězec**. Tento řetězec můžete použít v libovolné aplikaci, která potřebuje přístup k databázi SQL Server.

![Načtení připojovacího řetězce pro databázi SQL Server](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Databáze SQL Always On

V databázích, které jsou vždy zpracovávány, jsou databáze vždy zpracovávány jinak než v samostatném serverovém prostředí. Další informace najdete v tématu [představení skupin dostupnosti Always on SQL Server na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Ověření databází SQL Always On

Následující snímek obrazovky ukazuje, jak můžete použít SQL Server Management Studio k zobrazení stavu databáze v SQL Always On.

![Stav databáze AlwaysOn v SQL Server Management Studio](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Databáze Always On by se měly zobrazovat jako **synchronizované** a dostupné ve všech instancích SQL a zobrazují se ve **skupinách dostupnosti**. V předchozím snímku obrazovky je příklad databáze newdb1 a jeho stav je **newdb1 (synchronizovaný)** .

### <a name="delete-an-alwayson-database"></a>Odstranění databáze AlwaysOn

Když odstraníte databázi SQL Always On z poskytovatele prostředků, SQL databázi odstraní z **primární** repliky a ze skupiny dostupnosti.

SQL pak převede databázi do stavu **obnovování** v ostatních replikách a nevyřadí databázi, pokud se neaktivuje. Pokud databáze není vyřazena, sekundární replika přejde do stavu **nesynchronizace** .

## <a name="next-steps"></a>Další kroky

Informace o tom, jak [nabízet vysoce dostupné databáze SQL](azure-stack-tutorial-sql.md)
