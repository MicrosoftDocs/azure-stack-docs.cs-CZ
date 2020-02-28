---
title: Šifrování dat v klidovém umístění
titleSuffix: Azure Stack Hub
description: Přečtěte si, jak Azure Stack centrum chrání vaše data pomocí šifrování v klidovém prostředí.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 06/17/2019
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 67d24f65e57df8827d8a8e91962ab3bc11cd6290
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697650"
---
# <a name="data-at-rest-encryption-in-azure-stack-hub"></a>Šifrování neaktivních dat v centru Azure Stack

Centrum Azure Stack chrání data uživatelů a infrastruktury na úrovni subsystému úložiště pomocí šifrování v klidovém prostředí. Subsystém úložiště Azure Stackového rozbočovače je šifrován pomocí nástroje BitLocker s 128 šifrováním AES. Klíče BitLockeru jsou trvale uložené v interním úložišti tajných klíčů.

Šifrování neaktivních dat je běžným požadavkem na mnoho hlavních standardů dodržování předpisů (například PCI-DSS, FedRAMP, HIPAA). Centrum Azure Stack vám umožní splnit tyto požadavky bez dalších požadavků na práci nebo konfigurace. Další informace o tom, jak centrum Azure Stack pomáhá splnit standardy dodržování předpisů, najdete na [portálu Microsoft Trust Service](https://aka.ms/AzureStackCompliance).

> [!NOTE]
> Šifrování neaktivních dat chrání vaše data před tím, než je někdo, který fyzicky stole aspoň jeden pevný disk. Šifrování neaktivních dat nechrání proti datům zachyceným přes síť (přenášená data), aktuálně používaná data (data v paměti) nebo obecně exfiltrated data, která jsou v provozu.

## <a name="retrieving-bitlocker-recovery-keys"></a>Načítání klíčů pro obnovení nástroje BitLocker

Klíče nástroje BitLocker pro Azure Stack centra pro neaktivní neaktivní data jsou spravovány interně. Nemusíte je zadávat pro běžné operace nebo při spuštění systému. Scénáře podpory ale můžou vyžadovat, aby obnovovací klíč nástroje BitLocker mohl přenést systém do online režimu.  

> [!WARNING]
> Načtěte obnovovací klíče BitLockeru a uložte je do zabezpečeného umístění mimo centrum Azure Stack. Neexistence klíčů pro obnovení v průběhu určitých scénářů podpory může způsobit ztrátu dat a vyžadovat obnovení systému ze záložní image.

Načtení klíčů pro obnovení BitLockeru vyžaduje přístup k [privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md) (PEP). Z relace PEP spusťte rutinu Get-AzsRecoveryKeys.

```powershell
##This cmdlet retrieves the recovery keys for all the volumes that are encrypted with BitLocker.
Get-AzsRecoveryKeys
```

Volitelné parametry pro rutinu *Get-AzsRecoveryKeys* :

| Parametr | Popis | Typ | Požadováno |
|---------|---------|---------|---------|
|*získání* | Vrátí nezpracovaná data mapování mezi obnovovacím klíčem, názvem počítače a ID hesel každého zašifrovaného svazku.  | Přepínač | Ne (navržené pro scénáře podpory)|

## <a name="troubleshoot-issues"></a>Řešení potíží

V extrémních případech se může požadavek na odemčení BitLockeru způsobit neúspěšné spuštění konkrétního svazku. V závislosti na dostupnosti některých součástí architektury může toto selhání vést k výpadkům a potenciálním ztrátám dat, pokud nemáte klíče pro obnovení BitLockeru.

> [!WARNING]
> Načtěte obnovovací klíče BitLockeru a uložte je do zabezpečeného umístění mimo centrum Azure Stack. Neexistence klíčů pro obnovení v průběhu určitých scénářů podpory může způsobit ztrátu dat a vyžadovat obnovení systému ze záložní image.

Pokud máte podezření, že v systému dochází k problémům s BitLockerem, jako je Azure Stack selhání centra, obraťte se na podporu. Podpora vyžaduje klíče pro obnovení nástroje BitLocker. Většinu problémů spojených s BitLockerem je možné vyřešit pomocí operace FRU pro konkrétní virtuální počítač/hostitele/svazek. V ostatních případech je možné provést ruční odblokování pomocí klíčů pro obnovení BitLockeru. Pokud klíče pro obnovení nástroje BitLocker nejsou k dispozici, jedinou možností je obnovení ze záložní image. V závislosti na tom, kdy byla poslední záloha provedena, může dojít ke ztrátě dat.

## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace o Azure Stack zabezpečení centra](azure-stack-security-foundations.md).
- Další informace o tom, jak BitLocker chrání CSV, najdete v tématu [Ochrana sdílených svazků clusteru a sítí SAN pomocí nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/protecting-cluster-shared-volumes-and-storage-area-networks-with-bitlocker).