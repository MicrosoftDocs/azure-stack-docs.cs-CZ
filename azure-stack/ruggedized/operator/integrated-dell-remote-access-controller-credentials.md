---
title: Integrované přihlašovací údaje k řadiči vzdáleného přístupu Dell
description: Vysvětluje, jak aktualizovat přihlašovací údaje pro integrovaný kontroler vzdáleného přístupu Dell.
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 04/28/2020
ms.reviewer: justinha
ms.lastreviewed: 04/28/2020
ms.openlocfilehash: 2c6e7f0aa0e6e03af061083259234e49275a83de
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939620"
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
1. V okně **Upravit uživatele** zadejte nové heslo do pole **heslo** a **potvrďte heslo**, jak je znázorněno na následujícím obrázku: 

   ![Snímek obrazovky zobrazující informace o uživateli](../operator/media/idrac-credentials/enter-user.png)

1. Klikněte na **Uložit** a pak klikněte na **OK**. 

## <a name="next-steps"></a>Další kroky

[Obměna tajných klíčů ve službě Azure Stack Hub](../../operator/azure-stack-rotate-secrets.md)