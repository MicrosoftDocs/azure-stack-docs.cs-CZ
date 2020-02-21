---
title: Vytváření databází MySQL v centru Azure Stack
description: Naučte se vytvářet a spravovat databáze MySQL zřízené pomocí poskytovatele prostředků adaptéru MySQL v Azure Stack hub.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: afe2a9fe46a5abae9f94347422153480536c37bc
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492117"
---
# <a name="create-mysql-databases-in-azure-stack-hub"></a>Vytváření databází MySQL v centru Azure Stack
Uživatel centra Azure Stack, který se přihlásí k odběru nabídky, která obsahuje databázovou službu MySQL, může vytvořit a spravovat samoobslužné databáze MySQL na portálu User Portal.

## <a name="create-a-mysql-database"></a>Vytvoření databáze MySQL

1. Přihlaste se k portálu pro uživatele centra Azure Stack.
2. Vyberte **+ vytvořit prostředek** > **Data + úložiště** > **MySQL Database** > **Přidat**.
3. V části **vytvořit databázi MySQL**zadejte název databáze a nakonfigurujte další nastavení podle požadavků vašeho prostředí.

    ![Vytvoření testovací databáze MySQL](./media/azure-stack-mysql-rp-deploy/mysql-create-db-a.png)

4. V části **vytvořit databázi**vyberte **SKU**. V části **Vybrat SKU MySQL**vyberte SKU pro vaši databázi.

    ![Vybrat SKU MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >Jelikož se hostitelské servery přidávají do centra Azure Stack, přiřadí se mu SKU. Databáze se vytvářejí ve fondu hostitelských serverů v SKU.

5. V části **přihlášení**vyberte ***konfigurovat požadovaná nastavení***.
6. V části **Vybrat přihlášení**můžete zvolit existující přihlášení nebo vybrat **+ vytvořit nové přihlášení** a nastavit nové přihlášení.  Zadejte přihlašovací jméno a **heslo** **databáze** a pak vyberte **OK**.

    ![Vytvořit nové přihlášení k databázi](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >Délka přihlašovacího jména databáze nesmí překročit 32 znaků v MySQL 5,7. V dřívějších edicích nemůže být delší než 16 znaků.

7. Výběrem **vytvořit** dokončete nastavování databáze.

Po nasazení databáze si poznamenejte **připojovací řetězec** v části **základy**. Tento řetězec můžete použít v libovolné aplikaci, která potřebuje přístup k databázi MySQL.

![Získání připojovacího řetězce pro databázi MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created-a.png)

## <a name="update-the-administrative-password"></a>Aktualizace hesla pro správu

Heslo můžete upravit tak, že ho změníte v instanci serveru MySQL.

1. Vyberte **prostředky pro správu** > **hostitelských serverech MySQL**. Vyberte hostitelský server.
2. V části **Nastavení**vyberte **heslo**.
3. V části **heslo**zadejte nové heslo a pak vyberte **Uložit**.

![Aktualizace hesla správce](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nabízet vysoce dostupné databáze MySQL](azure-stack-tutorial-mysql.md).
