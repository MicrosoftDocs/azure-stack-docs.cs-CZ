---
title: Plánování nasazení síťového adaptéru
description: Toto téma popisuje, jak naplánovat nasazení síťového adaptéru prostřednictvím centra pro správu Windows na sadu virtuálních počítačů s operačním systémem Azure Stack HCI.
author: AnirbanPaul
ms.author: anpaul
ms.topic: conceptual
ms.date: 10/7/2020
ms.openlocfilehash: 3ac17ca9cd54c2ec387ceb39cbda76f7cb539305
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255074"
---
# <a name="plan-to-deploy-network-controller"></a>Plánování nasazení síťového adaptéru

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Plánování nasazení síťového adaptéru prostřednictvím centra pro správu Windows vyžaduje sadu virtuálních počítačů, na kterých běží operační systém Azure Stack HCI. Síťový adaptér je vysoce dostupná a škálovatelná role serveru, která vyžaduje minimálně tři virtuální počítače pro zajištění vysoké dostupnosti ve vaší síti.

   >[!NOTE]
   > K nasazení síťového adaptéru doporučujeme použít vlastní vyhrazené virtuální počítače.

## <a name="network-controller-requirements"></a>Požadavky na síťový adaptér
K nasazení síťového adaptéru je potřeba následující:
- Virtuální pevný disk (VHD) pro Azure Stack operační systém HCI pro vytváření virtuálních počítačů síťového adaptéru.
- Název domény a přihlašovací údaje pro připojení virtuálních počítačů síťového adaptéru k doméně.
- Alespoň jeden virtuální přepínač, který nakonfigurujete pomocí Průvodce vytvořením clusteru v centru pro správu systému Windows.
- Konfigurace fyzické sítě, která odpovídá jedné z možností topologie v této části.

    Centrum pro správu Windows vytvoří konfiguraci v rámci hostitele Hyper-V. Síť pro správu se ale musí připojit k fyzickým adaptérům hostitele na základě jedné z následujících tří možností:

    **Možnost 1**: síť pro správu je fyzicky oddělená od sítí úloh. Tato možnost používá jeden virtuální přepínač pro výpočetní prostředky i pro úložiště:

    :::image type="content" source="./media/network-controller/topology-option-1.png" alt-text="Možnost 1 vytvořte fyzickou síť pro síťový adaptér." lightbox="./media/network-controller/topology-option-1.png":::

    **Možnost 2**: síť pro správu je fyzicky oddělená od sítí úloh. Tato možnost používá jediný virtuální přepínač jenom pro výpočetní výkon:

    :::image type="content" source="./media/network-controller/topology-option-2.png" alt-text="Možnost 2 pro vytvoření fyzické sítě pro síťový adaptér." lightbox="./media/network-controller/topology-option-2.png":::

    **Možnost 3**: síť pro správu je fyzicky oddělená od sítí úloh. Tato možnost používá dvě virtuální přepínače, jednu pro výpočetní prostředí a jednu pro úložiště:

    :::image type="content" source="./media/network-controller/topology-option-3.png" alt-text="Možnost 3 pro vytvoření fyzické sítě pro síťový adaptér." lightbox="./media/network-controller/topology-option-3.png":::

- Fyzické adaptéry pro správu můžete také seskupit tak, aby používaly stejný přepínač pro správu. V tomto případě se stále doporučuje použít jednu z možností v této části.
- Informace o síti pro správu, které síťový adaptér používá ke komunikaci s centrem pro správu systému Windows a hostiteli Hyper-V.
- Pro virtuální počítače síťového adaptéru buď adresování založené na protokolu DHCP, nebo statické síťové adresy.
- Plně kvalifikovaný název domény (representational state transfer) (FQDN) pro síťový adaptér, který používají klienti pro správu ke komunikaci se síťovým adaptérem.

   >[!NOTE]
   > Centrum pro správu systému Windows v současné době nepodporuje ověřování pomocí síťového adaptéru, a to buď pro komunikaci s klienty REST, nebo při komunikaci mezi virtuálními počítači síťového adaptéru. Pokud k nasazení a správě použijete PowerShell, můžete použít ověřování založené na protokolu Kerberos.

## <a name="configuration-requirements"></a>Požadavky na konfiguraci
Uzly clusteru síťového adaptéru můžete nasadit ve stejné podsíti nebo v různých podsítích. Pokud plánujete nasazení uzlů clusteru síťového adaptéru v různých podsítích, je nutné během procesu nasazení zadat název DNS síťového adaptéru REST.

Další informace najdete v tématu [Konfigurace dynamické registrace DNS pro síťový adaptér](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

## <a name="next-steps"></a>Další kroky
Teď jste připraveni nasadit síťový adaptér na virtuální počítače s operačním systémem.

Další informace najdete v následujících tématech:
- [Vytvoření clusteru Azure Stack HCI](../deploy/create-cluster.md)
- [Nasazení síťového adaptéru pomocí Windows PowerShellu](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts)

## <a name="see-also"></a>Viz také:
- [Síťový adaptér](/windows-server/networking/sdn/technologies/network-controller/network-controller)
- [Vysoká dostupnost síťového adaptéru](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability)