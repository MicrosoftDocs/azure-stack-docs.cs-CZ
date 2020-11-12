---
title: Partnerský vztah virtuální sítě v centru Azure Stack
description: Naučte se používat partnerský vztah virtuálních sítí k propojení virtuálních sítí v centru Azure Stack.
author: sethmanheim
ms.author: sethm
ms.date: 11/11/2020
ms.topic: conceptual
ms.reviewer: sranthar
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 16d7701161b8ec8c16aa3caecf5d5e291d6c0e99
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548663"
---
# <a name="virtual-network-peering"></a>Virtual Network partnerský vztah

Partnerské vztahy virtuálních sítí umožňují bezproblémové propojení virtuálních sítí v prostředí Azure Stack hub. Virtuální sítě se zobrazí jako jedna pro účely připojení. Provoz mezi virtuálními počítači používá základní infrastrukturu SDN. Podobně jako provoz mezi virtuálními počítači ve stejné síti je provoz směrován pouze přes privátní síť centra Azure Stack.

Centrum Azure Stack nepodporuje globální partnerský vztah, protože koncept "oblastí" neplatí.

Výhody použití partnerského vztahu virtuálních sítí jsou následující:

- Nízká latence a velká šířka pásma při propojení prostředků v různých virtuálních sítích.
- Schopnost prostředků v jedné virtuální síti komunikovat s prostředky v jiné virtuální síti.
- Možnost přenášet data mezi virtuálními sítěmi v různých předplatných a Azure Active Directorych klientech.
- Žádný výpadek prostředků ve virtuálních sítích při vytváření partnerského vztahu ani po jeho vytvoření.

Provoz mezi partnerskými virtuálními sítěmi je privátní. Provoz mezi virtuálními sítěmi je uložený ve vrstvě infrastruktury. V komunikaci mezi virtuálními sítěmi se nevyžaduje žádný veřejný Internet, brány ani šifrování.

## <a name="connectivity"></a>Připojení

U partnerských virtuálních sítí se prostředky v některé z virtuálních sítí můžou přímo připojit k prostředkům v partnerské virtuální síti.

Latence sítě mezi virtuálními počítači v partnerských virtuálních sítích ve stejné oblasti je stejná jako latence v rámci jedné virtuální sítě. Propustnost sítě závisí na šířce pásma, která je pro daný virtuální počítač povolená na základě jeho velikosti. S partnerským vztahem zde nejsou žádná další omezení týkající se šířky pásma.

Provoz mezi virtuálními počítači ve virtuálních sítích s navázaným partnerským vztahem je směrován přímo přes vrstvu SDN, nikoli prostřednictvím brány nebo přes veřejný Internet.

Skupiny zabezpečení sítě v obou virtuálních sítích můžete použít k blokování přístupu k ostatním virtuálním sítím nebo podsítím. Při konfiguraci partnerského vztahu virtuálních sítí buď otevřete nebo zavřete pravidla skupiny zabezpečení sítě mezi virtuálními sítěmi. Pokud otevřete úplné propojení mezi partnerskými virtuálními sítěmi, můžete použít skupiny zabezpečení sítě a zablokovat nebo odepřít konkrétní přístup. Výchozí možností je úplné připojení. Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení](/azure/virtual-network/security-overview).

## <a name="service-chaining"></a>Řetězení služeb

Řetězení služeb umožňuje směrovat provoz z jedné virtuální sítě do virtuálního zařízení nebo brány v partnerské síti prostřednictvím uživatelsky definovaných tras.

Pokud chcete povolit řetězení služeb, nakonfigurujte uživatelsky definované trasy, které odkazují na virtuální počítače v partnerských virtuálních sítích jako na IP adresu *dalšího segmentu směrování* .

Můžete nasadit sítě *rozbočovače a paprsků* , ve kterých virtuální síť rozbočovače hostuje součásti infrastruktury, jako je síťové virtuální zařízení nebo brána sítě VPN. Všechny uvedené virtuální sítě pak můžou vytvořit partnerský vztah s centrální virtuální sítí. Přenos toků přes síťová virtuální zařízení nebo brány sítě VPN ve virtuální síti rozbočovače.

Partnerský vztah virtuálních sítí umožňuje dalšímu směrování v uživatelsky definované trase být IP adresa virtuálního počítače v partnerské virtuální síti. Další informace o uživatelsky definovaných trasách najdete v [přehledu uživatelsky definovaných tras](/azure/virtual-network/virtual-networks-udr-overview#user-defined). Informace o vytvoření hvězdicové síťové topologie najdete v tématu [Síťová topologie centra s paprsky v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Brány a místní připojení

Každá virtuální síť, včetně partnerské virtuální sítě, může mít svou vlastní bránu. Virtuální síť může použít bránu pro připojení k místní síti. Přečtěte si [dokumentaci ke službě Virtual Network Gateway](/azure/vpn-gateway/).

Bránu můžete také nakonfigurovat ve virtuální síti s partnerským vztahem jako tranzitní bod pro místní síť. V takovém případě virtuální síť, která používá vzdálenou bránu, nemůže mít svou vlastní bránu. Virtuální síť má pouze jednu bránu. Brána je místní nebo vzdálená brána v partnerské virtuální síti, jak je znázorněno na následujícím obrázku:

:::image type="content" source="media/virtual-network-peering/virtual-network-gateway.png" alt-text="Topologie brány VPN Gateway":::

Všimněte si, že objekt **připojení** musí být vytvořený v bráně VPN ještě předtím, než povolíte možnosti **useremotegateways nastavený** v partnerském vztahu.

## <a name="virtual-network-peering-configuration"></a>Konfigurace partnerského vztahu virtuálních sítí

**Povolení přístupu k virtuální síti:** Povolení komunikace mezi virtuálními sítěmi umožňuje vzájemnou komunikaci prostředků připojených k libovolné virtuální síti se stejnou šířkou pásma a latencí, jako kdyby byly připojené ke stejné virtuální síti. Veškerá komunikace mezi prostředky ve dvou virtuálních sítích je směrována prostřednictvím interní vrstvy SDN.  

Jedním z důvodů, proč Nepovolit přístup k síti, může být scénář, ve kterém jste nastavili virtuální síť s jinou virtuální sítí, ale občas chcete zakázat tok přenosů mezi těmito dvěma virtuálními sítěmi. Povolení nebo zakázání je vhodnější než odstranění a opětovné vytváření partnerských vztahů. Pokud je toto nastavení zakázané, přenosy mezi partnerskými virtuálními sítěmi nesměrují.

**Povolení přesměrovaného provozu:** Toto políčko zaškrtněte, pokud chcete, aby byl provoz *předaný* síťovým virtuálním zařízením ve virtuální síti (který nepochází z virtuální sítě) k přenosu do této virtuální sítě prostřednictvím partnerského vztahu. Zvažte například tři virtuální sítě s názvem Spoke1, Spoke2 a hub. Partnerský vztah mezi každou virtuální sítí na paprsku a virtuální sítí rozbočovače existuje, ale partnerské vztahy mezi virtuálními sítěmi paprsků neexistují. Síťové virtuální zařízení je nasazené ve virtuální síti centrální sítě a uživatelsky definované trasy se aplikují na každou virtuální síť paprsků, která směruje provoz mezi podsítěmi přes síťové virtuální zařízení. Pokud toto políčko není zaškrtnuté pro partnerský vztah mezi jednotlivými virtuálními sítěmi a virtuální sítí rozbočovače, přenosy mezi virtuálními sítěmi paprsků nesměrují, protože rozbočovač nepřesměrovává přenos dat mezi virtuálními sítěmi. I když povolením této možnosti povolíte předaný přenos přes partnerský vztah, nevytvoří se žádné trasy definované uživatelem ani síťové virtuální zařízení. Uživatelsky definované trasy a síťová virtuální zařízení se vytvářejí samostatně. Přečtěte si informace o [trasách definovaných uživatelem](/azure/virtual-network/virtual-networks-udr-overview#user-defined). Toto nastavení nemusíte kontrolovat, pokud je přenos předáván mezi virtuálními sítěmi prostřednictvím VPN Gateway.

**Povolení přenosu brány:** Toto políčko zaškrtněte, pokud máte bránu virtuální sítě připojenou k této virtuální síti a chcete, aby provoz z partnerské virtuální sítě umožňoval tok přes bránu. Tato virtuální síť může být například připojená k místní síti přes bránu virtuální sítě. Zaškrtnutím tohoto políčka povolíte přenos z partnerské virtuální sítě do toku přes bránu připojenou k této virtuální síti do místní sítě. Pokud toto políčko zaškrtnete, virtuální síť s partnerským vztahem nemůže mít nakonfigurovanou bránu. Partnerská virtuální síť musí mít při nastavování partnerského vztahu z druhé virtuální sítě do této virtuální sítě zaškrtnuté políčko **použít vzdálenou bránu** . Pokud necháte toto políčko nezaškrtnuté (výchozí nastavení), provoz z partnerské virtuální sítě se pořád bude nacházet do této virtuální sítě, ale nemůže přesměrovat bránu virtuální sítě připojenou k této virtuální síti.

**Používejte vzdálené brány:** Zaškrtněte toto políčko, pokud chcete, aby provoz z této virtuální sítě procházel přes bránu virtuální sítě připojenou k virtuální síti, se kterou jste nacházeli s partnerským vztahem. Například virtuální síť, na kterou se připojujete, má připojenou bránu VPN, která umožňuje komunikaci s místní sítí. Zaškrtnutím tohoto políčka povolíte přenos z této virtuální sítě přes bránu VPN připojenou k partnerské virtuální síti. Pokud toto políčko zaškrtnete, musí mít připojená virtuální síť připojenou bránu virtuální sítě a musí mít zaškrtnuté políčko **povolený přenos brány** . Pokud necháte toto políčko nezaškrtnuté (výchozí nastavení), provoz z partnerské virtuální sítě může stále přesměrovat do této virtuální sítě, ale nemůže přesměrovat bránu virtuální sítě připojenou k této virtuální síti.

Vzdálené brány nemůžete použít, pokud už máte ve své virtuální síti nakonfigurovanou bránu.

### <a name="permissions"></a>Oprávnění

Ujistěte se prosím, že při vytváření partnerských vztahů s virtuální sítě v různých předplatných a tenantůch Azure AD mají účty přiřazenou roli **Přispěvatel** . Kromě toho neexistuje možnost uživatelského rozhraní pro partnerský vztah mezi různými klienty Azure AD. K vytvoření partnerských vztahů můžete použít Azure CLI a PowerShell.

## <a name="virtual-network-peering-frequently-asked-questions-faq"></a>Nejčastější dotazy týkající se partnerského vztahu virtuálních sítí

### <a name="what-is-virtual-network-peering"></a>Co je partnerský vztah virtuálních sítí?

Partnerský vztah virtuálních sítí umožňuje propojit virtuální sítě. Připojení peer-to-VNet mezi virtuálními sítěmi umožňuje směrovat provoz mezi nimi soukromě prostřednictvím adres IPv4. Virtuální počítače v partnerských virtuální sítě můžou vzájemně komunikovat, jako kdyby byly ve stejné síti. Připojení partnerských vztahů virtuální sítě je také možné vytvořit ve více předplatných.

### <a name="does-azure-stack-hub-support-global-vnet-peering"></a>Podporuje centrum Azure Stack globální partnerský vztah virtuální sítě?

Centrum Azure Stack nepodporuje globální partnerský vztah, protože koncept "oblastí" neplatí.

### <a name="on-which-azure-stack-hub-update-will-virtual-network-peering-be-available"></a>Na základě které aktualizace centra Azure Stack bude partnerský vztah virtuálních sítí dostupný?

partnerský vztah virtuální sítě je k dispozici v Azure Stack centru počínaje aktualizací 2008.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub-to-a-virtual-network-in-azure"></a>Můžu v Azure vytvořit partnerský vztah k virtuální síti ve službě Azure Stack hub?

Ne, partnerský vztah mezi Azure a Azure Stack hub není v tuto chvíli podporován.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub1-to-a-virtual-network-in-azure-stack-hub2"></a>Můžu v Azure Stack Hub1 k virtuální síti v Azure Stack hub2 vytvořit partnerský vztah k virtuální síti?

Ne, partnerský vztah se dá vytvořit jenom mezi virtuálními sítěmi v jednom systému Azure Stack hub. Další informace o tom, jak propojit dvě virtuální sítě s různými razítky, najdete [v tématu Vytvoření připojení typu VNet-to-VNet v centru Azure Stack](azure-stack-network-howto-vnet-to-vnet-stacks.md).

### <a name="can-i-enable-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Můžu povolit partnerský vztah, pokud moje virtuální sítě patří k předplatným v rámci různých klientů Azure Active Directory?

Yes. Je možné vytvořit partnerský vztah virtuální sítě, pokud vaše předplatná patří do různých klientů Azure Active Directory. Můžete to provést prostřednictvím PowerShellu nebo rozhraní příkazového řádku. Portál ještě není podporovaný.

### <a name="can-i-peer-my-virtual-network-with-a-virtual-network-in-a-different-subscription"></a>Můžu vytvořit partnerský vztah k virtuální síti s virtuální sítí v jiném předplatném?

Yes. Můžete vytvořit partnerský vztah virtuálních sítí v rámci předplatných.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Existují nějaká omezení šířky pásma pro připojení partnerských vztahů?

Ne. Partnerský vztah virtuálních sítí neomezuje žádná omezení šířky pásma. Šířka pásma je omezená jenom virtuálním počítačem nebo výpočetním prostředkem.

### <a name="my-virtual-network-peering-connection-is-in-an-initiated-state-why-cant-i-connect"></a>Moje připojení partnerského vztahu virtuální sítě je v *inicializovaném* stavu, proč se nemůžu připojit?

Pokud je připojení partnerského vztahu v **inicializovaném** stavu, znamená to, že jste vytvořili pouze jeden odkaz. Aby bylo možné vytvořit úspěšné připojení, je nutné vytvořit obousměrný odkaz. Například pro partnerský virtuální síť A k virtuální síti B se musí vytvořit odkaz z virtuální sítě A do virtuální sítě B a z virtuální sítě B na virtuální síť A. vytvoření obou odkazů změní stav na **připojeno**.

### <a name="my-virtual-network-peering-connection-is-in-a-disconnected-state-why-cant-i-create-a-peering-connection"></a>Moje připojení partnerského vztahu virtuální sítě je v *odpojeném* stavu, proč nemůžu vytvořit připojení partnerského vztahu?

Pokud je připojení partnerského vztahu virtuální sítě v **odpojeném** stavu, znamená to, že se odstranila jedna z vytvořených odkazů. Pokud chcete znovu vytvořit připojení partnerského vztahu, odstraňte odkaz a vytvořte ho znovu.

### <a name="is-virtual-network-peering-traffic-encrypted"></a>Je provoz partnerských vztahů virtuálních sítí zašifrovaný?

Ne. Provoz mezi prostředky v partnerských virtuálních sítích je privátní a izolovaný. Zůstane zcela v rámci vrstvy SDN systému Azure Stack hub.

### <a name="if-i-peer-vnet-a-to-vnet-b-and-i-peer-vnet-b-to-vnet-c-does-that-mean-vnet-a-and-vnet-c-are-peered"></a>Pokud I síť VNet peer-to-VNet B a I partnerské virtuální sítě B pro virtuální síť C, znamená to, že síť VNet a a virtuální síť C jsou partnerské?

Ne. Přenosných partnerských vztahů se nepodporuje. Musíte mít partnerský virtuální síť A a virtuální síť C.

## <a name="next-steps"></a>Další kroky

- [Virtuální síť Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)
- [Přehled uživatelsky definovaných tras](/azure/virtual-network/virtual-networks-udr-overview#user-defined)
- [Hvězdicová topologie sítě v Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
