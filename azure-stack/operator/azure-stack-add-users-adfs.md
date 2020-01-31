---
title: Přidání uživatelů centra Azure Stack v AD FS
description: Naučte se přidávat uživatele centra Azure Stack pro nasazení Active Directory Federation Services (AD FS) (AD FS).
author: ihenkel
ms.topic: article
ms.date: 06/03/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 7d11ae849a5ecc51c1506e8d978510884b752a9a
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890370"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-active-directory-federation-services-ad-fs"></a>Přidání nového uživatelského účtu centra Azure Stack v Active Directory Federation Services (AD FS) (AD FS)

Modul snap-in **Uživatelé a počítače služby Active Directory** můžete použít k přidání dalších uživatelů do prostředí Azure Stackového centra pomocí AD FS jako zprostředkovatele identity.

## <a name="add-windows-server-active-directory-users"></a>Přidat uživatele služby Active Directory systému Windows Server

1. Přihlaste se k počítači pomocí účtu, který poskytuje přístup k nástrojům pro správu systému Windows a otevřete novou konzolu MMC (Microsoft Management Console).
2. Vyberte **soubor > přidat nebo odebrat modul snap-in**.

   > [!TIP]
   > Položku *adresář-Domain* nahraďte doménou, která odpovídá vašemu adresáři. 

3. Vyberte **Uživatelé a počítače služby Active Directory** > *Directory-Domain* > **Users**.
4. Vyberte **akci** > **Nový** > **uživatel**.
5. V novém objektu – uživatel zadejte podrobnosti o uživateli. Vyberte **Next** (Další).
6. Zadejte a potvrďte heslo.
7. Kliknutím na tlačítko **Další** dokončete hodnoty. Kliknutím na tlačítko **Dokončit** vytvořte uživatele.


## <a name="next-steps"></a>Další kroky

[Vytvoření identity aplikace pro přístup k prostředkům centra Azure Stack](azure-stack-create-service-principals.md)