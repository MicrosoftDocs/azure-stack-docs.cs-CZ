---
title: Rychlý Start k vytvoření clusteru Kubernetes pomocí centra pro správu Windows
description: Naučte se vytvořit cluster Kubernetes pomocí centra pro správu Windows.
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 7ef2f0e0532ee342e8821b362b16433f755bc072
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623264"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Rychlý Start: Vytvoření clusteru Kubernetes na Azure Stack HCI pomocí centra pro správu Windows

> Platí pro: Azure Stack HCI

Po nastavení hostitele služby Azure Kubernetes můžete vytvořit cluster Kubernetes pomocí centra pro správu systému Windows. Pokud chcete místo toho použít PowerShell, přečtěte si téma [Vytvoření clusteru s Kubernetes pomocí PowerShellu](create-kubernetes-cluster-powershell.md).

Můžeme začít:

1. [Nastavte službu Azure Kubernetes](setup.md) a ověřte požadavky na [systém](system-requirements.md) , pokud jste to ještě neudělali.
1. Pokud chcete začít vytvářet cluster Kubernetes v centru pro správu Windows, stiskněte na obrazovce brána tlačítko **Přidat** .
2. Na panelu **Přidat nebo vytvořit prostředky** v části **cluster Kubernetes (Preview)** vyberte **vytvořit novou** a spusťte průvodce clusterem Kubernetes. V rámci verze Public Preview je tlačítko Přidat v části clustery Kubernetes nefunkční. Kdykoli během procesu vytváření clusteru Kubernetes můžete průvodce ukončit, ale Všimněte si, že se váš průběh neuloží. 

    ![Popisuje okno Přidat nebo vytvořit prostředky v centru pro správu systému Windows, které teď zahrnuje novou dlaždici pro clustery Kubernetes.](.\media\create-kubernetes-cluster\add-connection.png)

3. Projděte si požadavky na systém, který bude hostovat cluster Kubernetes, a v centru pro správu systému Windows. Po dokončení vyberte **Další**. 
4. Na stránce **základy** nakonfigurujte informace o clusteru, jako je integrace ARC Azure, informace o hostiteli služby Azure Kubernetes a velikost fondu primárních uzlů.  Informace o hostiteli služby Azure Kubernetes a velikost fondu primárních uzlů. Pole Hostitel služby Azure Kubernetes vyžaduje plně kvalifikovaný název domény Azure Stackého clusteru HCI, do kterého chcete nasadit cluster Kubernetes. Je nutné, abyste dokončili instalaci hostitele pro tento systém prostřednictvím nástroje Azure Kubernetes Service. Ve verzi Public Preview není počet uzlů upravitelný a výchozí hodnota je 2, ale velikost uzlu se dá nakonfigurovat pro větší zatížení. Po dokončení vyberte **Další**.

    ![Popisuje stránku základy průvodce clusterem Kubernetes.](.\media\create-kubernetes-cluster\basics.png)

5. Další fondy uzlů můžete nakonfigurovat tak, aby se spouštěly vaše úlohy na stránce **fondy uzlů** . Během veřejné verze Preview můžete přidat až jeden fond uzlů systému Windows a jeden fond uzlů pro Linux (kromě fondu uzlů systému). Pokud chcete povolit integraci ARC Azure později v tomto průvodci, budete potřebovat alespoň jeden fond uzlů pro Linux, který není ve fondu primárních uzlů. Až budete hotovi, vyberte **Další**.
6. Na stránce **síť** zadejte konfiguraci sítě. Pokud vyberete Upřesnit, můžete přizpůsobit rozsah adres, který se používá při vytváření virtuální sítě pro uzly v clusteru. Pokud vyberete "základní", vytvoří se virtuální sítě pro uzly v clusteru s výchozím rozsahem adres. Nastavení sítě (Nástroj pro vyrovnávání zatížení, síťové zásady a směrování aplikace HTTP) nelze změnit ve verzi Public Preview. Po dokončení vyberte **Další**.

    ![Znázorňuje stránku síť průvodce clusterem Kubernetes.](.\media\create-kubernetes-cluster\networking.png)

7. Na stránce **integrace** Připojte svůj cluster s dalšími službami, jako je řídicí panel Kubernetes, trvalé úložiště a Azure monitor. Trvalé úložiště je jediná služba, která je vyžadována ke konfiguraci na této stránce. Ve verzi Public Preview bude trvalé umístění úložiště ve výchozím nastavení výchozím umístěním úložiště, které je vybrané během nastavování hostitele. Toto pole je v tuto chvíli neupravitelné. Až budete hotovi, vyberte **Další**.
8. Zkontrolujte svůj výběr na stránce **Kontrola a vytvoření** . Až budete spokojeni, vyberte **vytvořit** a zahajte nasazení. Průběh nasazení se zobrazí v horní části této stránky. 
9. Po dokončení nasazení se na stránce **Další kroky** zobrazí podrobnosti o tom, jak spravovat cluster. Tato stránka obsahuje taky svůj klíč SSH a token řídicího panelu Kubernetes. Pokud jste se rozhodli zakázat řídicí panel Kubernetes nebo integraci Azure ARC v předchozím kroku, některé informace a pokyny na této stránce nemusí být k dispozici nebo funkční.

> [!IMPORTANT] 
> Doporučujeme uložit klíč SSH a token řídicího panelu Kubernetes do zabezpečeného dostupného umístění.

V tomto rychlém startu nastavíte hostitele služby Azure Kubernetes a nasadíte cluster Kubernetes. 

Pokud chcete získat další informace o službě Azure Kubernetes na Azure Stack HCI a Projděte si, jak nasadit a spravovat aplikace pro Linux v Azure Kubernetes Service v Azure Stack HCI, pokračujte v tomto kurzu.
