---
title: Koncepty – základy Kubernetes pro Azure Kubernetes Services (AKS) v Azure Stack HCL
description: Naučte se základní součásti clusteru a úloh Kubernetes a jejich vztah k funkcím ve službě Azure Kubernetes Service v Azure Stack HCI.
author: daschott
ms.author: daschott
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: e17e75631a6daac8c7523a2340dd69d5914a82de
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557068"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-on-azure-stack-hci"></a>Základní koncepty Kubernetes pro Azure Kubernetes Service v Azure Stack HCI

> Platí pro: AKS on Azure Stack HCI, AKS runtime na Windows serveru 2019 Datacenter

Služba Azure Kubernetes na Azure Stack HCI je platforma kontejneru Kubernetes na podnikové úrovni, která využívá Azure Stack HCI. Zahrnuje podporu pro základní Kubernetes, doplňky, účelově sestaveného hostitele kontejnerů Windows a hostitele kontejnerů Linux s podporou Microsoftu s cílem mít **jednoduché prostředí pro správu nasazení a životního cyklu**.

V tomto článku se seznámíte se základními komponentami infrastruktury Kubernetes, jako je například řídicí plocha, uzly a fondy uzlů. Společně s postupem seskupení prostředků do oborů názvů se zavádějí i prostředky úlohy, jako jsou lusky, nasazení a sady.

## <a name="kubernetes-cluster-architecture"></a>Architektura clusteru Kubernetes
Kubernetes je základní součástí služby Azure Kubernetes na Azure Stack HCL. Služba Azure Kubernetes v Azure Stack HCI používá sadu předdefinovaných konfigurací pro efektivní nasazení clusterů Kubernetes a jejich škálovatelnost.
 
Operace nasazení vytvoří více virtuálních počítačů se systémem Linux nebo Windows a spojí je dohromady, aby bylo možné vytvářet clustery Kubernetes. 
 
Nasazený systém je připraven přijmout standardní Kubernetes úlohy, škálovat tyto úlohy nebo dokonce škálovat počet virtuálních počítačů a také počet clusterů podle potřeby.

Cluster služby Azure Kubernetes je rozdělený na dvě hlavní komponenty na Azure Stack HCI:

- Cluster pro *správu* poskytuje základní mechanismus Orchestrace a rozhraní pro nasazení a správu jednoho nebo více cílových clusterů.
- *Cílové* clustery (označované také jako clustery úloh) jsou místo, kde běží aplikační úlohy a jsou spravovány clusterem pro správu.

![Znázorňuje technickou architekturu služby Azure Kubernetes na Azure Stack HCI.](.\media\concepts\architecture.png)

## <a name="management-cluster"></a>Cluster pro správu
Při vytváření clusteru služby Azure Kubernetes v Azure Stack HCI se automaticky vytvoří a nakonfiguruje cluster pro správu. Tento cluster pro správu zodpovídá za zřizování a správu cílových clusterů, ve kterých běží úlohy. Cluster pro správu zahrnuje následující základní komponenty Kubernetes:
- *Server API* – Server API je způsob zpřístupnění základních rozhraní Kubernetes API. Tato součást poskytuje interakci pro nástroje pro správu, jako je centrum pro správu Windows, moduly PowerShellu nebo `kubectl` .
- *Load Balancer* – Nástroj pro vyrovnávání zatížení je jedním VYHRAZENým virtuálním počítačem se systémem Linux s pravidlem vyrovnávání zatížení pro Server API clusteru pro správu.

### <a name="windows-admin-center"></a>Centrum pro správu systému Windows
Centrum pro správu systému Windows nabízí intuitivní uživatelské rozhraní pro operátor Kubernetes ke správě životního cyklu clusterů služby Azure Kubernetes na Azure Stack HCI.

### <a name="powershell-module"></a>Modul PowerShell
Modul PowerShell představuje snadný způsob, jak si stáhnout, nakonfigurovat a nasadit službu Azure Kubernetes Service na Azure Stack HCI. Modul PowerShell podporuje také nasazení a konfiguraci dalších cílových clusterů a také překonfigurování stávajících.

## <a name="target-cluster"></a>Cílový cluster
Cílový cluster (úlohy) je vysoce dostupným nasazením Kubernetes s využitím virtuálních počítačů se systémem Linux ke spouštění komponent roviny ovládacího prvku Kubernetes a také uzlů pro Linux Worker. Virtuální počítače založené na základu Windows serveru se používají pro vytváření pracovních uzlů Windows. Jeden nebo více cílových clusterů je spravovaných jedním clusterem pro správu.

### <a name="worker-nodes"></a>Pracovní uzly
Chcete-li spustit aplikace a podpůrné služby, potřebujete uzel Kubernetes. Cílový cluster služby Azure Kubernetes na Azure Stack HCI má minimálně jeden pracovní uzel, což je virtuální počítač, na kterém běží součásti uzlu Kubernetes, a taky hostování lusků a služeb, které tvoří úlohu aplikace.

### <a name="load-balancer"></a>Nástroj pro vyrovnávání zatížení
Nástroj pro vyrovnávání zatížení je virtuální počítač se systémem Linux a HAProxy +, který poskytuje služby Vyrovnávání zatížení pro cílové clustery nasazené clusterem pro správu.

Pro každý cílový cluster služba Azure Kubernetes v Azure Stack HCI přidá aspoň jeden virtuální počítač pro vyrovnávání zatížení (bez virtuální sítě). Kromě toho je možné vytvořit jiný nástroj pro vyrovnávání zatížení pro vysokou dostupnost serveru rozhraní API v cílovém clusteru. Jakákoli služba Kubernetes typu `LoadBalancer` , která je vytvořena v cílovém clusteru, ukončí vytvoření pravidla vyrovnávání zatížení v virtuálním počítači s jednou.

### <a name="add-on-components"></a>Add-On komponenty
V libovolném clusteru je několik volitelných doplňkových komponent, které je možné nasadit v jakémkoli clusteru, zejména: Arc Azure, Prometheus, Grafana nebo řídicí panel Kubernetes.

## <a name="kubernetes-components"></a>Součásti Kubernetes
V této části se seznámíte se základními komponentami pro Kubernetes úlohy, které se dají nasadit do služby Azure Kubernetes v Azure Stack cílových clusterech HCI, jako jsou lusky, nasazení a sady, společně s postupem seskupení prostředků do oborů názvů.

### <a name="pods"></a>podů

Kubernetes používá *lusky* ke spuštění instance aplikace. Pod představuje jednu instanci vaší aplikace. lusky mají typicky mapování 1:1 s kontejnerem, i když existují pokročilé scénáře, kde objekt pod může obsahovat více kontejnerů. Tyto lomené lusky jsou naplánované společně na stejném uzlu a umožňují kontejnerům sdílet související prostředky.

Když vytvoříte pod, můžete definovat *požadavky* na prostředky pro vyžádání určitého množství prostředků procesoru nebo paměti. Plánovač Kubernetes se pokusí naplánovat lusky, aby běžely na uzlu s dostupnými prostředky pro splnění požadavku. Můžete také zadat maximální omezení prostředků, které brání danému zařízení v využívání příliš velkého množství výpočetních prostředků ze základního uzlu. Osvědčeným postupem je zahrnout do všech lusků omezení prostředků, které pomůžou plánovači Kubernetes pochopit, které prostředky jsou potřeba a povolené.

Další informace najdete v tématu životní cyklus [Kubernetes lusky][kubernetes-pods] a [Kubernetes pod][kubernetes-pod-lifecycle].

Pod je logický prostředek, ale kontejnery jsou spuštěny úlohami aplikace. lusky jsou obvykle dočasné a nepoužívané prostředky a samostatně naplánované lusky neposkytují některé z funkcí vysoké dostupnosti a redundance Kubernetes. Místo toho jsou lusky nasazeny a spravovány Kubernetes *řadiči*, jako je například kontrolér nasazení.

### <a name="deployments-and-yaml-manifests"></a>Nasazení a manifesty YAML

*Nasazení* představuje jednu nebo více identické lusky spravované kontrolérem nasazení Kubernetes. Nasazení definuje počet *replik* (lusky), které se mají vytvořit, a Plánovač Kubernetes zajišťuje, aby v případě, že dojde k rozchodu nebo uzlů, bylo naplánováno další lusky na uzlech v pořádku.

Nasazení můžete aktualizovat, aby se změnila konfigurace lusků, použitého obrazu kontejneru nebo připojeného úložiště. Řadič nasazení vyprázdní a ukončí daný počet replik, vytvoří repliky z nové definice nasazení a pokračuje v procesu, dokud nebudou všechny repliky v nasazení aktualizovány.

Většina bezstavových aplikací by měla používat model nasazení místo plánování jednotlivých lusků. Kubernetes může monitorovat stav a stav nasazení, aby se zajistilo, že se v clusteru spustí požadovaný počet replik. Když naplánujete pouze jednotlivé lusky, lusky se nerestartují, pokud dojde k potížím a nedojde k jejich přeplánování na funkčních uzlech, pokud jejich aktuální uzel narazí na problém.

Pokud aplikace vyžaduje, aby byly kvora instancí vždy k dispozici pro rozhodování o správě, nechcete, aby proces aktualizace narušil tuto schopnost. *rozpočty přerušení pod* mohou být použity k definování toho, kolik replik v nasazení lze v průběhu aktualizace nebo upgradu uzlu považovat za nefunkční. Například pokud máte ve svém nasazení *pět (5)* replik, můžete definovat přerušení v případě *4* , aby bylo možné v jednom okamžiku odstranit nebo přeplánovat pouze jednu repliku. Stejně jako u omezení prostředků pod je osvědčeným postupem definování rozpočtů přerušení v aplikacích vyžadujících minimální počet replik, které mají být vždy k dispozici.

Nasazení se obvykle vytváří a spravují pomocí `kubectl create` nebo `kubectl apply` . Nasazení vytvoříte tak, že definujete soubor manifestu ve formátu YAML (YAML Ain't Markup Language). Následující příklad vytvoří základní nasazení webového serveru NGINX. Nasazení určuje *tři (3)* repliky, které mají být vytvořeny, a vyžaduje, aby byl na kontejneru otevřen port *80* . Pro procesor a paměť se definují taky požadavky na prostředky a omezení.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Složitější aplikace se taky dají vytvářet taky včetně služeb, jako jsou služby Vyrovnávání zatížení v manifestu YAML.

Další informace najdete v tématu [nasazení Kubernetes][kubernetes-deployments] .

##### <a name="mixed-os-deployments"></a>Nasazení se smíšeným operačním systémem

Pokud se daná služba Azure Kubernetes v Azure Stack cílový cluster HCI skládá z uzlů operačního systému Linux i Windows, musí být úlohy naplánované na operační systém, který může podporovat zřizování úlohy. Kubernetes nabízí dva mechanismy, kterými zajistíte, aby se úlohy na uzlech s cílovým operačním systémem nakládal:

- *Selektor uzlů* je jednoduché pole ve specifikaci pod, které stanoví omezení, aby se naplánovala pouze v zdravých uzlech, které odpovídají operačnímu systému. 
- Příchody *a tolerovány* vzájemně spolupracují, aby se zajistilo, že lusky nebudou naplánovány na uzly záměrně. Uzel může být "ve tvaru", aby nepřijímala lusky, které explicitně netolerovány svoji chuti pomocí "tolerovánosti" ve specifikaci pod.

Další informace najdete v tématech [Selektory uzlů][node-selectors] a [příchuti a jejich tolerovánosti][taints-tolerations].

### <a name="statefulsets-and-daemonsets"></a>StatefulSets a DaemonSets

Řadič nasazení používá Plánovač Kubernetes ke spuštění určitého počtu replik na jakémkoli dostupném uzlu s dostupnými prostředky. Tento přístup k použití nasazení může být pro bezstavové aplikace dostačující, ale ne pro aplikace, které vyžadují trvalou konvenci pojmenování nebo úložiště. Pro aplikace, které vyžadují, aby existovala replika na každém uzlu nebo vybrané uzly v rámci clusteru, kontroler nasazení se nedívá na to, jak jsou repliky distribuované napříč uzly.

Existují dva Kubernetes prostředky, které umožňují spravovat tyto typy aplikací:

- *StatefulSets* – Udržujte stav aplikací nad rámec individuálního životního cyklu, například úložiště.
- *DaemonSets* – zajistěte spuštěnou instanci na každém uzlu, a to nejdříve v procesu Bootstrap Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Moderní vývoj aplikací se často zaměřuje na bezstavové aplikace, ale *StatefulSets* se dají použít pro stavové aplikace, jako jsou například aplikace, které obsahují databázové součásti. StatefulSet se podobá nasazení v tom, že jedna nebo více identických lusků se vytváří a spravují. Repliky v StatefulSet sledují řádný a sekvenční přístup k nasazení, škálování, upgradům a ukončením. U StatefulSet (jako repliky se přejmenovává) konvence pojmenování, názvy sítí a uchování úložiště.

Aplikaci definujete ve formátu YAML pomocí `kind: StatefulSet` a kontroler StatefulSet pak zpracovává nasazení a správu požadovaných replik. Data se zapisují do trvalého úložiště s trvalým úložištěm, i když se StatefulSet odstraní.

Další informace najdete v tématu [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliky ve StatefulSet se naplánují a spouštějí napříč libovolným dostupným uzlem ve službě Azure Kubernetes v clusteru Azure Stack HCI. Pokud potřebujete zajistit, aby na uzlu běžela aspoň jedna pod v sadě, můžete místo toho použít DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Pro konkrétní účely shromažďování protokolů nebo monitorování možná budete muset spustit dané zařízení na všech nebo vybraných uzlech. *DaemonSet* se znovu používá k nasazení jedné nebo více identické lusky, ale kontroler DaemonSet zajišťuje, že každý zadaný uzel spustí instanci uzlu pod.

Řadič DaemonSet může naplánovat lusky na uzlech na začátku v procesu spouštění clusteru, než se spustí výchozí Plánovač Kubernetes. Tato možnost zajistí, že lusky ve DaemonSet začínají před tradičními lusky v nasazení nebo v StatefulSet.

Podobně jako StatefulSets je DaemonSet definován jako součást definice YAML pomocí `kind: DaemonSet` .

Další informace najdete v tématu [Kubernetes DaemonSets][kubernetes-daemonset].

### <a name="namespaces"></a>Obory názvů

Prostředky Kubernetes, například lusky a nasazení, jsou logicky seskupeny do *oboru názvů*. Tato seskupení poskytují způsob, jak logicky dělit službu Azure Kubernetes v Azure Stack cílový cluster HCI a omezit přístup k vytváření, zobrazování a správě prostředků. Můžete například vytvořit obory názvů pro oddělení obchodních skupin. Uživatelé můžou pracovat jenom s prostředky v rámci svých přiřazených oborů názvů.

Když v Azure Stack HCI vytvoříte cluster služby Azure Kubernetes, jsou k dispozici tyto obory názvů:

- *výchozí* – tento obor názvů je tam, kde jsou ve výchozím nastavení vytvářeny lusky a nasazení, když žádné není k dispozici. V menších prostředích můžete nasazovat aplikace přímo do výchozího oboru názvů, aniž byste museli vytvářet další logické separace. Při interakci s rozhraním API Kubernetes, jako je například s `kubectl get pods` , je použit výchozí obor názvů, pokud není zadán žádný.
- *Kube-System* – tento obor názvů je tam, kde existují základní prostředky, jako jsou například síťové funkce jako DNS a proxy nebo řídicí panel Kubernetes. Do tohoto oboru názvů obvykle nesadíte vlastní aplikace.
- *Kube – Public* – tento obor názvů se obvykle nepoužívá, ale dá se použít k zobrazení prostředků v celém clusteru a může ho zobrazit libovolný uživatel.

Další informace najdete v tématu [obory názvů Kubernetes][kubernetes-namespaces].

[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[node-selectors]: https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
[taints-tolerations]: https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/
