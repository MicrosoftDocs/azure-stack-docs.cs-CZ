---
title: Přepnout přihlašovací údaje
description: Vysvětluje, jak aktualizovat přihlašovací údaje přepínače pro robustní centrum Azure Stack.
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/14/2020
ms.reviewer: justinha
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: a761544c8d514fd69364d917890d284ab06e9962
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939595"
---
# <a name="switch-credentials"></a>Přepnout přihlašovací údaje

V tomto tématu se dozvíte, jak změnit přihlašovací údaje správce (admin) a protokolu SNMP (Simple Network Management Protocol) u všech přepínačů. 

## <a name="prerequisites"></a>Předpoklady

Než spustíte tyto postupy:

- Připojte se k HLH pomocí vzdálené plochy.
- Vyhledejte na HLH, obvykle E:\Tools\Putty\putty.exe. Pokud není k dispozici, Stáhněte si ho a zkopírujte do HLH.
- Ujistěte se, že pro přepínače máte aktuální i nové přihlašovací údaje.

## <a name="update-credentials-for-the-admin-and-enable-accounts"></a>Aktualizovat přihlašovací údaje pro správce a povolit účty 

Pro každý přepínač v jednotce škálování (BMC, TOR1 a TOR2):

1. V HLH pomocí výstupu se přihlaste k přepínači pomocí aktuálních přihlašovacích údajů. 
1. Spusťte následující příkaz a nahraďte \<new password\> ho novým správcem a povolte přihlašovací údaje. 
   ```ini
   enable
   configuration terminal
   username admin pass
   word <new_password> privilege 15
   enable password <new_password>
   ```
1. Ponechte aktuální relaci na přepínač otevřený.
1. V HLH pomocí výstupu se přihlaste k přepínači pomocí nových přihlašovacích údajů.
1. Spusťte následující příkaz. Při povolení by se neměl zobrazovat výzva k zadání hesla.

   ```ini
   enable
   write
   dir flash:
   ```

1. Ověřte, jestli je spouštěcí – konfigurační konfigurace dnešní datum.
1. Po potvrzení zavřete tuto relaci i původní relaci.

## <a name="update-snmp-accounts"></a>Aktualizace účtů SNMP

Pro každý přepínač v jednotce škálování (BMC, TOR1 a TOR2):

1. V HLH pomocí výstupu se přihlaste k přepínači.
1. Spusťte následující příkaz, který získá aktuální uživatele a skupiny pro čtení/zápis SNMP:

   ```ini
   enable
   show run snmp | grep user
   ```

   Následující příklad ukazuje typ vráceného obsahu, včetně uživatele, názvu skupiny a hodnot hash hesla:

   ```ini
   snmp-server user <user> <group> 3 encrypted auth sha <password_hash>
   ```

1. Spusťte následující příkaz, nahraďte \<user\> a \<group\> s podrobnostmi, které jste určili v předchozím kroku, a nahraďte \<password\> nové heslo:

   ```ini
   configuration terminal
   snmp-server user <user> <group> 3 auth sha <password_new>
   end
   write
   exit
   ```

## <a name="next-steps"></a>Další kroky

[Obměna tajných klíčů ve službě Azure Stack Hub](../operator/azure-stack-rotate-secrets.md)