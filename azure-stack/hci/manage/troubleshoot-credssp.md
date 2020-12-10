---
title: Řešení CredSSP
description: Naučte se řešit potíže s zprostředkovatelem CredSSP
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: f90e03c275c4ca7a28a9d8392351bf55d0adb2c0
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97010833"
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

- Chcete-li použít Průvodce vytvořením clusteru při spuštění centra pro správu systému Windows na serveru místo počítače, musíte být členem skupiny Správci brány na serveru centra pro správu systému Windows. Další informace najdete v tématu [Možnosti přístupu uživatele v centru pro správu systému Windows](/windows-server/manage/windows-admin-center/plan/user-access-options).

- Při spuštění Průvodce vytvořením clusteru může zprostředkovatel CredSSP ohlásit problém, pokud se nevytvoří vztah důvěryhodnosti služby Active Directory nebo dojde k jeho přerušení. Výsledkem je, že se pro vytvoření clusteru použijí servery založené na pracovní skupině. V takovém případě zkuste ručně restartovat každý server v clusteru.

- Při spuštění centra pro správu systému Windows na serveru (v režimu služby) se ujistěte, že uživatelský účet je členem skupiny Správci brány.

- Doporučujeme spustit centrum pro správu systému Windows v počítači, který je členem stejné domény jako spravované servery.

- Aby bylo možné povolit nebo zakázat zprostředkovatele CredSSP na serveru, ujistěte se, že jste členem skupiny správců brány na daném počítači. Další informace najdete v prvním dvou částech [Konfigurace uživatelských Access Control a oprávnění](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

- Restartování služby Vzdálená správa systému Windows (WinRM) na serverech v clusteru vás může vyzvat k opětovnému vytvoření připojení WinRM mezi jednotlivými servery clusteru a centrem pro správu systému Windows.

    Můžete to udělat tak, že na každém serveru clusteru kliknete a v centru pro správu systému Windows v nabídce **nástroje** vyberte **služby**, vyberte **WinRM**, vyberte **restartovat** a pak na příkazovém řádku pro **restart služby** vyberte **Ano**.

## <a name="next-steps"></a>Další kroky

Další informace o CredSSP najdete v tématu [Zprostředkovatel podpory zabezpečení přihlašovacích údajů](/windows/win32/secauthn/credential-security-support-provider).