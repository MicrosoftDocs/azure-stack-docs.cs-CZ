---
title: Co je Azure Kubernetes Service v Azure Stack HCI?
description: Služba Azure Kubernetes v Azure Stack HCI je místní implementace služby Azure Kubernetes (AKS), která automatizuje spouštění kontejnerových aplikací ve velkém měřítku.
ms.topic: overview
author: v-susbo
ms.author: v-susbo
ms.date: 12/02/2020
ms.openlocfilehash: c27e81380514f6b27605f00884d41943146059c7
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840027"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>Co je Azure Kubernetes Service v Azure Stack HCI?
> Platí pro: AKS on Azure Stack HCI, AKS runtime na Windows serveru 2019 Datacenter

Služba Azure Kubernetes v Azure Stack HCI je místní implementace služby Azure Kubernetes (AKS), která automatizuje spouštění kontejnerových aplikací ve velkém měřítku. Služba Azure Kubernetes je teď ve verzi Preview na Azure Stack HCI a Windows Server 2019 Datacenter. díky tomu je rychlejší začít s hostováním kontejnerů Linux a Windows ve vašem datovém centru.

Pokud chcete začít používat službu Azure Kubernetes v místním prostředí, [Zaregistrujte si verzi Preview](https://aka.ms/AKS-HCI-Evaluate) (ve verzi Preview se neúčtují žádné náklady) a pak [nastavte službu Azure Kubernetes Service na Azure Stack HCI](setup.md). Pokud chcete místo toho použít službu Azure Kubernetes k orchestraci cloudových kontejnerů, přečtěte si téma [Azure Kubernetes Service v Azure](/azure/aks/intro-kubernetes).

V následujících částech jsou některé důvody, proč použít službu Azure Kubernetes na Azure Stack HCI, a pak zodpovědět některé běžné dotazy týkající se služby a jak začít. Pro pozadí na kontejnerech, přečtěte si téma [Windows a kontejnery](/virtualization/windowscontainers/about/) a na pozadí v Kubernetes najdete v tématu [Kubernetes core koncepty](kubernetes-concepts.md) nebo [Kubernetes.IO](https://kubernetes.io).

## <a name="use-aks-to-automate-management-of-containerized-applications"></a>Použití AKS k automatizaci správy kontejnerových aplikací

I když můžete spravovat několik kontejnerů ručně pomocí Docker a Windows, aplikace často využívají pět, deset nebo dokonce stovky kontejnerů, kde Kubernetes Orchestrator přichází.

Kubernetes je open source Orchestrator pro automatizaci správy kontejnerů ve velkém měřítku. Služba Azure Kubernetes zjednodušuje nasazení v místním Kubernetes tím, že poskytuje průvodce pro nastavení Kubernetes a základních doplňků v Azure Stack HCL a vytváření clusterů Kubernetes pro hostování vašich úloh.

Tady je několik funkcí poskytovaných službou Azure Kubernetes ve verzi Preview na Azure Stack HCL:

- Nasaďte škálovatelné aplikace ve velkém měřítku, aby se Kubernetes clustery se systémem v Azure Stackém clusteru HCI.
- Nasazení a Správa kontejnerových aplikací založených na systému Linux i Windows
- Nasazení AKS na Azure Stack HCI pomocí centra pro správu Windows nebo PowerShellu
- Horizontální navýšení a snížení kapacity přidáním nebo odebráním uzlů do clusteru Kubernetes
- Správa úložiště a sítě v clusteru Kubernetes
- Poskytování automatických aktualizací pro nasazení Kubernetes
- Upgradovat na nejnovější dostupnou verzi Kubernetes
- Používání oblíbených služeb Azure prostřednictvím ARC Azure pro Kubernetes

## <a name="simplify-setting-up-kubernetes"></a>Zjednodušení nastavení Kubernetes

Služba Azure Kubernetes zjednodušuje proces nastavení Kubernetes na Azure Stack HCI a Windows Server 2019 Datacenter a obsahuje následující funkce:

- Průvodce centrem pro správu systému Windows pro nastavení Kubernetes a jeho závislostí (například kubeadm, kubelet, kubectl a v případě síťového doplňku)
- Průvodce centrem pro správu systému Windows pro vytváření clusterů Kubernetes pro spouštění vašich kontejnerových aplikací
- Rutiny PowerShellu pro nastavení Kubernetes a vytváření clusterů Kubernetes pro případ, že byste chtěli skriptovat nastavení hostitele a vytváření clusteru Kubernetes

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Umožňuje zobrazit a spravovat Kubernetes pomocí místních nástrojů nebo ARC Azure.

Jakmile nastavíte službu Azure Kubernetes v místním prostředí a vytvoříte cluster Kubernetes, poskytujeme několik způsobů, jak spravovat a monitorovat infrastrukturu Kubernetes:

- **Místně pomocí oblíbených nástrojů, jako je Kubectl a Kubernetes** , můžete pomocí Open Source rozhraní nasadit aplikace do clusteru Kubernetes, spravovat prostředky clusteru, řešit problémy a zobrazovat běžící aplikace.
- **V Azure Portal pomocí ARC Azure** – pomocí ARC Azure můžete spravovat aplikace nasazené na clusterech Kubernetes napříč vaším cloudovým a místním prostředím. 
<br>Azure ARC také umožňuje spravovat clustery Kubernetes s dalšími službami Azure, včetně těchto:

  - Azure Monitor
  - Azure Policy
  - Řízení přístupu na základě rolí

## <a name="run-linux-and-windows-containers"></a>Spuštění kontejnerů Linux a Windows

Služba Azure Kubernetes plně podporuje kontejnery na platformě Linux i pro systém Windows. Když vytvoříte cluster Kubernetes v Azure Stack HCI, můžete zvolit, jestli se mají vytvořit fondy uzlů (skupiny stejných uzlů clusteru Kubernetes), aby se spouštěly kontejnery Linux, kontejnery Windows nebo obojí. 

Služba Azure Kubernetes vytváří uzly Linux a Windows, takže nemusíte spravovat přímo operační systémy Linux a Windows.

## <a name="secure-your-container-infrastructure"></a>Zabezpečení infrastruktury kontejneru

Služba Azure Kubernetes obsahuje řadu funkcí, které vám pomůžou zabezpečit infrastrukturu kontejneru:

- **Izolace na bázi hypervisoru pro pracovní uzly** – každý cluster Kubernetes běží na vlastní vyhrazené a izolované sadě virtuálních počítačů, takže klienti můžou sdílet stejnou fyzickou infrastrukturu.
- **Microsoft udržuje image pro systémy Linux a Windows pro pracovní uzly** – pracovní uzly používají systémy Linux a image virtuálních počítačů s Windows vytvořené Microsoftem, aby dodržovaly osvědčené postupy zabezpečení. Microsoft taky aktualizuje tyto image měsíčně o nejnovější aktualizace zabezpečení.

Zabezpečení je nepřetržitá oblast investic pro vydání služby Azure Kubernetes ve verzi Preview na Azure Stack HCL, takže si můžete zůstat vyladěné.

## <a name="where-can-i-run-azure-kubernetes-service"></a>Kde můžu spustit službu Azure Kubernetes?

Služba Azure Kubernetes je dostupná na následujících platformách:

- V cloudu Azure prostřednictvím [služby Azure Kubernetes v Azure](/azure/aks/intro-kubernetes)
- V místním prostředí prostřednictvím služby Azure Kubernetes na Azure Stack HCI (k čemu je tento článek vše)
- Místní na Windows serveru prostřednictvím modulu runtime služby Azure Kubernetes (Tento článek platí taky pro AKSr na Windows serveru).
- Místní v prostředí Azure Stack hub pomocí [modulu AKS v centru Azure Stack](../user/azure-stack-kubernetes-aks-engine-overview.md).

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>Jak Kubernetes pracuje na Azure Stack HCI?

Služba Azure Kubernetes funguje trochu odlišně při spuštění na Azure Stack HCI než při použití v cloudu Azure:

- Služba Kubernetes v Azure je hostovaná služba, ve které je pro vás spravovaná velká část infrastruktury správy Kubernetes (Řídicí rovina). Řídicí rovina i vaše kontejnerové aplikace běží na virtuálních počítačích Azure.
- Pomocí služby Azure Kubernetes Service v Azure Stack HCI jste službu nastavili přímo v clusteru Azure Stack HCI, takže zadáte kontrolu nad rovinou ovládacího prvku, abyste mohli mluvit. Plocha ovládacího prvku, vaše kontejnerové aplikace a služba Azure Kubernetes jsou všechny spouštěné ve virtuálních počítačích hostovaných vaším clusterem s vlastním sblížením.

Jakmile se služba Azure Kubernetes nastavila v clusteru Azure Stack HCI, funguje podobně jako hostovaná služba Azure Kubernetes: službu můžete používat k vytváření clusterů Kubernetes, které spouštějí vaše aplikace s vašimi kontejnery. Tyto clustery Kubernetes jsou skupiny virtuálních počítačů, které fungují jako pracovní uzly a spouštějí vaše kontejnery aplikací. Cluster Kubernetes obsahuje také rovinu ovládacího prvku, která se skládá ze systémových služeb Kubernetes používaných pro orchestraci kontejnerů aplikace.

Tady je několik zjednodušených diagramů, které ukazují, jak architektura služby Azure Kubernetes Compare při spuštění v Azure a v Azure Stack HCL.

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="Architektura služby Azure Kubernetes hostované v Azure, která ukazuje, jak jsou služby platformy a většina řídicí plochy spravované v Azure, zatímco clustery Kubernetes, které spouštějí vaše aplikace s vašimi aplikacemi, jsou spravované zákazníkem." lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="Architektura služby Azure Kubernetes na Azure Stack HCI, která ukazuje, jak všechno běží na Azure Stack clusteru HCI, včetně platformy Azure Kubernetes Service, roviny ovládacího prvku a Kubernetes clusterů, které spouštějí vaše aplikace s kontejnerem." lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>Co potřebujete, než začnete

V následujících částech najdete souhrn toho, co potřebujete ke spuštění služby Azure Kubernetes v Azure Stack HCL. Úplné podrobnosti najdete v tématu [před instalací služby Azure Kubernetes Service na Azure Stack HCI](system-requirements.md).

### <a name="on-your-windows-admin-center-system"></a>V systému centra pro správu systému Windows

Váš počítač, na kterém je spuštěná brána centra pro správu Windows, má následující požadavky:  

 - Registrováno v Azure
 - Ve stejné doméně jako cluster Azure Stack HCI nebo Windows Server 2019 Datacenter

### <a name="on-the-azure-stack-hci-cluster-or-windows-server-2019-datacenter-failover-cluster-that-hosts-azure-kubernetes-service"></a>V clusteru Azure Stack HCI nebo v clusteru s podporou převzetí služeb při selhání datacenter Windows serveru 2019, který hostuje službu Azure Kubernetes

Cluster Azure Stack HCI nebo cluster s podporou převzetí služeb při selhání datacenter Windows serveru 2019 má následující požadavky:

- Maximálně čtyři servery v clusteru pro tuto verzi Preview
- 1 TB dostupné kapacity ve fondu úložiště pro službu Azure Kubernetes
- Aspoň 30 GB volné paměti pro provozování virtuálních počítačů služby Azure Kubernetes
- Všechny servery v clusteru musí používat pro tuto verzi Preview výběr oblasti a jazyka EN-US.

Obecné požadavky na systém Azure Stack HCI najdete v článku o [požadavcích na systém Azure Stack HCI](../hci/concepts/system-requirements.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Konfigurace sítě pro Azure Stack HCI

Síť připojená k virtuálním počítačům v clusteru Azure Stack HCI nebo Windows Server 2019 Datacenter vyžaduje vyhrazený rozsah adres IPv4 dostupných pro službu Azure Kubernetes a dostupné pro virtuální počítače v clusteru Azure Stack HCI nebo Windows Server 2019 Datacenter. Další informace o požadavcích na síť najdete [v článku AKS on Azure Stack HCL – požadavky na systém](system-requirements.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít se službou Azure Kubernetes v Azure Stack HCI, přečtěte si následující články:

- [Kontrola požadavků](system-requirements.md)
- [Nastavení služby Azure Kubernetes v Azure Stack HCL pomocí prostředí PowerShell](setup-powershell.md)
- [Nastavení služby Azure Kubernetes v Azure Stack HCI pomocí centra pro správu Windows](create-kubernetes-cluster.md)

