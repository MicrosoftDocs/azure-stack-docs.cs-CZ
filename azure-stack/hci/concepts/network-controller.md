---
title: Plánování nasazení síťového adaptéru
description: Toto téma popisuje, jak naplánovat nasazení síťového adaptéru prostřednictvím centra pro správu Windows na sadu virtuálních počítačů s operačním systémem Azure Stack HCI.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 785665c9edc3af3230b4813e6da6bceddc43bd0a
ms.sourcegitcommit: b147d617c32cea138b5bd4bab568109282e44317
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90010828"
---
# <a name="plan-to-deploy-the-network-controller"></a>Plánování nasazení síťového adaptéru

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019 

Plánování nasazení síťového adaptéru prostřednictvím centra pro správu Windows vyžaduje sadu virtuálních počítačů, na kterých běží operační systém Azure Stack HCI. Síťový adaptér je vysoce dostupná a škálovatelná role serveru, která vyžaduje minimálně tři virtuální počítače pro zajištění vysoké dostupnosti ve vaší síti.

   >[!NOTE]
   > K nasazení síťového adaptéru doporučujeme použít vlastní vyhrazené virtuální počítače.

## <a name="network-controller-requirements"></a>Požadavky na síťový adaptér
K nasazení síťového adaptéru je potřeba následující:
- Virtuální pevný disk pro Azure Stack operační systém HCI pro vytvoření virtuálních počítačů síťového adaptéru.
- Název domény a přihlašovací údaje, které se připojí k virtuálním počítačům síťového adaptéru k doméně.
- Konfigurace fyzické sítě, která odpovídá jedné ze dvou možností topologie v této části.

    Centrum pro správu Windows vytvoří konfiguraci v rámci hostitele Hyper-V. Síť pro správu se ale musí připojit k fyzickým adaptérům hostitele na základě jedné z následujících dvou možností:

    **Možnost 1**: jeden fyzický přepínač připojí síť pro správu k fyzickému adaptéru pro správu na hostiteli a šachtu na fyzických adaptérech, které virtuální přepínač používá:

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="Možnost 1 vytvořte fyzickou síť pro síťový adaptér." lightbox="./media/network-controller/topology-option-1.png":::

    **Možnost 2**: Pokud je síť pro správu fyzicky oddělená od sítí zatížení, vyžadují se dva virtuální přepínače:

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Možnost 2 pro vytvoření fyzické sítě pro síťový adaptér." lightbox="./media/network-controller/topology-option-1.png":::

- Informace o síti pro správu, které síťový adaptér používá ke komunikaci s centrem pro správu systému Windows a hostiteli Hyper-V.
- Pro virtuální počítače síťového adaptéru buď adresování založené na protokolu DHCP, nebo statické síťové adresy.
- Plně kvalifikovaný název domény (representational state transfer) (FQDN) pro síťový adaptér, který používají klienti pro správu ke komunikaci se síťovým adaptérem.

   >[!NOTE]
   > Centrum pro správu systému Windows v současné době nepodporuje ověřování pomocí síťového adaptéru, a to buď pro komunikaci s klienty REST, nebo při komunikaci mezi virtuálními počítači síťového adaptéru. Pokud k nasazení a správě použijete PowerShell, můžete použít ověřování založené na protokolu Kerberos.

## <a name="configuration-requirements"></a>Požadavky na konfiguraci
Uzly clusteru síťového adaptéru můžete nasadit ve stejné podsíti nebo v různých podsítích. Pokud plánujete nasazení uzlů clusteru síťového adaptéru v různých podsítích, je nutné během procesu nasazení zadat název DNS síťového adaptéru REST.

Další informace najdete v tématu [Konfigurace dynamické registrace DNS pro síťový adaptér](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).


## <a name="next-steps"></a>Další kroky
Nyní jste připraveni k nasazení síťového adaptéru na virtuální počítače, na kterých běží operační systém Azure Stack HCI.

Další informace najdete v následujících tématech:
- [Vytvoření clusteru Azure Stack HCI](../deploy/create-cluster.md)

## <a name="see-also"></a>Viz také
- [Síťový adaptér](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [Vysoká dostupnost síťového adaptéru](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)