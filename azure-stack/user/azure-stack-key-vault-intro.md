---
title: Úvod do Key Vault v Azure Stack | Microsoft Docs
description: Přečtěte si, jak Key Vault spravuje klíče a tajné kódy v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: fc68f80688f6b8cbe0376d332d706c9dc7b6dd92
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842888"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Úvod do Key Vault v Azure Stack

## <a name="prerequisites"></a>Požadavky

* Přihlaste se k odběru nabídky, která zahrnuje službu Azure Key Vault.  
* [PowerShell je nakonfigurovaný pro použití s Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Základy Key Vault

Key Vault v Azure Stack pomáhají chránit kryptografické klíče a tajné kódy, které využívají cloudové aplikace a služby. Pomocí Key Vault můžete šifrovat klíče a tajné klíče, například:

* Ověřovací klíče
* Klíče účtu úložiště
* Šifrovací klíče dat
* soubory. pfx
* Hesla

Key Vault zjednodušuje proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Vývojáři můžou během pár minut vytvořit klíče pro vývoj a testování a potom je bez problémů migrovat na produkční klíče. Správci zabezpečení můžou k klíčům udělit oprávnění (a odvolat jim) podle potřeby.

Kdokoli s předplatným Azure Stack může vytvářet a používat trezory klíčů. I když Key Vault výhody vývojářů a správců zabezpečení, operátor, který spravuje jiné služby Azure Stack v organizaci, může implementovat a spravovat. Například operátor Azure Stack se může přihlásit pomocí předplatného Azure Stack a vytvořit trezor pro organizaci, do které se mají ukládat klíče. Až to bude hotové, můžou:

* Vytvořte nebo importujte klíč nebo tajný klíč.
* Odvolejte nebo odstraňte klíč nebo tajný klíč.
* Udělte uživatelům nebo aplikacím přístup k trezoru klíčů, aby mohli spravovat nebo používat jeho klíče a tajné kódy.
* Nakonfigurujte použití klíče (například podpis nebo šifrování).

Operátor pak může poskytnout vývojářům pomocí identifikátorů URI (Uniform Resource Identifier) pro volání ze svých aplikací. Operátoři můžou taky poskytovat správcům zabezpečení informace o protokolování použití klíče.

Vývojáři můžou klíče spravovat i přímo pomocí rozhraní API. Další informace najdete v příručce pro [vývojáře Key Vault](/azure/key-vault/key-vault-developers-guide).

## <a name="scenarios"></a>Scénáře

Následující scénáře popisují, jak Key Vault mohou pokrýt potřeby vývojářů a správců zabezpečení.

### <a name="developer-for-an-azure-stack-app"></a>Vývojář pro aplikaci Azure Stack

**Řešení** Chci napsat aplikaci pro Azure Stack, která používá klíče pro podepisování a šifrování. Chci, aby tyto klávesy byly externí z mé aplikace, aby bylo řešení vhodné pro geograficky distribuovanou aplikaci.

**Vydá** Klíče jsou uloženy v trezoru a v případě potřeby vyvolány identifikátorem URI.

### <a name="developer-for-software-as-a-service-saas"></a>Vývojář softwaru jako služby (SaaS)

**Řešení** Nechci odpovědnost nebo potenciální odpovědnost za klíče a tajné kódy daného zákazníka. Chci, aby si zákazníci mohli vlastnit a spravovat svoje klíče, abyste se mohli soustředit na to, co je nejlepší, což poskytuje základní softwarové funkce.

**Vydá** Zákazníci mohou importovat a spravovat vlastní klíče v Azure Stack.

### <a name="chief-security-officer-cso"></a>Vedoucí oddělení zabezpečení (CSO)

**Řešení** Chci se ujistit, že moje organizace má kontrolu nad životním cyklem klíčů a může monitorovat jejich využití.

**Vydá** Key Vault je navržený tak, aby Microsoft nerozpoznal ani neextrahuje vaše klíče. Když aplikace potřebuje provést kryptografické operace pomocí zákaznických klíčů, Key Vault použije klíče jménem aplikace. Aplikace nevidí klíče zákazníka. I když používáme více Azure Stack služeb a prostředků, můžete klíče spravovat z jednoho umístění v Azure Stack. Trezor poskytuje jedno rozhraní, bez ohledu na to, kolik trezorů máte v Azure Stack, které oblasti podporují a které aplikace je používají.

## <a name="next-steps"></a>Další postup

* [Správa Key Vault v Azure Stack na portálu](azure-stack-key-vault-manage-portal.md)  
* [Správa Key Vault v Azure Stack pomocí prostředí PowerShell](azure-stack-key-vault-manage-powershell.md)
