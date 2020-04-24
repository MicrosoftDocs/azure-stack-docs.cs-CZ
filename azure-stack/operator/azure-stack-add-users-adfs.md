---
title: Přidání uživatelů centra Azure Stack v AD FS
description: Naučte se přidávat uživatele centra Azure Stack pro nasazení Active Directory Federation Services (AD FS) (AD FS).
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/03/2019
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 79380d40282757e704f9dc0ef3bdaf977253ba14
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703345"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-active-directory-federation-services-ad-fs"></a>Přidání nového uživatelského účtu centra Azure Stack v Active Directory Federation Services (AD FS) (AD FS)

Modul snap-in **Uživatelé a počítače služby Active Directory** můžete použít k přidání dalších uživatelů do prostředí Azure Stackového centra pomocí AD FS jako zprostředkovatele identity.

## <a name="add-windows-server-active-directory-users"></a>Přidat uživatele služby Active Directory systému Windows Server

1. Přihlaste se k počítači pomocí účtu, který poskytuje přístup k nástrojům pro správu systému Windows a otevřete novou konzolu MMC (Microsoft Management Console).
2. Vyberte **soubor > přidat nebo odebrat modul snap-in**.

   > [!TIP]
   > Položku *adresář-Domain* nahraďte doménou, která odpovídá vašemu adresáři. 

3.  > Vyberte adresář **Uživatelé a počítače služby Active Directory***– Domain* > **Users**.
4. Vyberte **Akce** > **Nový** > **uživatel**.
5. V novém objektu – uživatel zadejte podrobnosti o uživateli. Vyberte **Další**.
6. Zadejte a potvrďte heslo.
7. Kliknutím na tlačítko **Další** dokončete hodnoty. Kliknutím na tlačítko **Dokončit** vytvořte uživatele.


## <a name="next-steps"></a>Další kroky

[Vytvoření identity aplikace pro přístup k prostředkům centra Azure Stack](azure-stack-create-service-principals.md)