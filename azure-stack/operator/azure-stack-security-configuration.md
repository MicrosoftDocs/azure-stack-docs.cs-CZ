---
title: Konfigurace ovládacích prvků zabezpečení centra Azure Stack
description: Naučte se konfigurovat řízení zabezpečení v centru Azure Stack.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/17/2019
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 06/17/2019
ms.openlocfilehash: 72757c67a10e11eabd88d55fb4dee7adcb2fe558
ms.sourcegitcommit: 390eac7abc94cea1405178e8d6a9358f6488f5d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78231566"
---
# <a name="configure-azure-stack-hub-security-controls"></a>Konfigurace ovládacích prvků zabezpečení centra Azure Stack

Tento článek vysvětluje ovládací prvky zabezpečení, které je možné změnit v Azure Stack hub, a zvýrazní případné kompromisy.

Architektura centra Azure Stack je postavená na dvou bezpečnostních pilířích zabezpečení: ve výchozím nastavení předpokládat porušení a posílení zabezpečení. Další informace Azure Stack zabezpečení centra najdete v tématu [zabezpečení infrastruktury centra Azure Stack stav](azure-stack-security-foundations.md). I když je výchozí stav centra zabezpečení Azure Stack pro přípravu do provozu, existují některé scénáře nasazení, které vyžadují další posílení zabezpečení.

## <a name="tls-version-policy"></a>Zásada verze TLS

Protokol TLS (Transport Layer Security) je široce přijatý kryptografický protokol pro navázání šifrované komunikace přes síť. Protokol TLS se vyvinul v čase a vystavilo se několik verzí. Infrastruktura centra Azure Stack pro veškerou komunikaci používá pouze TLS 1,2. V případě externích rozhraní Azure Stack centrum aktuálně používá protokol TLS 1,2. U zpětné kompatibility ale taky podporuje vyjednávání až po TLS 1,1. a 1,0. Když klient TLS požaduje komunikaci přes TLS 1,1 nebo TLS 1,0, Azure Stack centrum dodrží požadavek vyjednáním s nižší verzí TLS. Pokud klient požaduje TLS 1,2, Azure Stack hub vytvoří připojení TLS pomocí protokolu TLS 1,2.

Vzhledem k tomu, že TLS 1,0 a 1,1 jsou postupně zastaralé nebo zakázané organizacemi a standardy dodržování předpisů, a to od aktualizace 1906, můžete teď nakonfigurovat zásadu TLS v centru Azure Stack. Zásadu pouze TLS 1,2 můžete vystavit pouze v případě, že se některý pokus o vytvoření relace protokolu TLS s verzí nižší než 1,2 není povolen a zamítnut.

> [!IMPORTANT]
> Microsoft doporučuje používat pouze zásady TLS 1,2 pro produkční prostředí Azure Stack hub.

## <a name="get-tls-policy"></a>Získat zásady TLS

Pro zobrazení zásad TLS pro všechny koncové body centra Azure Stack použijte [privilegovaný koncový bod (PEP)](azure-stack-privileged-endpoint.md) :

```powershell
Get-TLSPolicy
```

Příklad výstupu:

    TLS_1.2

## <a name="set-tls-policy"></a>Nastavení zásad TLS

Pomocí [privilegovaného koncového bodu (PEP)](azure-stack-privileged-endpoint.md) nastavte zásady TLS pro všechny koncové body centra Azure Stack:

```powershell
Set-TLSPolicy -Version <String>
```

Parametry pro rutinu *set-TLSPolicy* :

| Parametr | Popis | Typ | Požaduje se |
|---------|---------|---------|---------|
| *Verze* | Povolené verze protokolu TLS v centru Azure Stack | Řetězec | ano|

Pomocí jedné z následujících hodnot nakonfigurujte povolené verze TLS pro všechny koncové body centra Azure Stack:

| Hodnota verze | Popis |
|---------|---------|
| *TLS_All* | Koncové body TLS centra Azure Stack podporují protokol TLS 1,2, ale je povolené vyjednávání na TLS 1,1 a TLS 1,0. |
| *TLS_1.2* | Koncové body TLS centra Azure Stack podporují pouze TLS 1,2. | 

Aktualizace zásad TLS trvá několik minut.

### <a name="enforce-tls-12-configuration-example"></a>Vynutilit příklad konfigurace TLS 1,2

Tento příklad nastaví zásady protokolu TLS tak, aby vynutila pouze TLS 1,2.

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

### <a name="allow-all-versions-of-tls-12-11-and-10-configuration-example"></a>Příklad konfigurace povolení všech verzí TLS (1,2, 1,1 a 1,0)

Tento příklad nastaví zásady TLS tak, aby povolovaly všechny verze TLS (1,2, 1,1 a 1,0).

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

## <a name="next-steps"></a>Další kroky

- [Další informace o stav zabezpečení infrastruktury centra Azure Stack](azure-stack-security-foundations.md)
- [Naučte se, jak tyto tajné klíče otočit v centru Azure Stack.](azure-stack-rotate-secrets.md)
- [Aktualizace antivirové ochrany v programu Windows Defender v centru Azure Stack](azure-stack-security-av.md)
