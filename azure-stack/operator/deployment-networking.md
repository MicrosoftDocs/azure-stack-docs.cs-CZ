---
title: Síťový provoz nasazení Azure Stack | Microsoft Docs
description: Tento článek popisuje, co můžete očekávat od Azure Stackch síťových procesů nasazení.
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
ms.date: 12/05/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 12/05/2019
ms.openlocfilehash: 3dc764bd94712dd95ae76358148a4cb0a533fb48
ms.sourcegitcommit: 8e5a4659d8669ccfe6cd75d03200ae09a16b5f8a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74898561"
---
# <a name="about-deployment-network-traffic"></a>O provozu sítě nasazení
Porozumění síťovému provozu během nasazení Azure Stack pomůže nasazení provést úspěšně. Tento článek vás provede průběhem provozu sítě během procesu nasazení, abyste věděli, co očekávat.

Na tomto obrázku jsou zobrazeny všechny komponenty a připojení, která se podílejí na procesu nasazení:

![Topologie sítě nasazení Azure Stack](media/deployment-networking/figure1.png)

> [!NOTE]
> Tento článek popisuje požadavky na připojené nasazení. Další informace o dalších metodách nasazení najdete v tématu [modely připojení nasazení Azure Stack](azure-stack-connection-models.md).

### <a name="the-deployment-vm"></a>Virtuální počítač nasazení
Řešení Azure Stack zahrnuje skupinu serverů, které se používají k hostování Azure Stack komponent a dalšího serveru s názvem hostitel životního cyklu hardwaru (HLH). Tento server slouží k nasazení a správě životního cyklu vašeho řešení a hostuje virtuální počítač nasazení (DVM) během nasazování.

Poskytovatelé řešení Azure Stack můžou zřídit další virtuální počítače pro správu. Před provedením jakýchkoli změn virtuálních počítačů pro správu od poskytovatele řešení potvrďte u poskytovatele řešení.

## <a name="deployment-requirements"></a>Požadavky na nasazení
Před zahájením nasazení existují některé minimální požadavky, které může váš výrobce OEM ověřit, aby bylo zajištěno úspěšné dokončení nasazení:

-   [Certifikáty](azure-stack-pki-certs.md)
-   [Předplatné Azure](azure-stack-validate-registration.md). Možná budete muset ověřit předplatné.
-   Přístup k internetu
-   DNS
-   NTP

> [!NOTE]
> Tento článek se zaměřuje na poslední tři požadavky. Další informace o prvních dvou najdete na odkazech uvedených výše.

## <a name="deployment-network-traffic"></a>Provoz sítě nasazení
DVM je nakonfigurovaný s IP adresou ze sítě řadiče pro správu základní desky a vyžaduje síťový přístup k Internetu. I když ne všechny síťové komponenty řadiče pro správu základní desky vyžadují externí směrování nebo přístup k Internetu, může to vyžadovat i některé součásti specifické pro výrobce OEM, které využívají IP adresy z této sítě.

Během nasazování se DVM ověřuje v Azure Active Directory (Azure AD) pomocí účtu Azure z vašeho předplatného. Aby to bylo možné, DVM vyžaduje přístup k Internetu na seznam [konkrétních portů a adres URL](azure-stack-integrate-endpoints.md). DVM bude používat server DNS pro přeposílání požadavků DNS, které provádějí interní komponenty, na externí adresy URL. Interní DNS před nasazením předává tyto požadavky na adresu DNS pro přeposílání, kterou poskytnete výrobci OEM. Totéž platí pro server NTP, ale spolehlivý časový server je nutný k udržení synchronizace konzistence a času pro všechny součásti Azure Stack.

Přístup k Internetu vyžadovaný DVM během nasazení je jenom odchozí, během nasazování se neprovádějí žádná příchozí volání. Pamatujte, že používá svoji IP adresu jako zdroj a že Azure Stack nepodporuje konfigurace proxy serveru. Proto je třeba pro přístup k Internetu zadat transparentní proxy server nebo překlad adres (NAT). Během nasazení budou některé interní součásti začít přistupovat k Internetu prostřednictvím externí sítě pomocí veřejných virtuálních IP adres. Po dokončení nasazení se veškerá komunikace mezi Azure a Azure Stack provede prostřednictvím externí sítě pomocí veřejných virtuálních IP adres.

Síťové konfigurace v přepínačích Azure Stack obsahují seznamy řízení přístupu (ACL), které omezují provoz mezi určitými síťovými zdroji a cíli. DVM je jediná součást s neomezeným přístupem. i HLH je omezená. Můžete požádat výrobce OEM o možnosti přizpůsobení a usnadnit tak správu a přístup z vašich sítí. Z důvodu těchto seznamů ACL je důležité se vyhnout změnám adres serverů DNS a NTP v době nasazení. Pokud to uděláte, budete muset znovu nakonfigurovat všechny přepínače pro řešení.

Po dokončení nasazení budou zadané adresy serveru DNS a NTP nadále používány součástmi systému prostřednictvím SDN pomocí externí sítě. Pokud třeba po dokončení nasazení zkontrolujete žádosti DNS, zdroj se změní z IP adresy DVM na veřejnou VIP.

## <a name="next-steps"></a>Další kroky
[Ověření registrace v Azure](azure-stack-validate-registration.md)
