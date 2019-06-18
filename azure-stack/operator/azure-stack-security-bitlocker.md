---
title: Data šifrování neaktivních dat ve službě Azure Stack
description: Zjistěte, jak Azure Stack chrání svoje data pomocí šifrování v klidovém stavu
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 0e21808ad82a61014b69cb26958a0c13518af13a
ms.sourcegitcommit: 7fa9b64aeae2b22be7acfb5c4987d233303107c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67166501"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Data šifrování neaktivních dat ve službě Azure Stack

Azure Stack chrání uživatele a infrastruktury data na úrovni subsystému úložiště pomocí šifrování v klidovém stavu. Subsystém úložiště služby Azure Stack je šifrování pomocí nástroje BitLocker s 128bitové šifrování AES. Klíče Bitlockeru jsou zachované v interní úložiště tajných kódů.

Data šifrování neaktivních dat jsou běžné požadavky pro řadu standardů dodržování předpisů hlavní (například PCI-DSS, FedRAMP, HIPAA). Azure Stack umožňuje splňují tyto požadavky bez další práce, konfigurací. Další informace o jak Azure Stack pomáhá splnění standardů dodržování předpisů, najdete v článku [Microsoft Service Trust Portal](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Data šifrování neaktivních dat chrání vaše data před přistupuje někoho, kdo fyzicky odcizit jeden nebo více pevných disků. Data šifrování neaktivních dat neposkytuje ochranu proti dat, které jsou zachyceny v síti (přenášená data), aktuálně používá daty (data v paměti) nebo více obecně platí, data se exfiltrated, zatímco systém je zapnutý a spuštěný.

## <a name="retrieving-bitlocker-recovery-keys"></a>Načítání klíče pro obnovení Bitlockeru

Interně jsou spravované klíče Azure Stack Bitlockeru pro neaktivní uložená data. Není nutné je zadat pro standardních operací nebo při spuštění systému. Scénáře podpory však může vyžadovat obnovení klíče Bitlockeru o vyřazení systému online.  

> [!WARNING]
> Získat klíče pro obnovení nástroje BitLocker a uložit je do zabezpečeného umístění mimo Azure Stack. Bez nutnosti obnovovací klíče při určitých scénářích podpory může dojít ke ztrátě a vyžadují obnovení systému ze zálohy bitové kopie.

Načítání klíče pro obnovení Bitlockeru vyžaduje přístup k [privilegovaných koncový bod](azure-stack-privileged-endpoint.md) (období). Z relace období spusťte rutinu Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Volitelné parametry pro *Get-AzsRecoveryKeys* rutiny:

| Parametr | Popis | Type | Požaduje se |
|---------|---------|---------|---------|
|*nezpracované* | Vrátí nezpracovaná data objektu mapování mezi obnovovací klíč, název počítače a heslo ID každé zašifrovaného svazku  | – přepínač | žádné (Designed for scénáře podpory)|

## <a name="troubleshoot-issues"></a>Řešení potíží

V extrémních případech Bitlockerem odemknout žádost může selhat a vytvářet konkrétní svazek není spustit. V závislosti na dostupnosti některé ze součástí architektury toto selhání by mohlo způsobit výpadky a potenciální ztráty dat Pokud nemáte k dispozici vaše klíče pro obnovení Bitlockeru.

> [!WARNING]
> Získat klíče pro obnovení nástroje BitLocker a uložit je do zabezpečeného umístění mimo Azure Stack. Bez nutnosti obnovovací klíče při určitých scénářích podpory může dojít ke ztrátě a vyžadují obnovení systému ze zálohy bitové kopie.

Pokud máte podezření, že váš systém se setkává s problémy s nástrojem BitLocker, jako je Azure Stack selhání spuštění, obraťte se na podporu. Podpora vyžaduje klíče pro obnovení Bitlockeru. Problémů lze vyřešit pomocí FRU operace pro tento konkrétní virtuální počítač a hostitele/svazek související s většinou nástroje BitLocker. Ostatních případech můžete provést ruční postup odemykání pomocí klíče pro obnovení Bitlockeru. Pokud klíče pro obnovení Bitlockeru nejsou k dispozici, je jediná možnost obnovit ze záložní image. V závislosti na tom, kdy byla provedena poslední zálohy může dojít k úniku.

## <a name="next-steps"></a>Další postup

- [Další informace o zabezpečení služby Azure Stack](azure-stack-security-foundations.md)
- Další informace o tom, jak nástroj BitLocker chrání sdílené svazky clusteru naleznete v tématu [ochraně clusteru sdílené svazky a sítí SAN pomocí Bitlockeru](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).