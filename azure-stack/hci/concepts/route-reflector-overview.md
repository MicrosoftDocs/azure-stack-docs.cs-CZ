---
title: Přehled reflektorů směrování protokolu BGP v Azure Stack HCI
description: V tomto tématu se dozvíte o odrážejících tras protokolu BGP pro softwarově definované sítě v Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: ca130c60e8cf08484001f606c4d0f84d786ca16b
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858885"
---
# <a name="what-is-route-reflector"></a>Co je reflektor směrování?

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

[Služba vzdáleného přístupu (RAS) obsahuje bránu protokolu BGP (Remote Access Service](gateway-overview.md) ), která je k dispozici jako alternativa k celé TOPOLOGII protokolu BGP, která je vyžadována pro synchronizaci směrování mezi směrovači. Border Gateway Protocol Reflektor trasy v nasazení softwarově definované sítě je logická entita, která je umístěná na rovině ovládacího prvku mezi bránami služby RAS a [síťovým adaptérem](network-controller-overview.md). Nejedná se však o zapojení do směrování roviny dat.

## <a name="how-route-reflector-works"></a>Jak funguje reflektor trasy

Při úplné synchronizaci sítí se všechny směrovače protokolu BGP musí připojit ke všem ostatním směrovačům v topologii směrování. Když ale použijete reflektor trasy, jedná se o jediného směrovače, který se připojuje ke všem ostatním směrovačům označovaným jako klienti odrážejících tras protokolu BGP, což zjednodušuje synchronizaci tras a snižuje síťový provoz. Reflektor trasy se učí všechny trasy, vypočítá nejlepší trasy a znovu distribuuje nejlepší trasy do svých klientů protokolu BGP.

Můžete nakonfigurovat, aby se tunely vzdáleného přístupu pro jednotlivé klienty ukončily na více než jednom virtuálním počítači brány RAS. To přináší zvýšenou flexibilitu pro poskytovatele cloudových služeb (CSP) v situacích, kdy jeden virtuální počítač brány RAS nemůže splňovat všechny požadavky na šířku pásma pro připojení klientů.

Tato možnost však zavádí další složitost správy směrování a efektivní synchronizaci tras mezi vzdálenými lokalitami klienta a jejich virtuálními prostředky v cloudovém datacentru. Poskytování klientů s připojením k více branám RAS taky přináší další složitost konfigurace na konci podniku, kde každá lokalita klienta bude mít samostatné sousední směrovače směrování.

Odrazce trasy protokolu BGP v řídící rovině řeší tyto problémy a zpřístupňuje nasazení interního prostředků infrastruktury CSP pro podnikové klienty.

- Když do datového centra přidáte nového tenanta, síťový adaptér automaticky nakonfiguruje první bránu RAS klienta jako reflektor trasy.

- Každý tenant má odpovídající reflektor směrování a nachází se na jednom z virtuálních počítačů brány RAS přidružených k tomuto tenantovi.

- Reflektor trasy klienta funguje jako reflektor trasy pro všechny virtuální počítače brány RAS, které jsou přidruženy k tenantovi. Mezi brány klienta, jiné než brány služby RAS, patří odrazní klienti trasy. Reflektor trasy provádí synchronizaci směrování mezi všemi klienty odrážejících tras, aby mohlo dojít ke skutečnému směrování cesty k datům.

- Reflektor trasy neposkytuje služby pro bránu RAS, na které je nakonfigurovaná.

- Reflektor trasy aktualizuje síťový adaptér s trasami organizace, které odpovídají podnikovým webům tenanta. To umožňuje síťovému adaptéru nakonfigurovat požadované zásady virtualizace sítě Hyper-V ve virtuální síti tenanta pro koncovou cestu k datům.

- Pokud vaši Podnikoví zákazníci používají směrování BGP v adresním prostoru zákazníka, je reflektor trasy brány RAS jediným externím sousedem protokolu BGP (eBGP) pro všechny lokality odpovídajícího tenanta. To platí bez ohledu na body ukončení tunelu podnikového klienta. Jinými slovy, bez ohledu na to, který virtuální počítač brány vzdáleného přístupu v datacentru CSP ukončí tunelové propojení VPN typu Site-to-site pro lokalitu tenanta, je eBGP partnerský uzel pro všechny klientské lokality.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Přehled služby RAS Gateway](gateway-overview.md)
- [Architektura nasazení brány služby RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Požadavek IETF (Internet Engineering Task Force) pro komentáře téma RFC 4456 trasa protokolu BGP: alternativa k internímu protokolu BGP celé sítě](https://tools.ietf.org/html/rfc4456)