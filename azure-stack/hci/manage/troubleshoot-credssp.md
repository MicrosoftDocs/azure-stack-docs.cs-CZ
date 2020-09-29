---
title: Řešení CredSSP
description: Naučte se řešit potíže s zprostředkovatelem CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 08/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 24e9483aa9658809adc9be7fbfa4a2cb13daab84
ms.sourcegitcommit: 952d26ad08fcc28ad3ad83e27644e61497623a44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87899905"
---
# <a name="troubleshoot-credssp"></a>Řešení CredSSP

> Platí pro Azure Stack HCI, verze v20H2

Některé Azure Stack operace HCI používají Vzdálená správa systému Windows (WinRM), což ve výchozím nastavení nepovoluje delegování přihlašovacích údajů. Aby bylo možné delegování povolit, musí mít počítač dočasně povolený zprostředkovatel zabezpečení (CredSSP) (Credential Security Support Provider). CredSSP je poskytovatel podpory zabezpečení, který umožňuje klientovi delegovat přihlašovací údaje na cílový server pro vzdálené ověřování. 

Povolení CredSSP je degradované zabezpečení stav a ve většině případů by mělo být zakázáno po dokončení úkolu nebo operace.

Mezi úlohy, které vyžadují, aby bylo možné povolit CredSSP, patří:

- Pracovní postup Průvodce vytvořením clusteru
- Dotazy nebo aktualizace služby Active Directory
- Dotazy a aktualizace systému SQL Server
- Vyhledání účtů nebo počítačů v jiné doméně nebo prostředí, které není připojené k doméně

## <a name="troubleshooting-tips"></a>Rady pro řešení potíží

Pokud máte problémy se zprostředkovatelem CredSSP, může vám pomáhat následující tipy k odstraňování potíží:

- Při spuštění Průvodce vytvořením clusteru může zprostředkovatel CredSSP ohlásit problém, pokud se nevytvoří vztah důvěryhodnosti služby Active Directory nebo dojde k jeho přerušení. Výsledkem je, že se pro vytvoření clusteru použijí servery založené na pracovní skupině. V takovém případě zkuste ručně restartovat každý server v clusteru.

- Při spuštění centra pro správu systému Windows na serveru (v režimu služby) se ujistěte, že uživatelský účet je členem skupiny Správci brány.

- Aby bylo možné povolit nebo zakázat zprostředkovatele CredSSP na serveru, ujistěte se, že jste členem skupiny správců brány na daném počítači. Další informace najdete v prvním dvou částech [Konfigurace uživatelských Access Control a oprávnění](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="next-steps"></a>Další kroky

Další informace o CredSSP najdete v tématu [Zprostředkovatel podpory zabezpečení přihlašovacích údajů](/windows/win32/secauthn/credential-security-support-provider).