---
title: Integrované přihlašovací údaje k řadiči vzdáleného přístupu Dell
description: Vysvětluje, jak aktualizovat přihlašovací údaje pro integrovaný kontroler vzdáleného přístupu Dell.
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/27/2020
ms.reviewer: justinha
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: 1525872e3c59ab7e5b0cd436ba5ab8e2e3836594
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329095"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller"></a>Aktualizovat přihlašovací údaje pro integrovaný kontroler vzdáleného přístupu Dell

V této části je popsán postup změny integrovaných přihlašovacích údajů k řadiči vzdáleného přístupu (iDRAC) od společnosti Dell. 

## <a name="prerequisites"></a>Předpoklady

Před spuštěním postupu: 

- Pomocí vzdálené plochy se připojte k virtuálnímu počítači pro správu. 
- Ujistěte se, že máte nové přihlašovací údaje pro účet nebo účty. 
 
## <a name="update-the-idrac-credentials"></a>Aktualizace přihlašovacích údajů iDRAC

Pokud chcete aktualizovat přihlašovací údaje iDRAC pro všechny servery PowerEdge (uzly jednotek HLH a Scale jednotky) v prostředí:

1. Ve webovém prohlížeči se přihlaste k https://<iDRAC_IP>. 
1. Přejít na **iDRAC nastavení**  >  **Uživatelé**. 
1. Vyberte uživatele, kterého chcete upravit, a pak klikněte na **Upravit**. 
1. V okně **Upravit uživatele** zadejte nové heslo do pole **heslo** a **potvrďte heslo** , jak je znázorněno na následujícím obrázku: 

   ![Snímek obrazovky zobrazující informace o uživateli](../operator/media/idrac-credentials/enter-user.png)

1. Klikněte na **Uložit** a pak klikněte na **OK**. 

## <a name="next-steps"></a>Další kroky

[Otočení tajných kódů v centru Azure Stack](../../operator/azure-stack-rotate-secrets.md)