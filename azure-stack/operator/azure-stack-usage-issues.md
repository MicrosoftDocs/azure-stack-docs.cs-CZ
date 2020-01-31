---
title: Problémy s připojením a chyby v centru Azure Stack
description: Řešení potíží s chybami a problémy s využitím centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 4a4b34c6aeaac3bd484c0a954af0f3a456cbfe85
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882686"
---
# <a name="usage-connectivity-errors"></a>Chyby připojení využití

Data o využití centra Azure Stack se do Azure odesílají pomocí komponenty [*Azure Bridge*](azure-stack-usage-reporting.md) v centru Azure Stack. Pokud se most ve službě Azure Stack hub nemůže připojit ke službě Azure Usage, zobrazí se následující chyba:

![Chyba mostu použití](media/azure-stack-usage-issues/usageerror2.png)

Okno může poskytovat další informace o chybě a řešení:

![řešení chyb](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Řešení potíží s připojením

Pokud chcete tento problém zmírnit, vyzkoušejte následující kroky:

- Ověřte, že konfigurace sítě umožňuje Azure Bridge připojit se ke vzdálené službě.

- Pokud chcete najít ID předplatného Azure, které se používá pro registraci, skupinu prostředků a název prostředku registrace, otevřete okno [ **vlastnosti** > **správy oblastí** ](azure-stack-registration.md#verify-azure-stack-hub-registration) . Ověřte, že prostředek registrace existuje pod správným ID předplatného Azure v Azure Portal. Provedete to tak, že přejdete na **všechny prostředky** vytvořené v rámci ID předplatného Azure a zaškrtnete políčko **Zobrazit skryté typy** . Pokud nemůžete najít registrační prostředek, postupujte podle kroků v části [obnovení nebo změna registrace](azure-stack-registration.md#renew-or-change-registration) pro opětovné zaregistrování centra Azure Stack.

  ![Portál](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Kódy chyb

Tato část popisuje kódy chyb využití.

| Kód chyby                 | Problém                                                                                                                                             | Náprava                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Most centra Azure Stack nemůže odeslat žádost o použití koncového bodu služby v Azure.                                                            | Zkontroluje, jestli proxy server blokuje nebo zachytává přístup k koncovému bodu služby Usage.                                                                                                                                                                                                             |
| RequestTimedOut            | Požadavek byl odeslán z Azure Bridge, ale služba využití v Azure nedokázala odpovědět v časovém limitu.                             | Zkontroluje, jestli proxy server blokuje nebo zachytává přístup k koncovému bodu služby Usage.                                                                                                                                                                                                                        |
| LoginError                 | Nelze provést ověření pomocí Microsoft Azure Active Directory.                                                                                                             | Zajistěte, aby byl koncový bod přihlášení ke službě Azure AD přístupný ze všech virtuálních počítačů s XRP v centru Azure Stack.                                                                                                                                                                                                                     |
| CertificateValidationError | Azure Bridge nemůže odeslat žádost, protože se nemůže ověřit ve službě Azure.                                    | Ověřte, jestli existuje proxy, který zachycuje přenos HTTPS mezi Azure Stackm XRP hub a koncovým bodem brány služby Usage.                                                                                                                                                                                      |
| Neautorizováno               | Azure Bridge nemůže odeslat data do služby využití v Azure, protože služba Azure nemůže ověřit most centra Azure Stack. | Ověřte, jestli se prostředek registrace změnil, a pokud ano, znovu zaregistrujte Azure Stack centrum. <br><br> V některých případech může tato chyba způsobit problémy s synchronizací mezi Azure Stack hub a Azure AD. V takovém případě je nutné zajistit, aby se časy v XRPch virtuálních počítačích v centru Azure Stack synchronizovaná se službou Azure AD. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

Kromě toho může být nutné zadat soubory protokolu pro součásti Azure Bridge, WAS a WASPublic pomocí následujících [kroků](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vytváření sestav Azure Stack dat o využití centra do Azure](azure-stack-usage-reporting.md).
- Pokud chcete zkontrolovat chybové zprávy, pokud se spouštějí v procesu registrace, přečtěte si téma [chybové zprávy registrace klienta](azure-stack-registration-errors.md).
- Přečtěte si další informace o [infrastruktuře vytváření sestav využití pro poskytovatele Cloud Solution Provider](azure-stack-csp-ref-infrastructure.md).
