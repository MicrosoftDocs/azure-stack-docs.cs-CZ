---
title: Integrace Azure Stack Datacenter – DNS
description: Naučte se integrovat Azure Stack DNS s vaším centrem DNS.
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
ms.openlocfilehash: 9e60a8f9ebda573141e2f97a9182087e90741652
ms.sourcegitcommit: 250689d6d09acc677bf59de76510d5d5f1c6190e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69896358"
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integrace Azure Stack Datacenter – DNS

Aby bylo možné přistupovat k Azure Stack koncovým bodům, jako je **portál**, **adminportal**, **Správa**a **adminmanagement** z vnějšího Azure Stack, je nutné integrovat Azure Stack služby DNS se servery DNS, které hostují zóny DNS. Chcete použít v Azure Stack.

## <a name="azure-stack-dns-namespace"></a>Azure Stack obor názvů DNS

Při nasazení Azure Stack musíte poskytnout nějaké důležité informace týkající se DNS.


|Pole  |Popis  |Příklad|
|---------|---------|---------|
|Oblast|Geografické umístění vašeho nasazení Azure Stack.|`east`|
|Název externí domény|Název zóny, kterou chcete použít pro nasazení Azure Stack.|`cloud.fabrikam.com`|
|Internal Domain Name|Název interní zóny, která se používá pro služby infrastruktury v Azure Stack.  Je to integrovaná a privátní adresářová služba (není dostupná z vnějšku Azure Stack nasazení).|`azurestack.local`|
|Servery DNS pro přeposílání|Servery DNS, které se používají k přeposílání dotazů DNS, zón DNS a záznamů hostovaných mimo Azure Stack, a to buď na podnikovém intranetu nebo na veřejném Internetu. Pokud nahradíte službu DNS pro přeposílání, musí se aktualizovat IP adresa. |`10.57.175.34`<br>`8.8.8.8`|
|Předpona názvů (volitelné)|Předpona názvů, kterou chcete, aby názvy počítačů v rolích infrastruktury Azure Stack měly.  Pokud není zadaný, použije se výchozí `azs`hodnota.|`azs`|

Plně kvalifikovaný název domény (FQDN) nasazení Azure Stack a koncových bodů je kombinací parametru region a parametru názvu externí domény. Při použití hodnot z příkladů v předchozí tabulce bude plně kvalifikovaný název domény pro toto nasazení Azure Stack následující název:

`east.cloud.fabrikam.com`

Například příklady některých koncových bodů tohoto nasazení by vypadaly jako následující adresy URL:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Chcete-li použít tento příklad oboru názvů DNS pro nasazení Azure Stack, jsou vyžadovány následující podmínky:

- Zóna `fabrikam.com` je zaregistrovaná buď s doménovým registrátorem, interním podnikovým serverem DNS nebo obojím, v závislosti na požadavcích na překlad názvů.
- Podřízená doména `cloud.fabrikam.com` existuje v zóně `fabrikam.com`.
- Servery DNS, které hostují zóny `fabrikam.com` a `cloud.fabrikam.com` jsou dostupné z nasazení Azure Stack.

Aby bylo možné přeložit názvy DNS pro koncové body a instance Azure Stack mimo Azure Stack, je nutné integrovat servery DNS, které jsou hostiteli externích zón DNS, pro Azure Stack se servery DNS, které hostují nadřazenou zónu, kterou chcete použít.

### <a name="dns-name-labels"></a>Popisky názvů DNS

Azure Stack podporuje přidání popisku názvu DNS k veřejné IP adrese, aby bylo možné překlad názvů pro veřejné IP adresy. To může být pohodlný způsob, jak uživatelům oslovit aplikace a služby hostované v Azure Stack podle názvu. Popisek názvu DNS používá mírně odlišný obor názvů než koncové body infrastruktury. V následujícím ukázkovém oboru názvů se obor názvů pro popisky názvů DNS zobrazí takto:

`*.east.cloudapp.cloud.fabrikam.com`

Proto pokud tenant indikuje hodnotu **MyApp** v poli popisek názvu DNS prostředku veřejné IP adresy, vytvoří záznam a pro **Myapp** v zóně **East.CLOUDAPP.Cloud.fabrikam.com** na externím serveru DNS Azure Stack. Výsledný plně kvalifikovaný název domény se zobrazí takto:

`myapp.east.cloudapp.cloud.fabrikam.com`

Pokud chcete tuto funkci využít a použít tento obor názvů, musíte integrovat servery DNS, které jsou hostiteli externí zóny DNS, pro Azure Stack se servery DNS, které hostují nadřazenou zónu, kterou chcete použít. Toto je jiný obor názvů než obor názvů pro koncové body služby Azure Stack, takže musíte vytvořit další pravidlo delegování nebo podmíněného předávání.

Další informace o tom, jak popisek názvu DNS funguje, najdete v tématu [použití DNS v Azure Stack](../user/azure-stack-dns.md).

## <a name="resolution-and-delegation"></a>Překládání a delegování

Existují dva typy serverů DNS:

- Autoritativní server DNS hostuje zóny DNS. Odpovídá pouze na dotazy DNS pro záznamy v těchto zónách.
- Rekurzivní server DNS nehostuje zóny DNS. Odpovídá na všechny dotazy DNS voláním autoritativních serverů DNS, které shromáždí potřebná data.

Azure Stack zahrnuje autoritativní i rekurzivní servery DNS. Rekurzivní servery se používají k překladu názvů všeho s výjimkou interní privátní zóny a externí veřejné zóny DNS pro nasazení Azure Stack. 

![Azure Stack architektura DNS](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Překlad externích názvů DNS z Azure Stack

Chcete-li přeložit názvy DNS pro koncové body mimo Azure Stack (například\.: www Bing.com), je nutné poskytnout servery DNS, které Azure Stack mohou použít k přeposílání požadavků DNS, pro které Azure Stack není autoritativní. Pro nasazení se servery DNS, na které Azure Stack předává požadavky, vyžadují v listu nasazení (v poli pro přeposílání DNS). Pro odolnost proti chybám zadejte v tomto poli aspoň dva servery. Bez těchto hodnot není nasazení Azure Stack úspěšné. Pokud se nahrazují servery DNS, aktualizujte IP adresy. 

### <a name="configure-conditional-dns-forwarding"></a>Konfigurace podmíněného předávání DNS

> [!IMPORTANT]
> To platí jenom pro nasazení AD FS.

Pokud chcete povolit překlad názvů pomocí existující infrastruktury DNS, nakonfigurujte podmíněné předávání.

Chcete-li přidat podmíněný předávací server, je nutné použít privilegovaný koncový bod.

Pro tento postup použijte počítač v síti datového centra, který může komunikovat s privilegovaným koncovým bodem v Azure Stack.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce) a připojte se k IP adrese privilegovaného koncového bodu. Použijte přihlašovací údaje pro ověřování CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Po připojení k privilegovanému koncovému bodu spusťte následující příkaz PowerShellu. Nahraďte vzorové hodnoty poskytované vaším názvem domény a IP adresami serverů DNS, které chcete použít.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Překlad Azure Stack názvů DNS z vnějšku Azure Stack
Autoritativní servery jsou ty, které obsahují informace o externích zónách DNS, a všechny zóny vytvořené uživatelem. Integrací s těmito servery umožníte delegování zóny nebo podmíněné předávání k překladu Azure Stack názvů DNS z vnějšku Azure Stack.

## <a name="get-dns-server-external-endpoint-information"></a>Získat informace o externím koncovém bodu serveru DNS

K integraci nasazení Azure Stack s infrastrukturou DNS potřebujete tyto informace:

- Plně kvalifikované názvy domén serveru DNS
- DNS server IP addresses

Plně kvalifikované názvy domén pro Azure Stack DNS serverů mají následující formát:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Pomocí ukázkových hodnot jsou plně kvalifikované názvy domény pro servery DNS:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Tyto informace se vytvoří také na konci všech Azure Stack nasazení v souboru s názvem `AzureStackStampInformation.json`. Tento soubor se nachází ve `C:\CloudDeployment\logs` složce virtuálního počítače pro nasazení. Pokud si nejste jistí, jaké hodnoty byly použity pro nasazení Azure Stack, můžete získat hodnoty z tohoto místa.

Pokud virtuální počítač pro nasazení už není dostupný nebo není dostupný, můžete hodnoty získat připojením k privilegovanému koncovému bodu a spuštěním `Get-AzureStackStampInformation` rutiny PowerShellu. Další informace najdete v tématu [privilegovaný koncový bod](azure-stack-privileged-endpoint.md).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Nastavení podmíněného předávání na Azure Stack

Nejjednodušší a nejbezpečnější způsob, jak integrovat Azure Stack s infrastrukturou DNS, je provést podmíněné přesměrování zóny ze serveru, který hostuje nadřazenou zónu. Tento přístup se doporučuje, pokud máte přímou kontrolu nad servery DNS, které hostují nadřazenou zónu pro Azure Stack externí obor názvů DNS.

Pokud nejste obeznámeni s tím, jak provést podmíněné přesměrování pomocí služby DNS, přečtěte si následující článek na webu TechNet: [Přiřaďte pro název domény podmíněný překlad](https://technet.microsoft.com/library/cc794735)nebo dokumentaci specifickou pro vaše řešení DNS.

V případech, kdy jste určili svou externí Azure Stack zónu DNS, aby vypadala jako podřízená doména názvu vaší podnikové domény, nelze použít podmíněné přesměrování. Je třeba nakonfigurovat delegování DNS.

Příklad:

- Název domény DNS společnosti:`contoso.com`
- Azure Stack název externí domény DNS:`azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegování externí zóny DNS na Azure Stack

Aby bylo možné přeložit názvy DNS z vnějšku nasazení Azure Stack, musíte nastavit delegování DNS.

Každý registrátor má vlastní nástroje pro správu DNS, které umožňují měnit záznamy názvových serverů pro doménu. Na stránce správy DNS registrátora upravte záznamy NS a nahraďte záznamy NS pro zónu těmi, které jsou v Azure Stack.

Většina registrátorů DNS vyžaduje, abyste k dokončení delegování zadali aspoň dva servery DNS.

## <a name="next-steps"></a>Další postup

[Integrace brány firewall](azure-stack-firewall.md)
