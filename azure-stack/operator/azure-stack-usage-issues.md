---
title: Problémy s připojením k využití a chyb ve službě Azure Stack | Dokumentace Microsoftu
description: Řešení potíží s Azure Stack využití problémy a chyby.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 57c546ec3583c9e04594e4da542a3c2ce3f72c62
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419755"
---
# <a name="usage-connectivity-errors"></a>Chyby připojení k použití

Data o využití služby Azure Stack je odeslána do Azure s [ *Azure most* komponenty](azure-stack-usage-reporting.md) ve službě Azure Stack. Pokud přemostění v rámci služby Azure Stack se nemůže připojit ke službě využití Azure, zobrazí se následující chyba:

![Chyba most použití](media/azure-stack-usage-issues/usageerror2.png)

V okně může poskytovat další informace o chybě a řešení:

![řešení chyb](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Vyřešte problémy s připojením

Chcete-li tyto problémy zmírnit, zkuste následující kroky:

- Ověřte, že tato konfigurace sítě umožňuje most Azure pro připojení k vzdálené služby.

- Přejděte [ **Správa oblastí** > **vlastnosti** ](azure-stack-registration.md#verify-azure-stack-registration) okno Najít ID předplatného Azure používá k registraci, skupinu prostředků a název registrace zdrojů. Ověřte, zda existuje registrace prostředků podle ID správné předplatného Azure na webu Azure portal. Uděláte to tak, přejděte na **všechny prostředky** vytvořené v rámci ID předplatného Azure a kontrolu **zobrazit skryté typy** pole. Pokud nemůžete najít prostředek registrace, postupujte podle kroků v [obnovit nebo změna registrace](azure-stack-registration.md#renew-or-change-registration) přeregistrovat služby Azure Stack.

  ![Portál](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Kódy chyb

Tato část popisuje využití kódy chyb.

| Kód chyby                 | Problém                                                                                                                                             | Náprava                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Azure Stack přemostění není schopen odeslat požadavek na koncový bod služby usage v Azure.                                                            | Zkontrolujte, jestli je proxy server blokuje nebo zachycování přístup ke koncovému bodu služby usage.                                                                                                                                                                                                             |
| RequestTimedOut            | Požadavek byl odeslán z most Azure, ale služba usage v Azure nepodařilo odpovědět v časovém limitu.                             | Zkontrolujte, jestli je proxy server blokuje nebo zachycování přístup ke koncovému bodu služby usage.                                                                                                                                                                                                                        |
| LoginError                 | Nejde ověřit s Microsoft Azure Active Directory.                                                                                                             | Ujistěte se, že koncový bod přihlašovacího účtu služby Azure AD je přístupná ze všech virtuálních počítačů XRP ve službě Azure Stack.                                                                                                                                                                                                                     |
| CertificateValidationError | Azure přemostění není schopen odeslat požadavek, protože to není možné ověřit pomocí služby Azure.                                    | Zkontrolujte, jestli je proxy server zachycuje přenosy mezi počítači Azure Stack XRP a koncovému bodu usage pro bránu HTTP.                                                                                                                                                                                      |
| Neautorizováno               | Azure most nedokáže k zápisu dat do služby využití ve službě Azure, protože služba Azure není možné ověřit most Azure Stack. | Zkontrolujte, jestli registrace prostředek byl změněn a pokud ano, znovu registrace Azure Stack. <br><br> V některých případech čas problémům synchronizace mezi službami Azure Stack a Azure AD může způsobit selhání. V takovém případě Ujistěte se, že časy na virtuálních počítačích XRP ve službě Azure Stack jsou synchronizované s Azure AD. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

Kromě toho můžete může vyžadovat, aby poskytli soubory protokolu pro komponenty Azure most WAS a WASPublic pomocí následujících [tyto kroky](azure-stack-diagnostics.md#log-collection-tool).

## <a name="next-steps"></a>Další postup

- Další informace o [generování sestav dat využití služby Azure Stack na Azure](azure-stack-usage-reporting.md).
- Zkontrolujte chybové zprávy, pokud se spouštějí v procesu registrace, najdete v článku [chybových zpráv registrace klienta](azure-stack-registration-errors.md).
- Další informace o [infrastrukturu pro generování sestav využití pro poskytovatele cloudových služeb](azure-stack-csp-ref-infrastructure.md).
