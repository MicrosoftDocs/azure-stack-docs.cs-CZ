---
title: Azure Stack Integrace brány firewall pro Azure Stack integrované systémy | Microsoft Docs
description: Přečtěte si o Azure Stack integraci brány firewall pro Azure Stack integrovaných systémech.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: e50f2cf722dc4a5b66dbc68c769127e346386134
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019313"
---
# <a name="azure-stack-firewall-integration"></a>Integrace brány Azure Stack firewall
Pro lepší zabezpečení Azure Stack doporučujeme použít zařízení brány firewall. Brány firewall můžou přispět k obraně proti akcím, jako jsou například distribuované útoky s cílem odepření služeb (DDOS), zjišťování vniknutí a kontrola obsahu. Můžou se ale taky stát kritickými body pro služby Azure Storage, jako jsou objekty blob, tabulky a fronty.

 Pokud se používá režim odpojeného nasazení, je nutné publikovat AD FS koncový bod. Další informace najdete v článku věnovaném [identitě pro integraci Datacenter](azure-stack-integrate-identity.md).

Koncové body pro Azure Resource Manager (správce), portál pro správu a Key Vault (správce) nemusí nutně vyžadovat externí publikování. Například jako poskytovatel služeb můžete omezit plochu pro útok tím, že pouze spravujete Azure Stack zevnitř vaší sítě, nikoli z Internetu.

V případě podnikových organizací může být externí síť stávající podnikovou sítí. V tomto scénáři je nutné publikovat koncové body pro provoz Azure Stack z podnikové sítě.

### <a name="network-address-translation"></a>Překlad síťových adres
Překlad adres (NAT) je doporučená metoda, která umožňuje virtuálnímu počítači pro nasazení (DVM) získat přístup k externím prostředkům a Internetu během nasazení a také k virtuálním počítačům ERCS (Emergency Recovery Console) nebo privilegovanému koncovému bodu (PEP) během registrace a odstraňování potíží.

Překlad adres (NAT) může být také alternativou k veřejným IP adresám na externí nebo veřejné VIP. To se ale nedoporučuje, protože to omezuje činnost koncového uživatele tenanta a zvyšuje složitost. Jednou z možností je jeden pro překlad adres (NAT), který stále vyžaduje jednu veřejnou IP adresu pro uživatele ve fondu. Další možností je celá řada pro překlad adres (NAT), která vyžaduje pravidlo překladu adres (NAT) na uživatelskou VIP pro všechny porty, které uživatel může použít.

K downsides používání protokolu NAT pro veřejné virtuální IP adresy patří:
- Překlad adres (NAT) zvyšuje režii při správě pravidel brány firewall, protože uživatelé řídí jejich vlastní koncové body a jejich vlastní pravidla publikování v zásobníku SDN (Software-Defined Networking). Uživatelé se musí spojit s operátorem Azure Stack, aby mohli své virtuální IP adresy publikovat a aktualizovat seznam portů.
- I když použití překladu adres (NAT) omezuje činnost koncového uživatele, poskytuje operátorovi úplnou kontrolu nad požadavky publikování.
- V případě hybridních cloudových scénářů s Azure zvažte, že Azure nepodporuje nastavování tunelu VPN pro koncový bod pomocí překladu adres (NAT).

### <a name="ssl-decryption"></a>Dešifrování SSL
V současné době se doporučuje zakázat dešifrování SSL u všech Azure Stackch přenosů. Pokud je podpora v budoucích aktualizacích podporovaná, poskytnou se pokyny, jak povolit dešifrování SSL pro Azure Stack.

## <a name="edge-firewall-scenario"></a>Scénář brány firewall na hraničních zařízeních
V nasazení Edge je Azure Stack nasazena přímo za hraničním směrovačem nebo bránou firewall. V těchto scénářích se podporuje, aby brána firewall byla nad hranicí (scénář 1), kde podporuje konfigurace brány firewall aktivní-aktivní i aktivní – pasivní, nebo funguje jako hraniční zařízení (scénář 2), kde podporuje jenom bránu firewall aktivní-aktivní. konfigurace se spoléhá na ECMP (EQUAL-cost multi-Path) s protokolem BGP nebo statickým směrováním pro převzetí služeb při selhání.

Veřejné IP adresy určené pro veřejný fond VIP z externí sítě v době nasazení. V hraničním scénáři se pro účely zabezpečení nedoporučuje používat veřejné IP adresy směrování v žádné jiné síti. Tento scénář umožňuje uživateli vyzkoušet si plně řízené cloudové prostředí jako ve veřejném cloudu, jako je Azure.  

![Příklad brány firewall Azure Stack Edge](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Scénář pro bránu firewall Enterprise intranet nebo hraniční sítě
V podnikovém intranetu nebo hraničním nasazení je Azure Stack nasazený v bráně firewall s více zónami nebo mezi hraniční bránou firewall a interní bránou firewall pro podnikovou síť. Provoz se pak distribuuje mezi zabezpečenou, hraniční sítí (nebo DMZ) a nezabezpečenými zónami, jak je popsáno níže:

- **Zabezpečená zóna**: Jedná se o interní síť, která používá interní nebo firemní IP adresy s směrováním. Zabezpečenou síť je možné rozdělit, mít internetový odchozí přístup prostřednictvím NAT v bráně firewall a jsou obvykle přístupné z libovolného místa v rámci datového centra prostřednictvím interní sítě. Všechny Azure Stack sítě by se měly nacházet v zabezpečené zóně s výjimkou veřejného fondu VIP externí sítě.
- **Hraniční zóna**. Hraniční síť je obvykle nasazení externích nebo internetových aplikací, jako jsou webové servery. Je obvykle monitorovaná branou firewall, aby se předešlo útokům, jako je DDoS a vniknutí (hacker), a zároveň umožňuje zadaný příchozí provoz z Internetu. V zóně DMZ by se měl umístit jenom fond virtuálních IP adres Azure Stack externí sítě.
- **Nezabezpečená zóna**. Toto je externí síť, Internet. Nedoporučuje **se** nasazovat Azure Stack v nezabezpečené zóně.

![Příklad Azure Stack hraniční sítě](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Víc se uč
Přečtěte si další informace o [portech a protokolech používaných Azure Stack koncovými body](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Další kroky
[Azure Stack požadavky PKI](azure-stack-pki-certs.md)

