---
title: Problémy s připojením a chyby v Azure Stack | Microsoft Docs
description: Řešení potíží s Azure Stack problémy a chybami při použití.
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
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 81ba613ec6a816d1ae6161d078452eea63ce1164
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974021"
---
# <a name="usage-connectivity-errors"></a>Chyby připojení využití

Data o využití Azure Stack se do Azure odesílají pomocí [komponenty *Azure Bridge* ](azure-stack-usage-reporting.md) v Azure Stack. Pokud se most ve Azure Stack nemůže připojit ke službě Azure Usage, zobrazí se následující chyba:

![Chyba mostu použití](media/azure-stack-usage-issues/usageerror2.png)

Okno může poskytovat další informace o chybě a řešení:

![řešení chyb](media/azure-stack-usage-issues/usageerror3.png)

## <a name="resolve-connectivity-issues"></a>Řešení potíží s připojením

Pokud chcete tento problém zmírnit, vyzkoušejte následující kroky:

- Ověřte, že konfigurace sítě umožňuje Azure Bridge připojit se ke vzdálené službě.

- V okně pro [ **správu oblastí**@no__t – 2 ](azure-stack-registration.md#verify-azure-stack-registration) Najděte ID předplatného Azure, které se používá pro registraci, skupinu prostředků a název prostředku registrace. Ověřte, že prostředek registrace existuje pod správným ID předplatného Azure v Azure Portal. Provedete to tak, že přejdete na **všechny prostředky** vytvořené v rámci ID předplatného Azure a zaškrtnete políčko **Zobrazit skryté typy** . Pokud nemůžete najít registrační prostředek, postupujte podle kroků v části [obnovení nebo změna registrace](azure-stack-registration.md#renew-or-change-registration) a znovu zaregistrujte své Azure Stack.

  ![Portál](media/azure-stack-usage-issues/stackres.png)

## <a name="error-codes"></a>Kódy chyb

Tato část popisuje kódy chyb využití.

| Kód chyby                 | Problém                                                                                                                                             | Náprava                                                                                                                                                                                                                                                                                        |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| NetworkError               | Azure Stack most nemůže odeslat požadavek na použití koncového bodu služby v Azure.                                                            | Zkontroluje, jestli proxy server blokuje nebo zachytává přístup k koncovému bodu služby Usage.                                                                                                                                                                                                             |
| RequestTimedOut            | Požadavek byl odeslán z Azure Bridge, ale služba využití v Azure nedokázala odpovědět v časovém limitu.                             | Zkontroluje, jestli proxy server blokuje nebo zachytává přístup k koncovému bodu služby Usage.                                                                                                                                                                                                                        |
| LoginError                 | Nelze provést ověření pomocí Microsoft Azure Active Directory.                                                                                                             | Zajistěte, aby byl koncový bod přihlášení ke službě Azure AD přístupný ze všech virtuálních počítačů s XRP v Azure Stack.                                                                                                                                                                                                                     |
| CertificateValidationError | Azure Bridge nemůže odeslat žádost, protože se nemůže ověřit ve službě Azure.                                    | Ověřte, jestli existuje proxy, který zachycuje přenos HTTPS mezi Azure Stackm počítačem XRP a koncovým bodem brány služby Usage.                                                                                                                                                                                      |
| Neautorizováno               | Most Azure nemůže odeslat data do služby využití v Azure, protože služba Azure nemůže ověřit most Azure Stack. | Ověřte, zda byl registrační prostředek změněn a v případě potřeby jej znovu zaregistrujte Azure Stack. <br><br> V některých případech může tato chyba způsobit problémy s synchronizací mezi Azure Stack a službou Azure AD. V takovém případě je nutné zajistit, aby se časy v Azure Stackch virtuálních počítačích XRP synchronizovaná se službou Azure AD. |
|                            |                                                                                                                                                   |                                                                                                                                                                                                                                                                                                    |

Kromě toho může být nutné zadat soubory protokolu pro součásti Azure Bridge, WAS a WASPublic pomocí následujících [kroků](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vytváření sestav Azure Stack dat o využití do Azure](azure-stack-usage-reporting.md).
- Pokud chcete zkontrolovat chybové zprávy, pokud se spouštějí v procesu registrace, přečtěte si téma [chybové zprávy registrace klienta](azure-stack-registration-errors.md).
- Přečtěte si další informace o [infrastruktuře vytváření sestav využití pro poskytovatele cloudových služeb](azure-stack-csp-ref-infrastructure.md).
