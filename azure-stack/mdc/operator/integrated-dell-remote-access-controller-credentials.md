---
title: Integrované přihlašovací údaje k řadiči vzdáleného přístupu Dell – MDC
description: Naučte se aktualizovat přihlašovací údaje pro integrovaný kontroler vzdáleného přístupu (iDRAC) od společnosti Dell (MDC) v modulárním datovém centru ().
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/27/2020
ms.reviewer: justinha
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: d0d2f522038d0788d4942db68b36e5bb21884619
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910733"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller---modular-data-center-mdc"></a>Aktualizace přihlašovacích údajů pro integrovaný kontroler vzdáleného přístupu Dell – modulární datové centrum (MDC)

V této části je popsán postup změny integrovaných přihlašovacích údajů k řadiči vzdáleného přístupu (iDRAC) od společnosti Dell. 

## <a name="prerequisites"></a>Požadavky

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