---
title: Úvod do Key Vault v centru Azure Stack
description: Přečtěte si, jak Key Vault spravuje klíče a tajné kódy v centru Azure Stack.
author: sethmanheim
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 09db1c2e8daa34b566512ca6daee2480a86527df
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704042"
---
# <a name="introduction-to-key-vault-in-azure-stack-hub"></a>Úvod do Key Vault v centru Azure Stack

## <a name="prerequisites"></a>Požadavky

* Přihlaste se k odběru nabídky, která zahrnuje službu Azure Key Vault.  
* PowerShell je nainstalovaný a [nakonfigurovaný pro použití s rozbočovačem Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Základy Key Vault

Key Vault v centru Azure Stack pomáhá chránit kryptografické klíče a tajné kódy, které využívají cloudové aplikace a služby. Pomocí Key Vault můžete šifrovat klíče a tajné klíče, například:

* Ověřovací klíče
* Klíče účtu úložiště
* Šifrovací klíče dat
* soubory. pfx
* Hesla

Key Vault zjednodušuje proces správy klíčů a zajišťuje vám kontrolu nad klíči, které se používají k přístupu a šifrování dat. Vývojáři můžou během pár minut vytvořit klíče pro vývoj a testování a potom je bez problémů migrovat na produkční klíče. Správci zabezpečení můžou k klíčům udělit oprávnění (a odvolat jim) podle potřeby.

Kdokoli s předplatným centra Azure Stack může vytvářet a používat trezory klíčů. I když Key Vault výhody vývojářů a správců zabezpečení, operátor, který spravuje jiné služby centra Azure Stack pro organizaci, může implementovat a spravovat. Například operátor centra Azure Stack se může přihlásit pomocí předplatného centra Azure Stack a vytvořit trezor pro organizaci, ve které se klíče mají ukládat. Až to bude hotové, můžou:

* Vytvořte nebo importujte klíč nebo tajný klíč.
* Odvolejte nebo odstraňte klíč nebo tajný klíč.
* Udělte uživatelům nebo aplikacím přístup k trezoru klíčů, aby mohli spravovat nebo používat jeho klíče a tajné kódy.
* Nakonfigurujte použití klíče (například podpis nebo šifrování).

Operátor pak může poskytnout vývojářům pomocí identifikátorů URI (Uniform Resource Identifier) pro volání ze svých aplikací. Operátoři můžou taky poskytovat správcům zabezpečení informace o protokolování použití klíče.

Vývojáři můžou klíče spravovat i přímo pomocí rozhraní API. Další informace najdete v příručce pro [vývojáře Key Vault](/azure/key-vault/key-vault-developers-guide).

## <a name="scenarios"></a>Scénáře

Následující scénáře popisují, jak Key Vault mohou pokrýt potřeby vývojářů a správců zabezpečení.

### <a name="developer-for-an-azure-stack-hub-app"></a>Vývojář pro aplikaci Azure Stack hub

**Problém:** Chci napsat aplikaci pro Azure Stack hub, která používá klíče pro podepisování a šifrování. Chci, aby tyto klávesy byly externí z mé aplikace, aby bylo řešení vhodné pro geograficky distribuovanou aplikaci.

**Příkaz:** Klíče jsou uloženy v trezoru a v případě potřeby vyvolány identifikátorem URI.

### <a name="developer-for-software-as-a-service-saas"></a>Vývojář softwaru jako služby (SaaS)

**Problém:** Nechci odpovědnost nebo potenciální odpovědnost za klíče a tajné kódy daného zákazníka. Chci, aby si zákazníci mohli vlastnit a spravovat svoje klíče, abyste se mohli soustředit na to, co je nejlepší, což poskytuje základní softwarové funkce.

**Příkaz:** Zákazníci mohou importovat a spravovat vlastní klíče v centru Azure Stack.

### <a name="chief-security-officer-cso"></a>Vedoucí oddělení zabezpečení (CSO)

**Problém:** Chci mít jistotu, že má moje organizace kontrolu nad životním cyklem klíčů a může monitorovat použití klíče.

**Příkaz:** Key Vault je navržený tak, aby vám Microsoft neviděli ani neextrahuje vaše klíče. Když aplikace potřebuje provést kryptografické operace pomocí zákaznických klíčů, Key Vault použije klíče jménem aplikace. Aplikace nevidí klíče zákazníka. I když používáme více služeb centra Azure Stack a prostředků, můžete klíče spravovat z jednoho místa v centru Azure Stack. Trezor poskytuje jedno rozhraní, bez ohledu na to, kolik trezorů máte v Azure Stack hub, které oblasti podporují a které aplikace je používají.

## <a name="next-steps"></a>Další kroky

* [Správa Key Vault v centru Azure Stack na portálu](azure-stack-key-vault-manage-portal.md)  
* [Správa Key Vault v centru Azure Stack pomocí prostředí PowerShell](azure-stack-key-vault-manage-powershell.md)
