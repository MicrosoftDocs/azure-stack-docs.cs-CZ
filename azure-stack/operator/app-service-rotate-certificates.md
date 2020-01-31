---
title: Otočení App Service v tajných klíčích a certifikátech centra Azure Stack
description: Naučte se otáčet tajné klíče a certifikáty používané Azure App Service v centru Azure Stack
author: BryanLa
ms.topic: article
ms.date: 01/10/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: f1a42f5b04ea83d9ff9130fb63ba6833cd7d2914
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876537"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>Otočení App Service v tajných klíčích a certifikátech centra Azure Stack

Tyto pokyny platí pouze pro Azure App Service v centru Azure Stack.  V centralizovaném postupu pro rotaci tajných klíčů pro Azure Stack centrum není zahrnutá rotace Azure App Service v tajných klíčích centra Azure Stack.  Operátoři můžou monitorovat platnost tajných kódů v rámci systému, datum, kdy se data naposledy aktualizovala, a zbývající čas do vypršení platnosti tajných klíčů.

> [!Important]
> Operátoři nebudou dostávat upozornění na vypršení platnosti tajného kódu na řídicím panelu centra Azure Stack, protože Azure App Service na Azure Stack hub není integrovaný do služby upozorňování centra Azure Stack.  Operátoři musí pravidelně monitorovat své tajné klíče pomocí Azure App Service v Azure Stack prostředí pro správu centra na portálu Azure Stack hub Administrators.

Tento dokument obsahuje postup pro otočení následujících tajných kódů:

* Šifrovací klíče používané v rámci Azure App Service v centru Azure Stack;
* Přihlašovací údaje pro připojení k databázi, které používá Azure App Service v centru Azure Stack k interakci s databázemi hostování a měření;
* Certifikáty, které používá Azure App Service v centru Azure Stack k zabezpečení koncových bodů;
* Přihlašovací údaje systému pro Azure App Service v rolích infrastruktury centra Azure Stack

## <a name="rotate-encryption-keys"></a>Otočení šifrovacích klíčů

Pokud chcete otočit šifrovací klíče používané v Azure App Service v centru Azure Stack, proveďte následující kroky:

1. Na portálu Azure Stack centra pro správu přejdete na prostředí pro správu App Service.

1. Přejděte do možnosti nabídky **tajných klíčů** .

1. V části šifrovací klíče klikněte na tlačítko **otočit** .

1. Kliknutím na tlačítko **OK** spusťte postup otáčení.

1. Šifrovací klíče se otočí a všechny instance rolí se aktualizují. Operátory mohou sledovat stav procedury pomocí tlačítka **stav** .

## <a name="rotate-connection-strings"></a>Otočení připojovacích řetězců

Chcete-li aktualizovat přihlašovací údaje pro připojovací řetězec databáze pro App Service hostování a měření databází, proveďte následující kroky:

1. Na portálu Azure Stack centra pro správu přejdete na prostředí pro správu App Service.

1. Přejděte do možnosti nabídky **tajných klíčů** .

1. Klikněte na tlačítko **otočit** v oddílu připojovací řetězce.

1. Zadejte uživatelské jméno a **heslo** **SA SQL** a kliknutím na **OK** spusťte postup otočení. 

1. Přihlašovací údaje se otočí v rámci Azure App Service instancí rolí. Operátory mohou sledovat stav procedury pomocí tlačítka **stav** .

## <a name="rotate-certificates"></a>Rotace certifikátů

K otočení certifikátů používaných v Azure App Service v centru Azure Stack proveďte následující kroky:

1. Na portálu Azure Stack centra pro správu přejdete na prostředí pro správu App Service.

1. Přejděte do možnosti nabídky **tajných klíčů** .

1. Klikněte na tlačítko **otočit** v části certifikáty.

1. Zadejte **soubor certifikátu** a příslušné **heslo** pro certifikáty, které chcete otočit, a klikněte na tlačítko **OK**.

1. Certifikáty se v rámci Azure App Service v Azure Stack instancích rolí centra otočí podle potřeby.  Operátory mohou sledovat stav procedury pomocí tlačítka **stav** .

## <a name="rotate-system-credentials"></a>Otočit systémová pověření

K otočení systémových přihlašovacích údajů používaných v Azure App Service v centru Azure Stack proveďte následující kroky:

1. Na portálu Azure Stack centra pro správu přejdete na prostředí pro správu App Service.

1. Přejděte do možnosti nabídky **tajných klíčů** .

1. V části přihlašovací údaje systému klikněte na tlačítko **otočit** .

1. Vyberte **Rozsah** přihlašovacích údajů systému, které chcete otočit.  Operátory se můžou rozhodnout pro otočení systémových přihlašovacích údajů pro všechny role nebo jednotlivé role.

1. Zadejte **uživatelské jméno místního správce**, nové **heslo** a potvrďte **heslo** a klikněte na **OK** .

1. Přihlašovací údaje se budou otáčet podle požadavků v rámci odpovídajících Azure App Service v instanci role centra Azure Stack.  Operátory mohou sledovat stav procedury pomocí tlačítka **stav** .



