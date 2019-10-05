---
title: Napravit problémy s certifikátem pro Azure Stack | Microsoft Docs
description: Pomocí nástroje pro kontrolu připravenosti na Azure Stack zkontrolujte a opravte problémy s certifikáty.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/19/2018
ms.openlocfilehash: 6e8adbc0d84c7816a081e751473764aab79cfcf2
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961916"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Řešení běžných problémů Azure Stack certifikátů PKI

Informace v tomto článku vám pomůžou pochopit a vyřešit běžné problémy s Azure Stack certifikáty PKI. Problémy můžete zjišťovat při použití nástroje pro kontrolu připravenosti Azure Stack k [ověření Azure Stack certifikátů PKI](azure-stack-validate-pki-certs.md). Nástroj zkontroluje, jestli certifikáty splňují požadavky na infrastrukturu veřejných klíčů pro nasazení Azure Stack a Azure Stackou rotaci, a výsledky zaprotokolují do [souboru Report. JSON](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Šifrování PFX

**Selhání** – šifrování PFX není v TRIPLEDES-SHA1.

**Oprava** – EXPORTUJTE soubory PFX pomocí šifrování **TripleDES-SHA1** . Toto je výchozí nastavení pro všechny klienty Windows 10 při exportu z modulu snap-in certifikáty nebo pomocí `Export-PFXCertificate`.

## <a name="read-pfx"></a>Přečíst PFX

**Upozornění** – heslo chrání jenom soukromé informace v certifikátu.  

**Oprava** – EXPORTUJTE soubory PFX s volitelným nastavením pro **možnost Povolit ochranu osobních údajů certifikátu**.  

**Chyba** – soubor PFX je neplatný.  

**Náprava** – certifikát znovu exportujte pomocí postupu v části [Příprava Azure Stack certifikátů PKI pro nasazení](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmus podpisu

**Chyba** – algoritmus podpisu je SHA1.

**Náprava** – pomocí kroků v části Azure Stack certifikáty podepisování požadavků vygenerujte žádost o podepsání certifikátu (CSR) pomocí algoritmu podpisu SHA256. Poté znovu odešlete CSR k certifikační autoritě, aby se certifikát mohl znovu vystavit.

## <a name="private-key"></a>Privátní klíč

**Chyba** : privátní klíč chybí nebo neobsahuje atribut místního počítače.  

**Náprava** – z počítače, který vygeneroval CSR, znovu exportujte certifikát pomocí postupu v části [Příprava Azure Stack certifikátů PKI pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment). Tyto kroky zahrnují export z úložiště certifikátů místního počítače.

## <a name="certificate-chain"></a>Řetěz certifikátů

**Selhání** – řetěz certifikátů není úplný.  

**Náprava** – certifikáty by měly obsahovat úplný řetěz certifikátů. Znovu exportujte certifikát pomocí postupu v části [příprava Azure Stack certifikátů PKI pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) a vyberte možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**.

## <a name="dns-names"></a>Názvy DNS

**Chyba** – **DNSNameList** na certifikátu neobsahuje název koncového bodu služby Azure Stack nebo platná shoda se zástupnými znaky. Zástupné shody jsou platné pouze pro levý krajní obor názvů názvu DNS. Například hodnota `*.region.domain.com` je platná pouze pro `portal.region.domain.com`, nikoli `*.table.region.domain.com`.

**Náprava** – pomocí kroků v Azure Stack certifikáty podepisování žádosti o podepsání znovu vygenerujte CSR se správnými názvy DNS pro podporu Azure Stackch koncových bodů. Odešlete CSR do certifikační autority a pak postupujte podle kroků v části [příprava Azure Stack certifikátů PKI pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) , které vyexportují certifikát z počítače, který vygeneroval CSR.  

## <a name="key-usage"></a>Použití klíče

**Selhání** -použití klíče postrádá digitální podpis nebo šifrování klíče nebo rozšířené použití klíče nemá ověřování serveru nebo ověřování klientů.  

**Náprava** – pomocí kroků v [Azure Stack certifikáty podepisování žádosti o podepsání](azure-stack-get-pki-certs.md) znovu vygenerujte CSR se správnými atributy použití klíče. Odešlete CSR do certifikační autority a ověřte, že šablona certifikátu nepřepisuje použití klíče v žádosti.

## <a name="key-size"></a>Velikost klíče

**Chyba** -velikost klíče je menší než 2048.

**Náprava** – pomocí kroků v [Azure Stack certifikáty podepisování žádosti o podepsání](azure-stack-get-pki-certs.md) znovu vygenerujte CSR se správnou délkou klíče (2048) a pak znovu odešlete CSR k certifikační autoritě.

## <a name="chain-order"></a>Pořadí řetězců

**Selhání** – pořadí certifikátů je nesprávné.  

**Náprava** – certifikát znovu exportujte pomocí postupu v části [Příprava Azure Stack certifikátů PKI pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment) a vyberte možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Zajistěte, aby byl pro export vybraný jenom listový certifikát.

## <a name="other-certificates"></a>Další certifikáty

**Chyba** – balíček PFX obsahuje certifikáty, které nejsou listový certifikát nebo část řetězu certifikátů.  

**Náprava** – certifikát znovu exportujte pomocí postupu v části [Příprava Azure Stack certifikátů PKI pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-for-deployment)a vyberte možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Zajistěte, aby byl pro export vybraný jenom listový certifikát.

## <a name="fix-common-packaging-issues"></a>Řešení běžných problémů s balíčkem

Nástroj **AzsReadinessChecker** obsahuje pomocnou rutinu nazvanou **Repair-AzsPfxCertificate**, která může importovat a exportovat soubor PFX za účelem opravy běžných problémů s balíčkem, včetně:

- **Šifrování PFX** není v TRIPLEDES-SHA1.
- V **privátním klíči** chybí atribut místního počítače.
- **Řetěz certifikátů** je neúplný nebo chybný. Pokud balíček PFX nepoužívá, musí místní počítač obsahovat řetěz certifikátů.
- **Další certifikáty**

**Oprava – AzsPfxCertificate** nemůže pomáhat, pokud potřebujete vygenerovat nového CSR a znovu vydat certifikát.

### <a name="prerequisites"></a>Požadavky

Na počítači, na kterém je nástroj spuštěný, musí být nahlášené tyto požadavky:

- Windows 10 nebo Windows Server 2016 s připojením k Internetu.
- PowerShell 5,1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující rutinu prostředí PowerShell a pak zkontrolujte *Hlavní* *** a** podverze:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Nakonfigurujte [PowerShell pro Azure Stack](azure-stack-powershell-install.md).
- Stáhněte si nejnovější verzi nástroje pro [kontrolu připravenosti na Azure Stack](https://aka.ms/AzsReadinessChecker) .

### <a name="import-and-export-an-existing-pfx-file"></a>Importovat a exportovat existující soubor PFX

1. V počítači, který splňuje požadavky, otevřete příkazový řádek PowerShellu se zvýšenými oprávněními a spusťte následující příkaz pro instalaci nástroje pro kontrolu připravenosti Azure Stack:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Z příkazového řádku PowerShellu spusťte následující rutinu a nastavte heslo PFX. Nahraďte `PFXpassword` skutečným heslem:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. Z příkazového řádku PowerShellu spusťte následující příkaz pro export nového souboru PFX:

   - U `-PfxPath` zadejte cestu k souboru PFX, se kterým pracujete. V následujícím příkladu je cesta `.\certificates\ssl.pfx`.
   - Pro `-ExportPFXPath` zadejte umístění a název souboru PFX pro export. V následujícím příkladu je cesta `.\certificates\ssl_new.pfx`:

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Po dokončení nástroje si přečtěte výstup pro úspěch:

   ```shell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Další kroky

- [Další informace o zabezpečení služby Azure Stack](azure-stack-rotate-secrets.md)
