---
title: Řešení běžných problémů s certifikáty PKI
titleSuffix: Azure Stack Hub
description: Řešení běžných problémů s Azure Stackmi certifikáty PKI centra pomocí nástroje pro kontrolu připravenosti centra Azure Stack
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 11/19/2019
ms.openlocfilehash: c7f17c603a6b54474db4036953f0fbd755d496cf
ms.sourcegitcommit: e72145ebb5eac17a47ba1c9119fd31de545fdace
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2020
ms.locfileid: "88724842"
---
# <a name="fix-common-issues-with-azure-stack-hub-pki-certificates"></a>Řešení běžných problémů s certifikáty PKI Azure Stack hub

Informace v tomto článku vám pomůžou pochopit a vyřešit běžné problémy s Azure Stackmi certifikáty PKI centra. Když použijete nástroj pro kontrolu připravenosti centra Azure Stack k [ověření certifikátů PKI Azure Stack hub](azure-stack-validate-pki-certs.md), můžete zjistit problémy. Nástroj zkontroluje, jestli certifikáty splňují požadavky na infrastrukturu veřejných klíčů pro nasazení centra Azure Stack a rotaci Azure Stack centra, a pak zaprotokolují výsledky do [report.jsv souboru](azure-stack-validation-report.md).  

## <a name="pfx-encryption"></a>Šifrování PFX

**Problém** – šifrování PFX není TRIPLEDES-SHA1.

**Oprava** – EXPORTUJTE soubory PFX pomocí šifrování **TripleDES-SHA1** . Toto je výchozí šifrování pro všechny klienty Windows 10 při exportu z modulu snap-in certifikáty nebo pomocí nástroje `Export-PFXCertificate` .

## <a name="read-pfx"></a>Přečíst PFX

**Upozornění** – heslo chrání jenom soukromé informace v certifikátu.  

**Opravit** – EXPORTUJTE soubory PFX s volitelným nastavením pro **možnost Povolit ochranu osobních údajů certifikátu**.  

**Problém** – soubor PFX je neplatný.  

**Oprava** – znovu exportujte certifikát pomocí postupu v části [Příprava certifikátů infrastruktury veřejných klíčů Azure Stack hub pro nasazení](azure-stack-prepare-pki-certs.md).

## <a name="signature-algorithm"></a>Algoritmus podpisu

Algoritmus **problém** -signatura je SHA1.

**Oprava** – pomocí postupu v Azure Stack centrum certifikátů Podepisování požadavků vygenerujte žádost o podepsání certifikátu (CSR) pomocí algoritmu podpisu SHA256. Poté znovu odešlete CSR k certifikační autoritě, aby se certifikát mohl znovu vystavit.

## <a name="private-key"></a>Privátní klíč

**Problém** – privátní klíč chybí nebo neobsahuje atribut místního počítače.  

**Opravit** – z počítače, který vygeneroval CSR, znovu exportujte certifikát pomocí postupu v části [příprava certifikátů PKI Azure Stack hub pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker). Tyto kroky zahrnují export z úložiště certifikátů místního počítače.

## <a name="certificate-chain"></a>Řetěz certifikátů

**Problém** – řetěz certifikátů není úplný.  

**Oprava** – certifikáty by měly obsahovat úplný řetěz certifikátů. Znovu exportujte certifikát pomocí postupu v části [Příprava certifikátů PKI služby Azure Stack hub pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker) a vyberte možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**.

## <a name="dns-names"></a>Názvy DNS

**Problém** – **DNSNameList** na certifikátu neobsahuje název koncového bodu služby centra Azure Stack ani platnou shodu se zástupnými znaky. Zástupné shody jsou platné pouze pro levý krajní obor názvů názvu DNS. Například `*.region.domain.com` je platná pouze pro `portal.region.domain.com` , nikoli `*.table.region.domain.com` .

**Oprava** – pomocí kroků v Azure Stack centrum certifikátů Podepisování žádosti o podepsání znovu vygenerujte CSR se správnými názvy DNS pro podporu koncových bodů centra Azure Stack. Odešlete CSR do certifikační autority. Pak postupujte podle kroků v části [Příprava certifikátů PKI Azure Stack hub pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker) a exportujte certifikát z počítače, který vygeneroval CSR.  

## <a name="key-usage"></a>Použití klíče

Při použití klíče k **chybě** chybí digitální podpis nebo šifrování klíče nebo rozšířené použití klíče nemá ověřování serveru nebo ověřování klientů.  

**Oprava** – pomocí kroků v části [Azure Stack hub Certificates Signing Request Generation](azure-stack-get-pki-certs.md) znovu vygenerujte CSR se správnými atributy použití klíče. Odešlete CSR do certifikační autority a ověřte, že šablona certifikátu nepřepisuje použití klíče v žádosti.

## <a name="key-size"></a>Velikost klíče

Velikost klíče **problému** je menší než 2048.

**Oprava** – použijte postup v části [Azure Stack vytvoření žádosti o podepsání certifikátů centra](azure-stack-get-pki-certs.md) pro opětovné vygenerování CSR se správnou délkou klíče (2048) a pak znovu odešlete CSR k certifikační autoritě.

## <a name="chain-order"></a>Pořadí řetězců

**Problém** – pořadí certifikátů řetězu není správné.  

**Oprava** – znovu exportujte certifikát pomocí postupu v části [Příprava certifikátů infrastruktury veřejných klíčů Azure Stack hub pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker) a vyberte možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Zajistěte, aby byl pro export vybraný jenom listový certifikát.

## <a name="other-certificates"></a>Další certifikáty

**Problém** – balíček PFX obsahuje certifikáty, které nejsou listový certifikát nebo část řetězu certifikátů.  

**Oprava** – znovu exportujte certifikát pomocí postupu v části [Příprava certifikátů infrastruktury veřejných klíčů Azure Stack hub pro nasazení](azure-stack-prepare-pki-certs.md#prepare-certificates-azure-stack-readiness-checker)a vyberte možnost **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Zajistěte, aby byl pro export vybraný jenom listový certifikát.

## <a name="fix-common-packaging-issues"></a>Řešení běžných problémů s balíčkem

Nástroj **AzsReadinessChecker** obsahuje pomocnou rutinu nazvanou **Repair-AzsPfxCertificate**, která může importovat a exportovat soubor PFX za účelem opravy běžných problémů s balíčkem, včetně:

- **Šifrování PFX** není v TRIPLEDES-SHA1.
- V **privátním klíči** chybí atribut místního počítače.
- **Řetěz certifikátů** je neúplný nebo chybný. Pokud balíček PFX nepoužívá, musí místní počítač obsahovat řetěz certifikátů.
- **Další certifikáty**

**Oprava – AzsPfxCertificate** nemůže pomáhat, pokud potřebujete vygenerovat nového CSR a znovu vystavit certifikát.

### <a name="prerequisites"></a>Požadavky

Na počítači, na kterém je nástroj spuštěný, musí být nahlášené tyto požadavky:

- Windows 10 nebo Windows Server 2016 s připojením k Internetu.
- PowerShell 5,1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující rutinu prostředí PowerShell a pak zkontrolujte **hlavní** a **dílčí** verze:

   ```powershell
   $PSVersionTable.PSVersion
   ```

- Nakonfigurujte [PowerShell pro centrum Azure Stack](azure-stack-powershell-install.md).
- Stáhněte si nejnovější verzi nástroje pro [kontrolu připravenosti centra Azure Stack](https://aka.ms/AzsReadinessChecker) .

### <a name="import-and-export-an-existing-pfx-file"></a>Importovat a exportovat existující soubor PFX

1. V počítači, který splňuje požadavky, otevřete příkazový řádek PowerShellu se zvýšenými oprávněními a spusťte následující příkaz pro instalaci nástroje pro kontrolu připravenosti centra Azure Stack:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Z příkazového řádku PowerShellu spusťte následující rutinu a nastavte heslo PFX. Nahraďte `PFXpassword` skutečným heslem:

   ```powershell
   $password = Read-Host -Prompt PFXpassword -AsSecureString
   ```

3. Z příkazového řádku PowerShellu spusťte následující příkaz pro export nového souboru PFX:

   - V `-PfxPath` poli zadejte cestu k souboru PFX, se kterým pracujete. V následujícím příkladu je cesta `.\certificates\ssl.pfx` .
   - Pro `-ExportPFXPath` Zadejte umístění a název souboru PFX pro export. V následujícím příkladu je cesta `.\certificates\ssl_new.pfx` :

   ```powershell
   Repair-AzsPfxCertificate -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
   ```  

4. Po dokončení nástroje si přečtěte výstup pro úspěch:

   ```shell
   Repair-AzsPfxCertificate v1.1809.1005.1 started.
   Starting Azure Stack Hub Certificate Import/Export
   Importing PFX .\certificates\ssl.pfx into Local Machine Store
   Exporting certificate to .\certificates\ssl_new.pfx
   Export complete. Removing certificate from the local machine store.
   Removal complete.
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Repair-AzsPfxCertificate Completed
   ```

## <a name="next-steps"></a>Další kroky

- [Další informace o zabezpečení centra Azure Stack](azure-stack-rotate-secrets.md)
