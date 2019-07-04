---
title: Konfiguraci ovládacích prvků zabezpečení služby Azure Stack
description: Další informace o konfiguraci kontrolních mechanismů pro zabezpečení ve službě Azure Stack
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: b36a6d826dc7249f10b4785b27511096e45923a9
ms.sourcegitcommit: 7348876a97e8bed504b5f5d90690ec8d1d9472b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67557862"
---
# <a name="configure-azure-stack-security-controls"></a>Konfiguraci ovládacích prvků zabezpečení služby Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Tento článek vysvětluje ovládací prvky zabezpečení, které lze změnit ve službě Azure Stack a zvýrazní nevýhody, kde je to možné.

Architektura služby Azure Stack je postavená na dva hlavní pilíře zabezpečení: předpokládej chybu zabezpečení a posílené už ve výchozím nastavení. Další informace o zabezpečení služby Azure Stack, najdete v části [stavu zabezpečení infrastruktury Azure stacku](azure-stack-security-foundations.md). Výchozí stav zabezpečení služby Azure Stack je připravené pro produkční prostředí, nejsou některé scénáře nasazení, které vyžadují další posílení zabezpečení.

## <a name="tls-version-policy"></a>Zásada verze TLS

Protokol zabezpečení TLS (Transport Layer) je běžně používaných kryptografických protokol k navázání šifrovaného komunikace v síti. Protokol TLS se průběžně vyvíjel a byly vydány více verzí. Infrastruktura Azure stacku výhradně používá TLS 1.2 pro jeho komunikaci. Pro externí rozhraní služby Azure Stack aktuálně výchozí použití protokolu TLS 1.2. Ale pro zpětnou kompatibilitu, podporuje také vyjednávání dolů protokolu TLS 1.1. do 1,0. Když klient TLS požaduje pro komunikaci pomocí protokolu TLS 1.1 a TLS 1.0, Azure Stack respektuje žádost o vyjednávání na nižší verzi TLS. Pokud si klient vyžádá TLS 1.2, Azure Stack se připojení TLS pomocí protokolu TLS 1.2.

Protože protokol TLS 1.0 a 1.1 se postupně přestanou nebo zakázané standardy organizace a dodržování předpisů, od. 1906 aktualizace teď můžete nakonfigurovat zásady TLS ve službě Azure Stack. Můžou vynutit zásadu jenom TLS 1.2. Pokud není jakékoli pokusy o navázání relace protokolu TLS verze nižší než 1.2 odmítl a povolené.

> [!IMPORTANT]
> Microsoft doporučuje používat pouze zásady protokolu TLS 1.2 pro produkční prostředí Azure Stack.

## <a name="get-tls-policy"></a>Získání zásad pro protokol TLS

Použití [privilegovaných koncový bod (období)](azure-stack-privileged-endpoint.md) TLS zásadách pro všechny koncové body služby Azure Stack:

```powershell
Get-TLSPolicy
```

Příklad výstupu:

    TLS_1.2

## <a name="set-tls-policy"></a>Nastavení zásad TLS

Použití [privilegovaných koncový bod (období)](azure-stack-privileged-endpoint.md) nastavení zásad protokolu TLS pro všechny koncové body služby Azure Stack:

```powershell
Set-TLSPolicy -Version <String>
```

Parametry pro *Set-TLSPolicy* rutiny:

| Parametr | Popis | Type | Požaduje se |
|---------|---------|---------|---------|
| *Verze* | Povolené verze protokolu TLS ve službě Azure Stack | String | ano|

Ke konfiguraci povolených verzí protokolu TLS pro všechny koncové body služby Azure Stack, použijte jednu z následujících hodnot:

| Hodnota verze | Popis |
|---------|---------|
| *TLS_All* | Koncové body Azure Stack TLS podporovala TLS 1.2, ale dolů vyjednávání protokolu TLS 1.1 a TLS 1.0 je povolen. |
| *TLS_1.2* | Koncové body Azure Stack TLS podporují jenom TLS 1.2. | 

Aktualizují se zásady TLS trvá několik minut na dokončení.

### <a name="enforce-tls-12-configuration-example"></a>Vynucení protokolu TLS 1.2 konfigurace – příklad

V tomto příkladu nastaví TLS zásadu vynutit pouze TLS 1.2.

```powershell
Set-TLSPolicy -Version TLS_1.2
```

Příklad výstupu:

    VERBOSE: Successfully setting enforce TLS 1.2 to True
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is enforced

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Povolit všechny verze protokolu TLS (1.1 a 1.2, 1.0) konfigurace – příklad

Tento příklad nastaví zásadu TLS povolit všechny verze protokolu TLS (1.1 a 1.2, 1.0).

```powershell
Set-TLSPolicy -Version TLS_All
```

Příklad výstupu:

    VERBOSE: Successfully setting enforce TLS 1.2 to False
    VERBOSE: Invoking action plan to update GPOs
    VERBOSE: Create Client for execution of action plan
    VERBOSE: Start action plan
    <...>
    VERBOSE: Verifying TLS policy
    VERBOSE: Get GPO TLS protocols registry 'enabled' values
    VERBOSE: GPO TLS applied with the following preferences:
    VERBOSE:     TLS protocol SSL 2.0 enabled value: 0
    VERBOSE:     TLS protocol SSL 3.0 enabled value: 0
    VERBOSE:     TLS protocol TLS 1.0 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.1 enabled value: 1
    VERBOSE:     TLS protocol TLS 1.2 enabled value: 1
    VERBOSE: TLS 1.2 is not enforced

## <a name="next-steps"></a>Další postup

- [Další informace o stavu zabezpečení infrastruktury Azure stacku](azure-stack-security-foundations.md)
- [Zjistěte, jak otočit vaše tajné kódy ve službě Azure Stack](azure-stack-rotate-secrets.md)
- [Aktualizovat antivirové ochrany Windows Defender v Azure stacku](azure-stack-security-av.md)
