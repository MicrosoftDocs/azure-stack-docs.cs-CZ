---
title: Přidání uživatelů centra Azure Stack v AD FS | Microsoft Docs
description: Naučte se přidávat uživatele centra Azure Stack pro nasazení Active Directory Federation Services (AD FS) (AD FS).
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 93e2e60e235ae26016f52a94916f429c38286511
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804873"
---
# <a name="add-azure-stack-hub-users-in-ad-fs"></a>Přidání uživatelů centra Azure Stack v AD FS
Pomocí modulu snap-in **Uživatelé a počítače služby Active Directory** můžete přidat další uživatele do prostředí Azure Stackového centra pomocí Active Directory Federation Services (AD FS) (AD FS) jako zprostředkovatele identity.

## <a name="add-windows-server-active-directory-users"></a>Přidat uživatele služby Active Directory systému Windows Server
> [!TIP]
> V tomto příkladu se používá výchozí azurestack. Local ASDK Active Directory. 

1. Přihlaste se k počítači pomocí účtu, který poskytuje přístup k nástrojům pro správu systému Windows a otevřete novou konzolu MMC (Microsoft Management Console).
2. Vyberte **soubor > přidat nebo odebrat modul snap-in**.
3. Vyberte **Uživatelé a počítače služby Active Directory** > **AzureStack. místní** > **Uživatelé**.
4. Vyberte **akci** > **Nový** > **uživatel**.
5. V novém objektu – uživatel zadejte podrobnosti o uživateli. Vyberte **Next** (Další).
6. Zadejte a potvrďte heslo.
7. Kliknutím na tlačítko **Další** dokončete hodnoty. Kliknutím na tlačítko **Dokončit** vytvořte uživatele.


## <a name="next-steps"></a>Další kroky
[Vytvoření instančních objektů](azure-stack-create-service-principals.md)