---
title: Co je služba Azure Kubernetes v Azure Stack HCI?
description: Služba Azure Kubernetes v Azure Stack HCI je místní implementace služby Azure Kubernetes (AKS), která automatizuje spouštění kontejnerových aplikací ve velkém měřítku.
ms.topic: overview
author: jasongerend
ms.author: jgerend
ms.date: 09/22/2020
ms.openlocfilehash: fad630d143fee31c4b63b4008c02bef697475155
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948983"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>Co je služba Azure Kubernetes v Azure Stack HCI?

Služba Azure Kubernetes v Azure Stack HCI je místní implementace služby Azure Kubernetes (AKS), která automatizuje spouštění kontejnerových aplikací ve velkém měřítku. Služba Azure Kubernetes je teď ve verzi Preview na Azure Stack HCI. díky tomu je rychlejší začít s hostováním kontejnerů pro Linux a Windows ve vašem datovém centru.

Pokud chcete začít používat službu Azure Kubernetes v místním prostředí, [Zaregistrujte si verzi Preview](https://aka.ms/AKS-HCI-Evaluate) (ve verzi Preview se neúčtují žádné náklady) a pak si přečtěte téma [nastavení služby Azure KUBERNETES v Azure Stack HCL](setup.md). Pokud chcete místo toho použít službu Azure Kubernetes k orchestraci cloudových kontejnerů, přečtěte si téma [Azure Kubernetes Service v Azure](/azure/aks/intro-kubernetes).

V následujících částech jsou některé důvody, proč použít službu Azure Kubernetes na Azure Stack HCI, a pak zodpovědět některé běžné dotazy týkající se služby a jak začít. Základní informace o kontejnerech najdete v tématu [Windows a kontejnery](/virtualization/windowscontainers/about/).

## <a name="automate-management-of-containerized-applications"></a>Automatizovaná správa kontejnerových aplikací

I když můžete spravovat několik kontejnerů ručně pomocí Docker a Windows, aplikace často využívají pět, deset nebo dokonce stovky kontejnerů, kde Kubernetes Orchestrator přichází.

Kubernetes je open source Orchestrator pro automatizaci správy kontejnerů ve velkém měřítku. Služba Azure Kubernetes zjednodušuje nasazení v místním Kubernetes tím, že poskytuje průvodce pro nastavení Kubernetes a základních doplňků v Azure Stack HCL a vytváření clusterů Kubernetes pro hostování vašich úloh.

Tady je několik funkcí poskytovaných službou Azure Kubernetes ve verzi Preview na Azure Stack HCL:

- Nasaďte škálovatelné aplikace do clusteru virtuálních počítačů (nazývaných cluster Kubernetes), které běží v clusteru Azure Stack HCI.
- Převzetí služeb při selhání v případě selhání uzlu v clusteru Kubernetes
- Nasazení a Správa kontejnerových aplikací založených na systému Linux i Windows
- Plánování úloh
- Monitorování stavu
- Horizontální navýšení a snížení kapacity přidáním nebo odebráním uzlů do clusteru Kubernetes
- Správa sítí
- Vyhledat služby
- Koordinace upgradů aplikací
- Přiřazení lusků uzlům clusteru s spřažením uzlu clusteru

Další informace o Kubernetes najdete v tématu [Kubernetes.IO](https://kubernetes.io).

## <a name="simplify-setting-up-kubernetes"></a>Zjednodušení nastavení Kubernetes

Služba Azure Kubernetes zjednodušuje proces nastavení Kubernetes na Azure Stack HCI a obsahuje následující funkce:

- Průvodce centrem pro správu systému Windows pro nastavení Kubernetes a jeho závislostí (například kubeadm, kubelet, kubectl a v případě síťového doplňku)
- Průvodce centrem pro správu systému Windows pro vytváření clusterů Kubernetes pro spouštění vašich kontejnerových aplikací
- Rutiny PowerShellu pro nastavení Kubernetes a vytváření clusterů Kubernetes pro případ, že byste chtěli skriptovat nastavení hostitele a vytváření clusteru Kubernetes

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>Umožňuje zobrazit a spravovat Kubernetes pomocí místních nástrojů nebo ARC Azure.

Jakmile nastavíte službu Azure Kubernetes v clusteru Azure Stack HCI a vytvoříte cluster Kubernetes, nabídneme několik způsobů, jak spravovat a monitorovat infrastrukturu Kubernetes:

- **Místně pomocí oblíbených nástrojů, jako je Kubectl a Kubernetes** , můžete pomocí Open source webového rozhraní nasadit aplikace do clusteru Kubernetes, spravovat prostředky clusteru, řešit problémy a zobrazovat běžící aplikace.
- **V Azure Portal pomocí ARC Azure** – pomocí služby Azure Spravujte službu Azure Kubernetes a clustery Kubernetes nasazené v cloudu i v místním prostředí. Pomocí ARC Azure můžete přidávat a odebírat clustery Kubernetes a také uzly do clusteru Kubernetes, měnit nastavení sítě a instalovat doplňky.
<br>Azure ARC také umožňuje spravovat clustery Kubernetes s dalšími službami Azure, včetně těchto:

  - Azure Monitor
  - Azure Policy
  - Řízení přístupu na základě rolí

## <a name="run-linux-and-windows-containers"></a>Spuštění kontejnerů Linux a Windows

Služba Azure Kubernetes plně podporuje kontejnery na platformě Linux i pro systém Windows. Když vytvoříte cluster Kubernetes v Azure Stack HCI, můžete zvolit, jestli se mají vytvořit fondy uzlů (skupiny identických virtuálních počítačů) pro spouštění kontejnerů Linux, kontejnerů Windows nebo obojího. 

Služba Azure Kubernetes vytváří virtuální počítače Linux a Windows, takže nemusíte spravovat přímo operační systémy Linux a Windows.

## <a name="secure-your-container-infrastructure"></a>Zabezpečení infrastruktury kontejneru

Služba Azure Kubernetes obsahuje řadu funkcí, které vám pomůžou zabezpečit infrastrukturu kontejneru:

- **Izolace na bázi hypervisoru pro pracovní uzly** – každý cluster Kubernetes běží na vlastní vyhrazené a izolované sadě virtuálních počítačů, takže klienti můžou sdílet stejnou fyzickou infrastrukturu.
- **Microsoft udržuje image pro systémy Linux a Windows pro pracovní uzly** – pracovní uzly používají systémy Linux a image virtuálních počítačů s Windows vytvořené Microsoftem, aby dodržovaly osvědčené postupy zabezpečení. Microsoft taky aktualizuje tyto image měsíčně o nejnovější aktualizace zabezpečení.

Zabezpečení je nepřetržitá oblast investic pro vydání služby Azure Kubernetes ve verzi Preview na Azure Stack HCL, takže si můžete zůstat vyladěné.

## <a name="where-can-i-run-azure-kubernetes-service"></a>Kde můžu spustit službu Azure Kubernetes?

Služba Azure Kubernetes je dostupná na následujících platformách:

- V cloudu Azure prostřednictvím [služby Azure Kubernetes v Azure](/azure/aks/intro-kubernetes)
- V místním prostředí prostřednictvím služby Azure Kubernetes na Azure Stack HCI (k čemu je tento článek vše)
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

Systém správy centra pro správu systému Windows má následující požadavky:

- Windows 10 (v současnosti nepodporujeme servery centra pro správu systému Windows.)
- 60 GB volného místa
- Registrováno v Azure
- Ve stejné doméně jako cluster s Azure Stack HCI

### <a name="on-the-azure-stack-hci-cluster-that-hosts-azure-kubernetes-service"></a>V Azure Stack clusteru HCI, který hostuje službu Azure Kubernetes

Cluster se systémem Azure Stack HCI, verze 20H2 nebo novější má následující požadavky:

- Maximálně čtyři servery v clusteru pro tuto verzi Preview
- 1 TB dostupné kapacity ve fondu úložiště pro službu Azure Kubernetes
- Aspoň 30 GB volné paměti pro provozování virtuálních počítačů služby Azure Kubernetes
- Všechny servery v clusteru musí používat pro tuto verzi Preview výběr oblasti a jazyka EN-US.

Obecné požadavky na Azure Stack HCI najdete v tématu [před nasazením Azure Stack HCI](../hci/deploy/before-you-start.md).

### <a name="the-network-configuration-for-azure-stack-hci"></a>Konfigurace sítě pro Azure Stack HCI

Síť připojená k virtuálním počítačům v clusteru Azure Stack HCI vyžaduje vyhrazený obor adres IPv4 DHCP, který je dostupný pro službu Azure Kubernetes a který je přístupný pro virtuální počítače v clusteru Azure Stack HCI.

V síti nemůžete používat značky VLAN pro službu Azure Kubernetes v Azure Stack HCI. Použijte přístupové (netagované) porty v síťových přepínačích pro síť, kterou používá Azure Stack HCI a virtuální počítače služby Azure Kubernetes.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít se službou Azure Kubernetes v Azure Stack HCI, přečtěte si následující články:

- [Kontrola požadavků](system-requirements.md)
- [Nastavení služby Azure Kubernetes v Azure Stack HCI](create-kubernetes-cluster.md)
