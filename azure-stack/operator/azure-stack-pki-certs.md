---
title: Azure Stack požadavky na certifikát infrastruktury veřejných klíčů | Microsoft Docs
description: Přečtěte si o Azure Stack požadavcích na nasazení certifikátů PKI pro Azure Stack integrovaných systémů.
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
ms.openlocfilehash: f306391451c4d04af3b5a37645f145fb732714f0
ms.sourcegitcommit: acebda8a42ac8ecdeba490fc1738e9041479dab0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72813998"
---
# <a name="azure-stack-public-key-infrastructure-certificate-requirements"></a>Azure Stack požadavky na certifikát infrastruktury veřejných klíčů

Azure Stack má síť s veřejnou infrastrukturou využívající externě přístupné veřejné IP adresy, které jsou přiřazené k malé sadě služeb Azure Stack a případně k virtuálním počítačům klientů. Při nasazování služby Azure Stack se vyžadují certifikáty PKI s odpovídajícími názvy DNS pro tyto veřejné koncové body infrastruktury Azure Stack. Tento článek poskytuje informace o:

- Jaké certifikáty jsou vyžadovány k nasazení Azure Stack.
- Proces získání certifikátů, které odpovídají těmto specifikacím.
- Příprava, ověřování a používání těchto certifikátů během nasazení.

> [!NOTE]
> Ve výchozím nastavení používá Azure Stack také certifikáty vydané z interní certifikační autority (CA) integrované se službou Active Directory pro ověřování mezi uzly. Pokud chcete certifikát ověřit, všechny počítače infrastruktury Azure Stack důvěřují kořenovému certifikátu interní certifikační autority přidáním tohoto certifikátu do místního úložiště certifikátů. V Azure Stack není žádné připnutí ani seznam povolených certifikátů. SÍŤ SAN každého certifikátu serveru je ověřena vůči plně kvalifikovanému názvu domény cíle. Také se ověří celý řetěz důvěryhodnosti spolu s datem vypršení platnosti certifikátu (standardní ověřování serveru TLS bez připnutí certifikátu).

## <a name="certificate-requirements"></a>Požadavky na certifikát
Následující seznam popisuje požadavky na certifikáty, které jsou potřeba k nasazení Azure Stack:

- Certifikáty se musí vydávat buď z interní certifikační autority, nebo z veřejné certifikační autority. Pokud se používá Veřejná certifikační autorita, musí být součástí základní image operačního systému v rámci programu Microsoft Trusted root Authority. Úplný seznam najdete v tématu [program důvěryhodných kořenových certifikátů společnosti Microsoft: účastníci](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
- Vaše infrastruktura Azure Stack musí mít síťový přístup k umístění seznamu odvolaných certifikátů (CRL) certifikační autority publikovaného v certifikátu. Tento seznam odvolaných certifikátů musí být koncovým bodem http.
- Při střídání certifikátů v předběžně 1903 sestaveních musí být certifikáty vystavené ze stejné interní certifikační autority, která se používá k podepisování certifikátů poskytovaných při nasazení nebo kterékoli veřejné certifikační autority. V 1903 a novějších verzích je možné certifikáty vystavovat v rámci certifikační autority nebo veřejné certifikační autority.
- Použití certifikátů podepsaných svým držitelem není podporováno.
- Pro nasazení a rotaci můžete buď použít jeden certifikát, který pokrývá všechny obory názvů v poli název předmětu a alternativní název předmětu (SAN) certifikátu, nebo můžete použít jednotlivé certifikáty pro každý z oborů názvů pod tímto Azure Stack služby, které hodláte využít. Oba přístupy vyžadují použití zástupných karet pro koncové body, kde jsou povinné, jako je třeba **trezor** klíčů a **KeyVaultInternal**.
- Šifrování PFX certifikátu by mělo být 3DES.
- Algoritmus podpisu certifikátu by neměl být SHA1.
- Formát certifikátu musí být PFX, protože pro Azure Stack instalaci jsou vyžadovány veřejné i privátní klíče. Privátní klíč musí mít nastaven atribut klíč místního počítače.
- Šifrování PFX musí být 3DES (Toto šifrování je při exportu z úložiště certifikátů klienta Windows 10 nebo Windows serveru 2016 výchozí.
- V poli "použití klíče" musí mít soubory PFX s certifikátem hodnotu "Digital Signature" a "KeyEncipherment".
- Soubory PFX certifikátu musí mít v poli rozšířené použití klíče hodnoty ověřování serveru (1.3.6.1.5.5.7.3.1) a ověřování klientů (1.3.6.1.5.5.7.3.2).
- Pole certifikátu ' vydáno pro: ' nesmí být stejné jako jeho pole ' vydáno uživatelem: '.
- Hesla pro všechny soubory PFX certifikátu musí být v době nasazení stejná.
- Heslo k certifikátu PFX musí být složité heslo. Toto heslo si poznamenejte, protože ho použijete jako parametr nasazení. Heslo musí splňovat následující požadavky na složitost hesla:
    - Minimální délka je osm znaků.
    - Alespoň tři z následujících znaků: velké písmeno, malé písmeno, číslice od 0-9, speciální znaky, abecední znak, který není velká a malá písmena.
- Zajistěte, aby se názvy subjektů a alternativní názvy předmětů v příponě alternativního názvu subjektu (x509v3_config) shodovaly. V poli alternativní název subjektu můžete zadat další názvy hostitelů (websites, IP adresy, běžné názvy), které mají být chráněné jedním certifikátem SSL.

> [!NOTE]  
> Certifikáty podepsané svým držitelem nejsou podporovány.

> [!NOTE]  
> *Je* podporována přítomnost zprostředkujících certifikačních autorit v řetězu certifikátů.

## <a name="mandatory-certificates"></a>Povinné certifikáty
Tabulka v této části popisuje Azure Stack certifikáty PKI veřejných koncových bodů, které jsou vyžadovány pro nasazení Azure AD i AD FS Azure Stack. Požadavky na certifikát jsou seskupené podle oblasti a také jako používané obory názvů a certifikáty, které jsou požadovány pro každý obor názvů. Tabulka také popisuje složku, ve které poskytovatel řešení kopíruje různé certifikáty na veřejný koncový bod.

Vyžadují se certifikáty s příslušnými názvy DNS pro každý Azure Stack koncový bod veřejné infrastruktury. Název DNS každého koncového bodu je vyjádřen ve formátu: *&lt;> předpony.&lt;oblast >.&lt;plně kvalifikovaný název domény >* .

Pro vaše nasazení musí hodnoty [region] a [externalfqdn] odpovídat oblasti a názvům externích domén, které jste zvolili pro váš Azure Stack systém. Příklad: Pokud byl název oblasti *Redmond* a externí název domény byl *contoso.com*, názvy DNS budou mít formát *&lt;předponu >. Redmond. contoso. com*. Hodnota *předpony&lt;* je předdefinována společností Microsoft, aby popsala koncový bod zabezpečený certifikátem. Kromě toho *prefix&lt;* hodnoty externích koncových bodů infrastruktury závisí na službě Azure Stack, která používá konkrétní koncový bod.

Pro produkční prostředí doporučujeme pro každý koncový bod vygenerovat jednotlivé certifikáty a zkopírovat je do odpovídajícího adresáře. Pro vývojová prostředí se certifikáty dají zadat jako jeden certifikát se zástupnými znaky, který pokrývá všechny obory názvů v polích předmět a alternativní název předmětu (SAN) zkopírované do všech adresářů. Jeden certifikát, který pokrývá všechny koncové body a služby, je nezabezpečený stav, a proto jenom pro vývoj. Pamatujte si, že obě možnosti vyžadují, abyste použili certifikáty se zástupnými znaky pro koncové body, jako je **ACS** , a Key Vault tam, kde jsou

> [!Note]  
> Během nasazování musíte zkopírovat certifikáty do složky pro nasazení, která odpovídá zprostředkovateli identity, na který nasazujete (Azure AD nebo AD FS). Pokud používáte jeden certifikát pro všechny koncové body, musíte tento soubor certifikátu zkopírovat do každé složky pro nasazení, jak je uvedeno v následujících tabulkách. Struktura složky je předem vytvořená ve virtuálním počítači pro nasazení a je možné ji najít na adrese: C:\CloudDeployment\Setup\Certificates.

| Složka pro nasazení | Požadovaný předmět certifikátu a alternativní názvy subjektu (SAN) | Rozsah (na oblast) | Obor názvů subdomény |
|-------------------------------|------------------------------------------------------------------|----------------------------------|-----------------------------|
| Veřejný portál | bran. >&lt;oblasti.&lt;plně kvalifikovaný název domény > | Portály | > &lt;oblasti.&lt;plně kvalifikovaný název domény > |
| Portál pro správu | adminportal. >&lt;oblasti.&lt;plně kvalifikovaný název domény > | Portály | > &lt;oblasti.&lt;plně kvalifikovaný název domény > |
| Azure Resource Manager veřejné | správu. >&lt;oblasti.&lt;plně kvalifikovaný název domény > | Azure Resource Manager | > &lt;oblasti.&lt;plně kvalifikovaný název domény > |
| Správce Azure Resource Manager | adminmanagement. >&lt;oblasti.&lt;plně kvalifikovaný název domény > | Azure Resource Manager | > &lt;oblasti.&lt;plně kvalifikovaný název domény > |
| ACSBlob | *. blob. >&lt;oblasti.&lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) | Blob Storage | příznaky. >&lt;oblasti.&lt;plně kvalifikovaný název domény > |
| ACSTable | *. Table. >&lt;oblasti.&lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) | Table Storage | stolní. >&lt;oblasti.&lt;plně kvalifikovaný název domény > |
| ACSQueue | *. Queue. >&lt;oblasti.&lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) | Queue Storage | provedených. >&lt;oblasti.&lt;plně kvalifikovaný název domény > |
| KeyVault | *. trezor. >&lt;oblasti.&lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) | Key Vault | hesel. >&lt;oblasti.&lt;plně kvalifikovaný název domény > |
| KeyVaultInternal | *.adminvault. >&lt;oblasti.&lt;plně kvalifikovaný název domény ><br>(Zástupný certifikát SSL) |  Interní Trezor klíčů |  adminvault. >&lt;oblasti.&lt;plně kvalifikovaný název domény > |
| Hostitel rozšíření Správce | *.adminhosting. >\<oblasti.\<plně kvalifikovaný název domény > (zástupné certifikáty SSL) | Hostitel rozšíření Správce | adminhosting. >\<oblasti.\<plně kvalifikovaný název domény > |
| Hostitel veřejného rozšíření | *. Hosting. >\<oblasti.\<plně kvalifikovaný název domény > (zástupné certifikáty SSL) | Hostitel veřejného rozšíření | který. >\<oblasti.\<plně kvalifikovaný název domény > |

Pokud nasadíte Azure Stack pomocí režimu nasazení služby Azure AD, stačí si vyžádat certifikáty uvedené v předchozí tabulce. Pokud však Azure Stack nasazujete pomocí režimu nasazení AD FS, musíte si také vyžádat certifikáty popsané v následující tabulce:

|Složka pro nasazení|Požadovaný předmět certifikátu a alternativní názvy subjektu (SAN)|Rozsah (na oblast)|Obor názvů subdomény|
|-----|-----|-----|-----|
|ADFS|Službou. *&lt;region >. &lt;fqdn >*<br>(Certifikát SSL)|ADFS|*&lt;oblasti.&lt;plně kvalifikovaný název domény >*|
|Graf|Zapisovací. *&lt;region >. &lt;fqdn >*<br>(Certifikát SSL)|Graf|*&lt;oblasti.&lt;plně kvalifikovaný název domény >*|
|

> [!IMPORTANT]
> Všechny certifikáty uvedené v této části musí mít stejné heslo.

## <a name="optional-paas-certificates"></a>Volitelné certifikáty PaaS
Pokud plánujete nasadit další Azure Stack PaaS služby (SQL, MySQL a App Service) po nasazení a konfiguraci Azure Stack, musíte požádat o další certifikáty, abyste pokryli koncové body služeb PaaS.

> [!IMPORTANT]
> Certifikáty, které používáte pro poskytovatele prostředků App Service, SQL a MySQL, musí mít stejnou kořenovou autoritu jako ty, které se používají pro globální koncové body Azure Stack.

Následující tabulka obsahuje popis koncových bodů a certifikátů vyžadovaných pro adaptéry SQL a MySQL a pro App Service. Tyto certifikáty nemusíte kopírovat do složky pro nasazení Azure Stack. Místo toho tyto certifikáty poskytnete při instalaci dalších poskytovatelů prostředků.

|Rozsah (na oblast)|Certifikát|Požadovaný předmět certifikátu a alternativní názvy subjektu (San)|Obor názvů subdomény|
|-----|-----|-----|-----|
|SQL, MySQL|SQL a MySQL|&#42;.dbadapter. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*<br>(Zástupný certifikát SSL)|dbadapter. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*|
|Aplikační služba|Výchozí certifikát SSL pro webový provoz|&#42;AppService. *&lt;region >. &lt;fqdn >*<br>&#42;. SCM. AppService. *&lt;region >. &lt;fqdn >*<br>&#42;. SSO. AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*<br>(Certifikát SSL s více doménovými znaky<sup>1</sup>)|AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*<br>SCM. AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*|
|Aplikační služba|API|API. AppService. *&lt;region >. &lt;fqdn >*<br>(Certifikát SSL<sup>2</sup>)|AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*<br>SCM. AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*|
|Aplikační služba|FTP|FTP. AppService. *&lt;region >. &lt;fqdn >*<br>(Certifikát SSL<sup>2</sup>)|AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*<br>SCM. AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*|
|Aplikační služba|JEDNOTNÉ|SSO. AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*<br>(Certifikát SSL<sup>2</sup>)|AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*<br>SCM. AppService. *&lt;oblasti.&lt;plně kvalifikovaný název domény >*|

<sup>1</sup> vyžaduje jeden certifikát s více alternativními názvy subjektu zástupného znaku. Všechny veřejné certifikační autority nemusí podporovat více než jeden zástupný znak sítě SAN na jednom certifikátu.

<sup>2</sup> A &#42;. AppService. *&lt;oblast >.&lt;plně kvalifikovaný název domény >* certifikát pro zástupné znaky se nedá použít místo těchto tří certifikátů (API. appservice. *&lt;region >.&lt;plně kvalifikovaný název domény >* , ftp. appservice. *&lt;oblast >.&lt;plně kvalifikovaný název domény >* a SSO. AppService. *&lt;oblast >.&lt;plně kvalifikovaný název domény >* . AppService explicitně vyžaduje použití samostatných certifikátů pro tyto koncové body.

## <a name="learn-more"></a>Další informace
Naučte se [generovat certifikáty PKI pro nasazení Azure Stack](azure-stack-get-pki-certs.md).

## <a name="next-steps"></a>Další kroky
[Integrujte AD FS identitu do svého datového centra Azure Stack](azure-stack-integrate-identity.md).