---
title: Integrace služby DNS centra Azure Stack Datacenter | Microsoft Docs
description: Přečtěte si, jak integrovat službu DNS centra Azure Stack k vašemu serveru DNS vašeho datového centra.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/21/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 08/21/2019
keywords: ''
ms.openlocfilehash: dc2a1f1c5cd386e5b5e473c2607b7fc63e49bf50
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817971"
---
# <a name="azure-stack-hub-datacenter-dns-integration"></a>Integrace služby DNS centra Azure Stack

Aby bylo možné získat přístup k koncovým bodům centra Azure Stack, jako jsou **portál**, **adminportal**, **Správa**a **adminmanagement** z vnějšího centra Azure Stack, je nutné integrovat služby DNS centra Azure Stack se servery DNS, které hostují zóny dns, které chcete použít v centru pro Azure Stack.

## <a name="azure-stack-hub-dns-namespace"></a>Obor názvů DNS centra Azure Stack

Pokud nasazujete Azure Stack centrum, budete muset poskytnout nějaké důležité informace týkající se DNS.


|Pole  |Popis  |Příklad:|
|---------|---------|---------|
|Region (Oblast)|Geografické umístění vašeho nasazení centra Azure Stack.|`east`|
|Název externí domény|Název zóny, kterou chcete použít pro nasazení centra Azure Stack.|`cloud.fabrikam.com`|
|Internal Domain Name|Název interní zóny, která se používá pro služby infrastruktury v centru Azure Stack. Je to integrovaná a soukromá adresářová služba (není dostupná z vnějšku nasazení Azure Stack hub).|`azurestack.local`|
|Servery DNS pro přeposílání|Servery DNS, které se používají k přeposílání dotazů DNS, zón DNS a záznamů hostovaných mimo centrum Azure Stack, buď na podnikovém intranetu nebo na veřejném Internetu. Po nasazení můžete hodnotu služby DNS resílat upravit pomocí [rutiny **set-AzSDnsForwarder** ](#editing-dns-forwarder-ips) . 
|Předpona názvů (volitelné)|Předpona názvů, kterou chcete, aby názvy počítačů v rolích infrastruktury centra Azure Stack měly.  Pokud není zadaný, výchozí hodnota je `azs`.|`azs`|

Plně kvalifikovaný název domény (FQDN) vašeho nasazení centra Azure Stack a koncových bodů je kombinací parametru region a parametru názvu externí domény. Při použití hodnot z příkladů v předchozí tabulce bude plně kvalifikovaný název domény pro toto nasazení centra Azure Stack následující název:

`east.cloud.fabrikam.com`

Například příklady některých koncových bodů tohoto nasazení by vypadaly jako následující adresy URL:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Pokud chcete použít tento ukázkový obor názvů DNS pro nasazení centra Azure Stack, vyžadují se tyto podmínky:

- Zóna `fabrikam.com` je zaregistrovaná buď s doménovým registrátorem, interním podnikovým serverem DNS nebo obojím, v závislosti na vašich požadavcích na překlad názvů.
- Podřízená doména `cloud.fabrikam.com` existuje v `fabrikam.com`zóny.
- Servery DNS, které hostují zóny `fabrikam.com` a `cloud.fabrikam.com`, jsou dostupné z nasazení centra Azure Stack.

Aby bylo možné přeložit názvy DNS pro koncové body a instance centra Azure Stack mimo Azure Stack rozbočovače, je potřeba integrovat servery DNS, které hostují externí zónu DNS pro Azure Stack hub se servery DNS, které hostují nadřazenou zónu, kterou chcete použít.

### <a name="dns-name-labels"></a>Popisky názvů DNS

Centrum Azure Stack podporuje přidání popisku názvu DNS k veřejné IP adrese, aby se povolilo překlad názvů pro veřejné IP adresy. Popisky DNS představují pohodlný způsob, jak uživatelům oslovit aplikace a služby hostované v Azure Stack centrum podle názvu. Popisek názvu DNS používá mírně odlišný obor názvů než koncové body infrastruktury. V následujícím ukázkovém oboru názvů se obor názvů pro popisky názvů DNS zobrazí takto:

`*.east.cloudapp.cloud.fabrikam.com`

Proto pokud tenant indikuje hodnotu **MyApp** v poli popisek názvu DNS prostředku veřejné IP adresy, vytvoří záznam a pro **Myapp** v zóně **East.CLOUDAPP.Cloud.fabrikam.com** na externím serveru DNS centra Azure Stack. Výsledný plně kvalifikovaný název domény se zobrazí takto:

`myapp.east.cloudapp.cloud.fabrikam.com`

Pokud chcete tuto funkci využít a použít tento obor názvů, musíte integrovat servery DNS, které hostují externí zónu DNS pro Azure Stack hub se servery DNS, které hostují nadřazenou zónu, kterou chcete použít. Jedná se o jiný obor názvů než obor názvů pro koncové body služby Azure Stack hub, takže musíte vytvořit další pravidlo delegování nebo podmíněného předávání.

Další informace o tom, jak popisek názvu DNS funguje, najdete [v tématu použití DNS v centru Azure Stack](../user/azure-stack-dns.md).

## <a name="resolution-and-delegation"></a>Překládání a delegování

Existují dva typy serverů DNS:

- Autoritativní server DNS hostuje zóny DNS. Odpovídá pouze na dotazy DNS pro záznamy v těchto zónách.
- Rekurzivní server DNS nehostuje zóny DNS. Odpovídá na všechny dotazy DNS voláním autoritativních serverů DNS, které shromáždí potřebná data.

Centrum Azure Stack zahrnuje autoritativní i rekurzivní servery DNS. Rekurzivní servery se používají k překladu názvů všeho s výjimkou interní privátní zóny a externí veřejné zóny DNS pro nasazení centra Azure Stack.

![Architektura služby DNS centra Azure Stack](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack-hub"></a>Překlad externích názvů DNS z centra Azure Stack

Chcete-li přeložit názvy DNS pro koncové body mimo Azure Stack centra (například: www\.bing.com), je nutné poskytnout servery DNS, které Azure Stack hub můžou použít k přeposílání požadavků DNS, pro které Azure Stack hub není autoritativní. Pro nasazení se v listu nasazení (v poli pro přeposílání DNS) vyžadují servery DNS, na které Azure Stack požadavky na rozbočovače dopředně požadavky. Pro odolnost proti chybám zadejte v tomto poli aspoň dva servery. Bez těchto hodnot není nasazení centra Azure Stack úspěšné. Po nasazení můžete hodnoty DNS pro přeposílání upravit pomocí [rutiny **set-AzSDnsForwarder** ](#editing-dns-forwarder-ips) . 



### <a name="configure-conditional-dns-forwarding"></a>Konfigurace podmíněného předávání DNS

> [!IMPORTANT]
> To platí jenom pro nasazení AD FS.

Pokud chcete povolit překlad názvů pomocí existující infrastruktury DNS, nakonfigurujte podmíněné předávání.

Chcete-li přidat podmíněný předávací server, je nutné použít privilegovaný koncový bod.

Pro tento postup použijte počítač v síti datového centra, který může komunikovat s privilegovaným koncovým bodem v centru Azure Stack.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce) a připojte se k IP adrese privilegovaného koncového bodu. Použijte přihlašovací údaje pro ověřování CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Po připojení k privilegovanému koncovému bodu spusťte následující příkaz PowerShellu. Nahraďte vzorové hodnoty poskytované vaším názvem domény a IP adresami serverů DNS, které chcete použít.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-hub-dns-names-from-outside-azure-stack-hub"></a>Překlad názvů DNS centra Azure Stack z vnějšího centra Azure Stack
Autoritativní servery jsou ty, které obsahují informace o externích zónách DNS, a všechny zóny vytvořené uživatelem. Integrací s těmito servery umožníte delegování zóny nebo podmíněné předávání k překladu Azure Stack názvů DNS centra z vnějšího centra Azure Stack.

## <a name="get-dns-server-external-endpoint-information"></a>Získat informace o externím koncovém bodu serveru DNS

K integraci nasazení centra Azure Stack s infrastrukturou DNS potřebujete tyto informace:

- Plně kvalifikované názvy domén serveru DNS
- DNS server IP addresses

Plně kvalifikované názvy domén pro servery DNS centra Azure Stack mají následující formát:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Pomocí ukázkových hodnot jsou plně kvalifikované názvy domény pro servery DNS:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Tyto informace se vytvoří také na konci všech nasazení centra Azure Stack v souboru s názvem `AzureStackStampInformation.json`. Tento soubor se nachází ve složce `C:\CloudDeployment\logs` virtuálního počítače nasazení. Pokud si nejste jistí, jaké hodnoty byly použity pro nasazení centra Azure Stack, můžete získat hodnoty z tohoto místa.

Pokud virtuální počítač pro nasazení už není dostupný nebo není dostupný, můžete hodnoty získat připojením k privilegovanému koncovému bodu a spuštěním rutiny `Get-AzureStackStampInformation` PowerShellu. Další informace najdete v tématu [privilegovaný koncový bod](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack-hub"></a>Nastavení podmíněného předávání do centra Azure Stack

Nejjednodušší a nejbezpečnější způsob, jak integrovat Azure Stack hub s infrastrukturou DNS, je provést podmíněné přesměrování zóny ze serveru, který hostuje nadřazenou zónu. Tento přístup se doporučuje, pokud máte přímou kontrolu nad servery DNS, které hostují nadřazenou zónu pro externí obor názvů DNS centra Azure Stack.

Pokud nejste obeznámeni s tím, jak provést podmíněné přesměrování pomocí služby DNS, přečtěte si následující článek na webu TechNet: [přiřazení podmíněného předávání pro název domény](https://technet.microsoft.com/library/cc794735)nebo dokumentaci specifickou pro vaše řešení DNS.

V případech, kdy jste určili svou externí zónu DNS centra Azure Stack, aby vypadala jako podřízená doména názvu vaší podnikové domény, nelze použít podmíněné přesměrování. Je třeba nakonfigurovat delegování DNS.

Příklad:

- Název domény DNS společnosti: `contoso.com`
- Název externí domény DNS centra Azure Stack: `azurestack.contoso.com`

## <a name="editing-dns-forwarder-ips"></a>Úprava IP adresy DNS pro přeposílání

IP adresy DNS pro přeposílání se nastavují během nasazování centra Azure Stack. Pokud se ale IP adresy pro předávací službu musí z jakéhokoli důvodu aktualizovat, můžete hodnoty upravit připojením k privilegovanému koncovému bodu a spuštěním rutin `Get-AzSDnsForwarder` a `Set-AzSDnsForwarder [[-IPAddress] <IPAddress[]>]` PowerShellu. Další informace najdete v tématu [privilegovaný koncový bod](azure-stack-privileged-endpoint.md).

## <a name="delegating-the-external-dns-zone-to-azure-stack-hub"></a>Delegování externí zóny DNS do centra Azure Stack

Aby bylo možné přeložit názvy DNS z vnějšku nasazení centra Azure Stack, je třeba nastavit delegování DNS.

Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. Na stránce správy DNS registrátora upravte záznamy NS a nahraďte záznamy NS pro zónu těmi v Azure Stack hub.

Většina registrátorů DNS vyžaduje, abyste k dokončení delegování zadali aspoň dva servery DNS.

## <a name="next-steps"></a>Další kroky

[Integrace brány firewall](azure-stack-firewall.md)
