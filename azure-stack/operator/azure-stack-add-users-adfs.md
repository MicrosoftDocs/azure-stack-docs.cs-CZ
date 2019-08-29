---
title: Přidat uživatele Azure Stack v AD FS | Microsoft Docs
description: Naučte se přidávat Azure Stack uživatelů pro nasazení Active Directory Federation Services (AD FS) (AD FS).
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
ms.openlocfilehash: 4411290b075e105a827de8fb2c8295dfd84e3b50
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118633"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Přidat uživatele Azure Stack v AD FS
Modul snap-in **Uživatelé a počítače služby Active Directory** můžete použít k přidání dalších uživatelů do Azure Stackho prostředí pomocí Active Directory Federation Services (AD FS) (AD FS) jako zprostředkovatele identity.

## <a name="add-windows-server-active-directory-users"></a>Přidat uživatele služby Active Directory systému Windows Server
> [!TIP]
> V tomto příkladu se používá výchozí azurestack. Local ASDK Active Directory. 

1. Přihlaste se k počítači pomocí účtu, který poskytuje přístup k nástrojům pro správu systému Windows a otevřete novou konzolu MMC (Microsoft Management Console).
2. Vyberte **soubor > přidat nebo odebrat modul snap-in**.
3.  > Vyberte **Uživatelé a počítače služby Active Directory** **AzureStack. místní** > **Uživatelé**.
4. Vyberte **Akce** > novýUživatel > .
5. V novém objektu – uživatel zadejte podrobnosti o uživateli. Vyberte **Další**.
6. Zadejte a potvrďte heslo.
7. Kliknutím na tlačítko **Další** dokončete hodnoty. Kliknutím na tlačítko **Dokončit** vytvořte uživatele.


## <a name="next-steps"></a>Další kroky
[Vytvoření instančních objektů](azure-stack-create-service-principals.md)