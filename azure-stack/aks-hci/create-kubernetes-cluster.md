---
title: Rychlý Start k vytvoření clusteru Kubernetes pomocí centra pro správu Windows
description: Naučte se vytvořit cluster Kubernetes pomocí centra pro správu Windows.
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 7a9c1bf5d89348175b7ac25a149fe01f384964a2
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612399"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>Rychlý Start: Vytvoření clusteru Kubernetes na Azure Stack HCI pomocí centra pro správu Windows

> Platí pro: Azure Stack HCI, Windows Server 2019 Datacenter

Po nastavení hostitele služby Azure Kubernetes můžete vytvořit cluster Kubernetes pomocí centra pro správu systému Windows. Pokud chcete místo toho použít PowerShell, přečtěte si téma [Vytvoření clusteru s Kubernetes pomocí PowerShellu](create-kubernetes-cluster-powershell.md).

Než budete pokračovat v Průvodci vytvořením clusteru Kubernetes, ujistěte se, že jste [nastavili službu Azure Kubernetes](setup.md) , a zkontrolujte [požadavky na systém](system-requirements.md) , pokud jste to ještě neudělali. Průvodce vytvořením clusteru Kubernetes můžete kontaktovat prostřednictvím stránky [všechna připojení](#creating-a-kubernetes-cluster-from-the-all-connections-page) nebo pomocí [řídicího panelu hostitele služby Azure Kubernetes](#creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard).

## <a name="creating-a-kubernetes-cluster-from-the-all-connections-page"></a>Vytvoření clusteru Kubernetes ze stránky všechna připojení 

Existují dva způsoby, jak vytvořit cluster Kubernetes v centru pro správu systému Windows. Jednu z těchto možností je udělat na stránce **všechna připojení** . Postupujte podle těchto kroků a potom přejděte do části [Průvodce vytvořením clusteru Kubernetes](#the-kubernetes-cluster-create-wizard) : 

1. Pokud chcete začít vytvářet cluster Kubernetes v centru pro správu Windows, stiskněte na obrazovce brána tlačítko **Přidat** . 
2. Na panelu **Přidat nebo vytvořit prostředky** v části **cluster Kubernetes (Preview)** vyberte **vytvořit novou** a spusťte průvodce clusterem Kubernetes. V rámci verze Public Preview je tlačítko Přidat v části clustery Kubernetes nefunkční. Kdykoli během procesu vytváření clusteru Kubernetes můžete průvodce ukončit, ale Všimněte si, že se váš průběh neuloží. 


    ![Znázorňuje okno Přidat nebo vytvořit prostředky v centru pro správu systému Windows, které teď obsahuje novou dlaždici pro clustery Kubernetes.](.\media\create-kubernetes-cluster\add-connection.png)
  
## <a name="creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard"></a>Vytvoření clusteru Kubernetes z řídicího panelu hostitele služby Azure Kubernetes  

Cluster Kubernetes můžete vytvořit také prostřednictvím řídicího panelu hostitele služby Azure Kubernetes. Tento řídicí panel najdete v nástroji Azure Kubernetes Service, když jste připojeni k systému, který používá hostitele služby Azure Kubernetes. Postupujte podle těchto kroků a potom přejděte do části [Průvodce vytvořením clusteru Kubernetes](#the-kubernetes-cluster-create-wizard) : 

1. Připojte se k systému, ve kterém chcete vytvořit cluster Kubernetes, a přejděte k nástroji **Azure Kubernetes Service** . Tento systém by už měl mít nastavený hostitel služby Azure Kubernetes.
2. V záhlaví **clusteru Kubernetes** vyberte tlačítko **Přidat cluster** .

![Ukazuje řídicí panel nástroje Azure Kubernetes, který se zobrazí po nastavení hostitele služby Azure Kubernetes.](.\media\setup\dashboard.png)
  
## <a name="the-kubernetes-cluster-create-wizard"></a>Průvodce vytvořením clusteru Kubernetes
Dosáhli jste Průvodce vytvořením clusteru Kubernetes prostřednictvím stránky **všechna připojení** nebo pomocí nástroje Azure Kubernetes Service. Můžeme začít:  

1. Projděte si požadavky na systém, který bude hostovat cluster Kubernetes, a v centru pro správu systému Windows. Po dokončení vyberte **Další**. 
2. Na stránce **základy** nakonfigurujte informace o clusteru, jako je integrace ARC Azure, informace o hostiteli služby Azure Kubernetes a velikost fondu primárních uzlů. Pole Hostitel služby Azure Kubernetes vyžaduje plně kvalifikovaný název domény Azure Stack HCI nebo Windows Server 2019 Datacenter, který jste použili při procházení stránky [instalace](setup.md) . Je nutné, abyste dokončili instalaci hostitele pro tento systém prostřednictvím nástroje Azure Kubernetes Service. Ve verzi Public Preview není počet uzlů upravitelný a výchozí hodnota je 2, ale velikost uzlu se dá nakonfigurovat pro větší zatížení. Po dokončení vyberte **Další**.

 [![Popisuje stránku základy průvodce clusterem Kubernetes. ](.\media\create-kubernetes-cluster\basics.png)](.\media\create-kubernetes-cluster\basics.png#lightbox)
 
3. Další fondy uzlů můžete nakonfigurovat tak, aby se spouštěly vaše úlohy na stránce **fondy uzlů** . Během veřejné verze Preview můžete přidat až jeden fond uzlů systému Windows a jeden fond uzlů pro Linux (kromě fondu uzlů systému). Pokud chcete povolit integraci ARC Azure později v tomto průvodci, budete potřebovat alespoň jeden fond uzlů pro Linux, který není ve fondu primárních uzlů. Až budete hotovi, vyberte **Další**.
4. Na stránce **síť** zadejte konfiguraci sítě. Pokud vyberete Upřesnit, můžete přizpůsobit rozsah adres, který se používá při vytváření virtuální sítě pro uzly v clusteru. Pokud vyberete základy, vytvoří se virtuální sítě pro uzly v clusteru s výchozím rozsahem adres. Nastavení sítě (Nástroj pro vyrovnávání zatížení, síťové zásady a směrování aplikace HTTP) nelze změnit ve verzi Public Preview. Po dokončení vyberte **Další**.

    [![Znázorňuje stránku síť průvodce clusterem Kubernetes. ](.\media\create-kubernetes-cluster\networking.png)](\media\create-kubernetes-cluster\networking.png#lightbox)

5. Na stránce **integrace** Připojte svůj cluster s dalšími službami, jako je trvalé úložiště. Na této stránce je nutné nakonfigurovat trvalé úložiště. Ve verzi Public Preview bude trvalé umístění úložiště ve výchozím nastavení výchozím umístěním úložiště, které je vybrané během instalace hostitele. Toto pole není momentálně editovatelné. Až budete hotovi, vyberte **Další**.
6. Zkontrolujte svůj výběr na stránce **Kontrola a vytvoření** . Až budete spokojeni, vyberte **vytvořit** a zahajte nasazení. Průběh nasazení se zobrazí v horní části této stránky. 
7. Po dokončení nasazení se na stránce **Další kroky** zobrazí podrobnosti o tom, jak spravovat cluster. Tato stránka obsahuje také klíč SSH. Pokud jste se rozhodli zakázat integraci Azure ARC v předchozím kroku, některé informace a pokyny na této stránce nemusí být k dispozici nebo funkční.

> [!IMPORTANT] 
> Klíč SSH doporučujeme uložit do zabezpečeného a přístupného umístění.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili cluster Kubernetes. Pokud se chcete dozvědět víc o službě Azure Kubernetes na Azure Stack HCI a Projděte si, jak nasadit a spravovat aplikace pro Linux v AKS v systému Azure Stack HCI, pokračujte na následující kurz:

- [Kurz: nasazení aplikací pro Linux ve službě Azure Kubernetes v Azure Stack HCL](deploy-linux-application.md)
