---
title: Azure Stack požadavky na certifikát infrastruktury veřejných klíčů pro Azure Stack integrované systémy | Microsoft Docs
description: Popisuje Azure Stack požadavky na nasazení certifikátů PKI pro Azure Stack integrovaných systémů.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 53d8e3daecba269bcdd21fc726e312758f1f6c6f
ms.sourcegitcommit: 38f21e0bcf7b593242ad615c9d8ef8a1ac19c734
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70902706"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure Stack požadavky na certifikát infrastruktury veřejných klíčů

Azure Stack má síť s veřejnou infrastrukturou využívající externě přístupné veřejné IP adresy, které jsou přiřazené k malé sadě služeb Azure Stack a případně k virtuálním počítačům klientů. Při nasazování služby Azure Stack se vyžadují certifikáty PKI s odpovídajícími názvy DNS pro tyto veřejné koncové body infrastruktury Azure Stack. Tento článek poskytuje informace o:

- Jaké certifikáty jsou vyžadovány k nasazení Azure Stack
- Proces získání certifikátů, které odpovídají těmto specifikacím
- Příprava, ověřování a používání těchto certifikátů během nasazení

> [!NOTE]
> Ve výchozím nastavení používá Azure Stack také certifikáty vydané z interní certifikační autority (CA) integrované se službou Active Directory pro ověřování mezi uzly. Pokud chcete certifikát ověřit, všechny počítače infrastruktury Azure Stack důvěřují kořenovému certifikátu interní certifikační autority přidáním tohoto certifikátu do místního úložiště certifikátů. V Azure Stack není žádné připnutí ani seznam povolených certifikátů. SÍŤ SAN každého certifikátu serveru je ověřena vůči plně kvalifikovanému názvu domény cíle. Také se ověří celý řetěz důvěryhodnosti spolu s datem vypršení platnosti certifikátu (standardní ověřování serveru TLS bez připnutí certifikátu).

## <a name="certificate-requirements"></a>Požadavky na certifikát
Následující seznam popisuje požadavky na certifikáty, které jsou potřeba k nasazení Azure Stack: 
- Certifikáty se musí vydávat buď z interní certifikační autority, nebo z veřejné certifikační autority. Pokud se používá Veřejná certifikační autorita, musí být součástí základní image operačního systému v rámci programu Microsoft Trusted root Authority. Úplný seznam najdete tady: https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca 
- Vaše infrastruktura Azure Stack musí mít síťový přístup k umístění seznamu odvolaných certifikátů (CRL) certifikační autority publikovaného v certifikátu. Tento seznam odvolaných certifikátů musí být koncovým bodem http.
- Při střídání certifikátů v předběžně 1903 sestaveních musí být certifikáty vystavené ze stejné interní certifikační autority, která se používá k podepisování certifikátů poskytovaných při nasazení nebo kterékoli veřejné certifikační autority. Pro 1903 & novějších certifikátů může vydávat jakákoli Podniková nebo Veřejná certifikační autorita.
- Použití certifikátů podepsaných svým držitelem není podporováno.
- Pro nasazení a rotaci můžete buď použít jeden certifikát, který pokrývá všechny obory názvů v poli název předmětu a alternativní název předmětu (SAN) certifikátu, nebo můžete použít jednotlivé certifikáty pro každý z oborů názvů pod tímto Azure Stack služby, které hodláte využít. Oba přístupy vyžadují použití zástupných znaků pro koncové body, pokud jsou vyžadovány, například **trezor** klíčů a **KeyVaultInternal**. 
- Šifrování PFX certifikátu by mělo být 3DES. 
- Algoritmus podpisu certifikátu by neměl být SHA1. 
- Formát certifikátu musí být PFX, protože pro Azure Stack instalaci jsou vyžadovány veřejné i privátní klíče. Privátní klíč musí mít nastaven atribut klíč místního počítače.
- Šifrování PFX musí být 3DES (Toto je výchozí nastavení při exportu z úložiště certifikátů klienta Windows 10 nebo Windows serveru 2016).
- V poli "použití klíče" musí mít soubory PFX s certifikátem hodnotu "Digital Signature" a "KeyEncipherment".
- Soubory PFX certifikátu musí mít v poli rozšířené použití klíče hodnoty ověřování serveru (1.3.6.1.5.5.7.3.1) a ověřování klientů (1.3.6.1.5.5.7.3.2).
- Pole certifikátu ' vydáno pro: ' nesmí být stejné jako jeho pole ' vydáno uživatelem: '.
- Hesla pro všechny soubory PFX certifikátu musí být v době nasazení stejná.
- Heslo k certifikátu PFX musí být složité heslo. Vytvořte heslo, které splňuje následující požadavky na složitost hesla. Minimální délka je osm znaků. Heslo obsahuje minimálně tři z následujících znaků: velká písmena, malá písmena, číslice od 0-9, speciální znaky, abecední znak, který není velká ani malá. Poznamenejte si toto heslo. Budete ho používat jako parametr nasazení.
- Zajistěte, aby se názvy subjektů a alternativní názvy předmětů v příponě alternativního názvu subjektu (x509v3_config) shodovaly. V poli alternativní název subjektu můžete zadat další názvy hostitelů (websites, IP adresy, běžné názvy), které mají být chráněné jedním certifikátem SSL.

> [!NOTE]  
> Certifikáty podepsané svým vlastníkem nejsou podporovány.

> [!NOTE]  
> *Je* podporována přítomnost zprostředkujících certifikačních autorit v řetězu certifikátů. 

## <a name="mandatory-certificates"></a>Povinné certifikáty
Tabulka v této části popisuje Azure Stack certifikáty PKI veřejných koncových bodů, které jsou vyžadovány pro nasazení Azure AD i AD FS Azure Stack. Požadavky na certifikát jsou seskupené podle oblasti a také jako používané obory názvů a certifikáty, které jsou požadovány pro každý obor názvů. Tabulka také popisuje složku, ve které poskytovatel řešení kopíruje různé certifikáty na veřejný koncový bod. 

Vyžadují se certifikáty s příslušnými názvy DNS pro každý Azure Stack koncový bod veřejné infrastruktury. Název DNS každého koncového bodu je vyjádřen ve formátu:  *&lt;prefix >.&lt; > oblasti plně&lt;kvalifikovaný název domény >* . 

Pro vaše nasazení musí hodnoty [region] a [externalfqdn] odpovídat oblasti a názvům externích domén, které jste zvolili pro váš Azure Stack systém. Příklad: Pokud byl název oblasti *Redmond* a externí název domény byl *contoso.com*, názvy DNS budou mít předponu formátu  *&lt;>. Redmond. contoso. com*. Předpona > hodnoty jsou předdefinována společností Microsoft, aby popsala koncový bod zabezpečený certifikátem.  *&lt;* Kromě toho je  *&lt;prefix >* hodnoty externích koncových bodů infrastruktury závislý na službě Azure Stack, která používá konkrétní koncový bod. 

Pro produkční prostředí doporučujeme pro každý koncový bod vygenerovat jednotlivé certifikáty a zkopírovat je do odpovídajícího adresáře. Pro vývojová prostředí se certifikáty dají zadat jako jeden certifikát se zástupnými znaky, který pokrývá všechny obory názvů v polích předmět a alternativní název předmětu (SAN) zkopírované do všech adresářů. Jeden certifikát, který pokrývá všechny koncové body a služby, je nezabezpečený stav, takže jenom pro vývoj. Pamatujte si, že obě možnosti vyžadují, abyste použili certifikáty se zástupnými znaky pro koncové body, jako je **ACS** , a Key Vault tam, kde jsou potřeba. 

> [!Note]  
> Během nasazování musíte zkopírovat certifikáty do složky pro nasazení, která odpovídá zprostředkovateli identity, na který nasazujete (Azure AD nebo AD FS). Pokud používáte jeden certifikát pro všechny koncové body, musíte tento soubor certifikátu zkopírovat do každé složky pro nasazení, jak je uvedeno v následujících tabulkách. Struktura složky je předem sestavena ve virtuálním počítači nasazení a je možné ji najít v umístění: C:\CloudDeployment\Setup\Certificates. 


| Složka pro nasazení | Požadovaný předmět certifikátu a alternativní názvy subjektu (SAN) | Rozsah (na oblast) | SubDomain namespace |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Veřejný portál | bran. &lt;> oblasti &lt;plně kvalifikovaný název domény > | Portály | &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| Portál pro správu | adminportal. &lt;> oblasti &lt;plně kvalifikovaný název domény > | Portály | &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| Azure Resource Manager veřejné | správu. &lt;> oblasti &lt;plně kvalifikovaný název domény > | Azure Resource Manager | &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| Správce Azure Resource Manager | adminmanagement.&lt;region>.&lt;fqdn> | Azure Resource Manager | &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| ACSBlob | *.blob.&lt;region>.&lt;fqdn><br>(Zástupný certifikát SSL) | Úložiště objektů blob | příznaky. &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| ACSTable | *. Table. &lt;> oblasti &lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) | Table Storage | stolní. &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| ACSQueue | *. Queue. &lt;> oblasti &lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) | Queue Storage | provedených. &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| KeyVault | *. trezor. &lt;> oblasti &lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) | Key Vault | hesel. &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| KeyVaultInternal | *.adminvault. &lt;> oblasti &lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) |  Interní Trezor klíčů |  adminvault. &lt;> oblasti &lt;plně kvalifikovaný název domény > |
| Hostitel rozšíření Správce | *.adminhosting. \<> oblasti \<plně kvalifikovaný název domény > (zástupné certifikáty SSL) | Hostitel rozšíření Správce | adminhosting.\<region>.\<fqdn> |
| Hostitel veřejného rozšíření | *. Hosting. \<> oblasti \<plně kvalifikovaný název domény > (zástupné certifikáty SSL) | Hostitel veřejného rozšíření | který. \<> oblasti \<plně kvalifikovaný název domény > |

Pokud nasadíte Azure Stack pomocí režimu nasazení služby Azure AD, stačí si vyžádat certifikáty uvedené v předchozí tabulce. Pokud však Azure Stack nasazujete pomocí režimu nasazení AD FS, musíte si také vyžádat certifikáty popsané v následující tabulce:

|Složka pro nasazení|Požadovaný předmět certifikátu a alternativní názvy subjektu (SAN)|Rozsah (na oblast)|SubDomain namespace|
|-----|-----|-----|-----|
|ADFS|adfs. *&lt;region>.&lt;fqdn>*<br>(Certifikát SSL)|ADFS|*&lt;> oblasti &lt;plně kvalifikovaný název domény >*|
|Graph|Zapisovací.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*<br>(Certifikát SSL)|Graph|*&lt;> oblasti &lt;plně kvalifikovaný název domény >*|
|

> [!IMPORTANT]
> Všechny certifikáty uvedené v této části musí mít stejné heslo. 

## <a name="optional-paas-certificates"></a>Volitelné certifikáty PaaS
Pokud plánujete nasadit další Azure Stack PaaS služby (SQL, MySQL a App Service) po nasazení a konfiguraci Azure Stack, budete muset požádat o další certifikáty, abyste pokryli koncové body služeb PaaS. 

> [!IMPORTANT]
> Certifikáty, které používáte pro poskytovatele prostředků App Service, SQL a MySQL, musí mít stejnou kořenovou autoritu jako ty, které se používají pro globální koncové body Azure Stack. 

Následující tabulka obsahuje popis koncových bodů a certifikátů vyžadovaných pro adaptéry SQL a MySQL a pro App Service. Tyto certifikáty nemusíte kopírovat do složky pro nasazení Azure Stack. Místo toho tyto certifikáty poskytnete při instalaci dalších poskytovatelů prostředků. 

|Rozsah (na oblast)|Certifikát|Požadovaný předmět certifikátu a alternativní názvy subjektu (San)|SubDomain namespace|
|-----|-----|-----|-----|
|SQL, MySQL|SQL a MySQL|&#42;.dbadapter.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*<br>(Zástupný certifikát SSL)|dbadapter.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >*|
|App Service|Výchozí certifikát SSL pro webový provoz|&#42;.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.scm.appservice. *&lt;region>.&lt;fqdn>*<br>&#42;.sso.appservice. *&lt;region>.&lt;fqdn>*<br>(Certifikát SSL s více doménovými znaky<sup>1</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|rozhraní API|api.appservice. *&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|FTP|ftp.appservice. *&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|
|App Service|Jednotné přihlašování|sso.appservice. *&lt;region>.&lt;fqdn>*<br>(Certifikát SSL<sup>2</sup>)|appservice. *&lt;region>.&lt;fqdn>*<br>scm.appservice. *&lt;region>.&lt;fqdn>*|

<sup>1</sup> vyžaduje jeden certifikát s více alternativními názvy subjektu zástupného znaku. Všechny veřejné certifikační autority nemusí podporovat více než jeden zástupný znak sítě SAN na jednom certifikátu. 

<sup>2</sup> A &#42;. AppService.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >* zástupný certifikát nemůže být použit místo těchto tří certifikátů (API. AppService. *&lt;> oblasti plně&lt;kvalifikovaný název domény >* , FTP. AppService.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >* a SSO. AppService.  *>oblasti&lt;&lt; plně kvalifikovaný název domény >* . AppService explicitně vyžaduje použití samostatných certifikátů pro tyto koncové body. 

## <a name="learn-more"></a>Víc se uč
Naučte se [generovat certifikáty PKI pro nasazení Azure Stack](azure-stack-get-pki-certs.md). 

## <a name="next-steps"></a>Další kroky
[Integrace identit](azure-stack-integrate-identity.md)