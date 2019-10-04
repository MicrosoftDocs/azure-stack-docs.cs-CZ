---
title: Používání databází poskytovaných adaptérem MySQL RP v Azure Stack | Microsoft Docs
description: Jak vytvářet a spravovat databáze MySQL zřízené pomocí poskytovatele prostředků adaptéru MySQL
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
ms.openlocfilehash: 6a933f812edeede1802c35e3d25915ce09b6f01b
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909218"
---
# <a name="create-mysql-databases"></a>Vytváření databází MySQL
Uživatel Azure Stack, který se přihlásí k odběru nabídky, která obsahuje databázovou službu MySQL, může vytvořit a spravovat samoobslužné databáze MySQL na portálu User Portal.

## <a name="create-a-mysql-database"></a>Vytvoření databáze MySQL

1. Přihlaste se k portálu Azure Stack User Portal.
2. Vyberte **+ vytvořit prostředek** > **data + úložiště** > **MySQL databáze** > **Přidat**.
3. V části **vytvořit databázi MySQL**zadejte název databáze a nakonfigurujte další nastavení podle požadavků vašeho prostředí.

    ![Vytvoření testovací databáze MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. V části **vytvořit databázi**vyberte **SKU**. V části **Vybrat SKU MySQL**vyberte SKU pro vaši databázi.

    ![Vybrat SKU MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Když se do Azure Stack přidají hostitelské servery, přiřadí se mu SKU. Databáze se vytvářejí ve fondu hostitelských serverů v SKU.

5. V části **přihlášení**vyberte ***konfigurovat požadovaná nastavení***.
6. V části **Vybrat přihlášení**můžete zvolit existující přihlášení nebo vybrat **+ vytvořit nové přihlášení** a nastavit nové přihlášení.  Zadejte přihlašovací jméno a **heslo** **databáze** a pak vyberte **OK**.

    ![Vytvořit nové přihlášení k databázi](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Délka přihlašovacího jména databáze nesmí překročit 32 znaků v MySQL 5,7. V dřívějších edicích nemůže být delší než 16 znaků.

7. Výběrem **vytvořit** dokončete nastavování databáze.

Po nasazení databáze si poznamenejte **připojovací řetězec** v části **základy**. Tento řetězec můžete použít v libovolné aplikaci, která potřebuje přístup k databázi MySQL.

![Získání připojovacího řetězce pro databázi MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Aktualizace hesla pro správu

Heslo můžete upravit tak, že ho změníte v instanci serveru MySQL.

1.  > Vyberte **prostředky pro správu** **servery MySQL hosting**. Vyberte hostitelský server.
2. V části **Nastavení**vyberte **heslo**.
3. V části **heslo**zadejte nové heslo a pak vyberte **Uložit**.

![Aktualizace hesla správce](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Další kroky

[Aktualizace poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-update.md)
