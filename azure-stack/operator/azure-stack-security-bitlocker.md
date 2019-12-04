---
title: Šifrování dat v klidovém umístění
titleSuffix: Azure Stack
description: Přečtěte si, jak Azure Stack chrání vaše data pomocí šifrování v klidovém prostředí.
services: azure-stack
author: PatAltimore
ms.service: azure-stack
ms.topic: article
ms.date: 06/17/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
keywords: ''
ms.openlocfilehash: 13455668330571e9190d37ea0abb4de2a7b88a5d
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780707"
---
# <a name="data-at-rest-encryption-in-azure-stack"></a>Šifrování neaktivních dat v Azure Stack

Azure Stack chrání data uživatelů a infrastruktury na úrovni subsystému úložiště pomocí šifrování v klidovém prostředí. Subsystém úložiště Azure Stack je zašifrovaný pomocí nástroje BitLocker s 128 šifrováním AES. Klíče BitLockeru jsou trvale uložené v interním úložišti tajných klíčů.

Šifrování neaktivních dat je běžným požadavkem na mnoho hlavních standardů dodržování předpisů (například PCI-DSS, FedRAMP, HIPAA). Azure Stack vám umožní splnit tyto požadavky bez dalších požadavků na práci nebo konfigurace. Další informace o tom, jak Azure Stack pomáhá splnit standardy dodržování předpisů, najdete na [portálu Microsoft Trust Service](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Šifrování neaktivních dat chrání vaše data před tím, než je někdo, který fyzicky stole aspoň jeden pevný disk. Šifrování neaktivních dat nechrání proti datům zachyceným přes síť (přenášená data), aktuálně používaná data (data v paměti) nebo obecně exfiltrated data, která jsou v provozu.

## <a name="retrieving-bitlocker-recovery-keys"></a>Načítání klíčů pro obnovení nástroje BitLocker

Azure Stack klíče BitLockeru pro data v klidovém umístění jsou interně spravovány. Nemusíte je zadávat pro běžné operace nebo při spuštění systému. Scénáře podpory ale můžou vyžadovat, aby obnovovací klíč nástroje BitLocker mohl přenést systém do online režimu.  

> [!WARNING]
> Načtěte obnovovací klíče BitLockeru a uložte je do zabezpečeného umístění mimo Azure Stack. Neexistence klíčů pro obnovení v průběhu určitých scénářů podpory může způsobit ztrátu dat a vyžadovat obnovení systému ze záložní image.

Načtení klíčů pro obnovení BitLockeru vyžaduje přístup k [privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md) (PEP). Z relace PEP spusťte rutinu Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Volitelné parametry pro rutinu *Get-AzsRecoveryKeys* :

| Parametr | Popis | Typ | Požaduje se |
|---------|---------|---------|---------|
|*získání* | Vrátí nezpracovaná data mapování mezi obnovovacím klíčem, názvem počítače a ID hesel každého zašifrovaného svazku.  | Přepínač | Ne (navržené pro scénáře podpory)|

## <a name="troubleshoot-issues"></a>Řešení potíží

V extrémních případech se může požadavek na odemčení BitLockeru způsobit neúspěšné spuštění konkrétního svazku. V závislosti na dostupnosti některých součástí architektury může toto selhání vést k výpadkům a potenciálním ztrátám dat, pokud nemáte klíče pro obnovení BitLockeru.

> [!WARNING]
> Načtěte obnovovací klíče BitLockeru a uložte je do zabezpečeného umístění mimo Azure Stack. Neexistence klíčů pro obnovení v průběhu určitých scénářů podpory může způsobit ztrátu dat a vyžadovat obnovení systému ze záložní image.

Pokud se domníváte, že v systému dochází k problémům s BitLockerem, například při selhání spuštění Azure Stack, obraťte se na podporu. Podpora vyžaduje klíče pro obnovení nástroje BitLocker. Většinu problémů spojených s BitLockerem je možné vyřešit pomocí operace FRU pro konkrétní virtuální počítač/hostitele/svazek. V ostatních případech je možné provést ruční odblokování pomocí klíčů pro obnovení BitLockeru. Pokud klíče pro obnovení nástroje BitLocker nejsou k dispozici, jedinou možností je obnovení ze záložní image. V závislosti na tom, kdy byla poslední záloha provedena, může dojít ke ztrátě dat.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o Azure Stack zabezpečení](azure-stack-security-foundations.md).
- Další informace o tom, jak BitLocker chrání CSV, najdete v tématu [Ochrana sdílených svazků clusteru a sítí SAN pomocí nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).