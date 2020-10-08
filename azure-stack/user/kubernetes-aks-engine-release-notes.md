---
title: Poznámky k verzi modulu Azure Kubernetes Service (AKS) v centru Azure Stack
description: Seznamte se s kroky, které je třeba provést s aktualizací pro AKS Engine v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 09/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/28/2020
ms.openlocfilehash: b4c5a2f6af605cc390a990df874bceae691006d3
ms.sourcegitcommit: edb60c948b445537e9411d6261c6c78359b71d0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91810639"
---
# <a name="release-notes-for-the-aks-engine-on-azure-stack-hub"></a>Poznámky k verzi pro modul AKS v centru Azure Stack
::: moniker range=">=azs-2002"
*Platí pro 0.55.4 verze v modulu AKS.*

Tento článek popisuje obsah modulu Azure Kubernetes Service (AKS) v aktualizaci centra Azure Stack. Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější vydání AKS modulu zaměřeného na platformu centra Azure Stack. Všimněte si, že nemá v úmyslu zdokumentovat informace o vydání pro modul AKS pro globální Azure.

## <a name="update-planning"></a>Plánování aktualizací

Příkaz pro upgrade AKS Engine plně automatizuje proces upgradu vašeho clusteru, postará se o virtuální počítače (VM), sítě, úložiště, Kubernetes a úlohy orchestrace. Před použitím aktualizace nezapomeňte zkontrolovat informace poznámky k verzi.

### <a name="upgrade-considerations"></a>Pokyny k upgradu

-   Používáte správnou položku Marketplace, AKS Base Ubuntu 16,04-LTS image distribuce pro vaši verzi modulu AKS? Verze najdete v části stažení nového image a AKS Engine.

-   Používáte pro cílový cluster správnou specifikaci clusteru ( `apimodel.json` ) a skupinu prostředků? Po původním nasazení clusteru se tento soubor vygeneroval ve výstupním adresáři. Podívejte se na `deploy` parametry příkazu [nasazení clusteru Kubernetes](./azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster).

-   Používáte spolehlivý počítač ke spuštění modulu AKS a ze kterého provádíte operace upgradu?

-   Pokud aktualizujete provozní cluster s aktivními úlohami, můžete upgradovat, aniž by to ovlivnilo, za předpokladu, že je cluster za normální zátěží. Měli byste ale mít záložní cluster pro případ, že je potřeba přesměrovat uživatele do IT. Cluster zálohování se důrazně doporučuje.

-   Pokud je to možné, spusťte příkaz z virtuálního počítače v prostředí Azure Stack hub, abyste snížili počet směrování sítě a potenciální problémy s připojením.

-   Ujistěte se, že vaše předplatné má dostatečnou kvótu pro celý proces. Proces během procesu přiděluje nové virtuální počítače. Výsledný počet virtuálních počítačů by byl stejný jako původní, ale naplánovalo se vytvoření dalších virtuálních počítačů během procesu.

-   Nejsou plánovány žádné aktualizace systému ani naplánované úlohy.

-   Nastavte připravený upgrade na cluster, který je nakonfigurovaný se stejnými hodnotami jako v produkčním clusteru, a před tím, než to uděláte, otestujte v produkčním clusteru.

### <a name="use-the-upgrade-command"></a>Použití příkazu Upgrade

Budete muset použít příkaz AKS Engine `upgrade` , jak je popsáno v následujícím článku [upgradujte cluster Kubernetes na rozbočovači Azure Stack](./azure-stack-kubernetes-aks-engine-upgrade.md).

### <a name="upgrade-interruptions"></a>Aktualizace přerušení

Někdy neočekávané faktory přerušují upgrade clusteru. K přerušení může dojít v případě, že modul AKS hlásí chybu nebo něco nastane procesu provádění modulu AKS. Prověřte příčinu přerušení, vyřešte ji a znovu odešlete stejný příkaz upgradu, aby bylo možné pokračovat v procesu upgradu. Příkaz pro **upgrade** je idempotentní a po opětovném odeslání příkazu by měl pokračovat v upgradu clusteru. V normálním případě se přerušení zvyšují čas k dokončení aktualizace, ale neměla by mít vliv na jejich dokončení.

### <a name="estimated-upgrade-time"></a>Odhadovaná doba upgradu

Odhadovaná doba je od 12 do 15 minut na virtuální počítač v clusteru. Například cluster s 20 uzly může trvat přibližně pět (5) hodin, než se upgraduje.

## <a name="download-new-image-and-aks-engine"></a>Stáhnout nový Image a modul AKS

Stáhněte si nové verze AKS Base Ubuntu image a AKS Engine.

Jak je vysvětleno v dokumentaci pro modul AKS pro Azure Stack centra, nasazení clusteru Kubernetes vyžaduje dvě hlavní součásti:

-   Binární soubor AKS-Engine

-   AKS Base Ubuntu 16,04-LTS image distribuce

V této aktualizaci jsou k dispozici nové verze těchto verzí:

-   Operátor centra Azure Stack bude muset stáhnout nový Ubuntu základní image na webu razítko (AKS Base):

    -   Název: AKS Base Ubuntu 16,04-LTS image distribuce, září 2020 (2020.09.14)

    -   Verze: 2020.09.14

    -   Postupujte podle pokynů v následujícím článku a [přidejte požadavky modulu Azure Kubernetes Services (AKS) na tržiště centra Azure Stack](../operator/azure-stack-aks-engine.md) .

-   Správce clusteru Kubernetes bude muset stáhnout novou verzi AKS-Engine 0.51.0. Přečtěte si pokyny v následujícím článku, [nainstalujte modul AKS v systému Linux do centra Azure Stack](./azure-stack-kubernetes-aks-engine-deploy-linux.md). Můžete postupovat podle stejného procesu, který používáte k první instalaci clusteru. Tato aktualizace přepíše předchozí binární soubor. Pokud jste například použili skript get-akse.sh, postupujte podle kroků uvedených v této části [instalace do připojeného prostředí](./azure-stack-kubernetes-aks-engine-deploy-linux.md#install-in-a-connected-environment). Stejný postup platí v případě, že instalujete nástroj do systému Windows, článek [nainstaluje modul AKS ve Windows do centra Azure Stack](./azure-stack-kubernetes-aks-engine-deploy-windows.md).

## <a name="aks-engine-and-azure-stack-version-mapping"></a>Mapování AKS Engine a verze Azure Stack

| Verze centra Azure Stack | Verze stroje AKS |
| ----------------------------- | ------------------------ |
| 1910 | 0.43.0, 0.43.1 |
| 2002 | 0.48.0, 0.51.0 |
| 2005 | 0.48.0, 0.51.0, 0.55.0, 0.55.4 |

## <a name="kubernetes-version-upgrade-path-in-aks-engine-v0554"></a>Cesta upgradu verze Kubernetes v AKS Engine v 0.55.4

Aktuální verzi a verzi upgradu najdete v následující tabulce pro Azure Stack hub. Nepoužívejte příkaz AKS-Engine get-versions, protože příkaz zahrnuje i verze podporované v globálním Azure. Následující tabulka verze a upgrade se vztahuje na cluster AKS Engine v centru Azure Stack.

| Aktuální verze | Dostupný upgrade |
| ------------------------- | ----------------------- |
| 1.15.10 | 1.15.12 |
| 1.15.12, 1.16.8, 1.16.9 | 1.16.14 |
| 1.16.8, 1.16.9, 1.16.14 | 1.17.11 |

V souboru JSON modelu rozhraní API zadejte hodnoty verze a verze v `orchestratorProfile` části, například pokud plánujete nasadit Kubernetes 1.16.14, musí být nastavené tyto dvě hodnoty (viz příklad [kubernetes-azurestack.jsv](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json)):

```json  
    -   "orchestratorRelease": "1.16",
    -   "orchestratorVersion": "1.16.14"
```

## <a name="whats-new"></a>Co je nového

- Aktualizace VHD Azure Stack Linux na 2020.09.14 #[3828](https://github.com/Azure/aks-engine/pull/3828)
- Přidá podporu pro K8s v 1.17.11 v Azure Stack [#3702](https://github.com/Azure/aks-engine/pull/3702)
- Přidá podporu pro K8s v 1.16.14 v Azure Stack [#3704](https://github.com/Azure/aks-engine/pull/3704)
- Aktualizace VHD pro Linux na 2020.09.14 [#3750](https://github.com/Azure/aks-engine/pull/3750)
- Aktualizace VHD Windows do srpna [#3730](https://github.com/Azure/aks-engine/pull/3730)
- Upgraduje Kubernetes metriky – Server na v 0.3.7 [#3669](https://github.com/Azure/aks-engine/pull/3669)
- Provede upgrade verze Docker pro opravu potíží s rotací protokolu [#3693](https://github.com/Azure/aks-engine/pull/3693)
- Upgraduje CoreDNS na verzi v 1.7.0 [#3608](https://github.com/Azure/aks-engine/pull/3608)
- Použijte Moby balíčky 19.03. x [#3549](https://github.com/Azure/aks-engine/pull/3549)
- Opravuje [#3571](https://github.com/Azure/aks-engine/pull/3571) strategii aktualizací Azure-CNI.

## <a name="known-issues"></a>Známé problémy

-   Nasazení více Kubernetes služeb paralelně v jednom clusteru může vést k chybě v konfiguraci základního nástroje pro vyrovnávání zatížení. Nasazení jedné služby v čase, pokud je to možné.
-   Spuštění AKS get-versions vytvoří informace týkající se Azure a centra Azure Stack, ale neexistuje explicitní způsob, jak nerozlišuje, co odpovídá centru Azure Stack. Nepoužívejte tento příkaz k zjištění, jaké verze jsou k dispozici pro upgrade. Použijte referenční tabulku upgradu popsanou výše.
-   Vzhledem k tomu, že nástroj AKS je sdílená úložiště zdrojového kódu napříč Azure a centra Azure Stack. Prozkoumání velkého množství zpráv k vydání verze a žádostí o přijetí změn vám umožní se domnívat, že nástroj podporuje jiné verze Kubernetes a platformy operačního systému nad rámec výše uvedeného, ignorujte je a jako oficiální průvodce pro tuto aktualizaci použijte tabulku verze výše.

> [!NOTE]  
> Podpora kontejnerů Windows a Azure CNI je dostupná ve verzi Private Preview. Pokud se chcete připojit k verzi Preview, můžete požádat o přístup: [https://aka.ms/ash-k8s-win-azurecni](https://aka.ms/ash-k8s-win-azurecni)

## <a name="reference"></a>Referenční informace

Toto je kompletní sada poznámek k verzi pro Azure a centrum Azure Stack v kombinaci:

- https://github.com/Azure/aks-engine/releases/tag/v0.51.1
- https://github.com/Azure/aks-engine/releases/tag/v0.52.1
- https://github.com/Azure/aks-engine/releases/tag/v0.53.1
- https://github.com/Azure/aks-engine/releases/tag/v0.54.1
- https://github.com/Azure/aks-engine/releases/tag/v0.55.0
- https://github.com/Azure/aks-engine/releases/tag/v0.55.4

::: moniker-end
::: moniker range="<=azs-1910"
*Platí pro verzi 0.48.0 nebo starší modul AKS.*

Tento článek popisuje obsah modulu Azure Kubernetes Service (AKS) v aktualizaci centra Azure Stack. Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější vydání AKS modulu zaměřeného na platformu centra Azure Stack. Všimněte si, že nemá v úmyslu zdokumentovat informace o vydání pro modul AKS pro globální Azure.

## <a name="update-planning"></a>Plánování aktualizací

Příkaz pro upgrade AKS Engine plně automatizuje proces upgradu vašeho clusteru, postará se o virtuální počítače (VM), sítě, úložiště, Kubernetes a úlohy orchestrace. Před použitím aktualizace si přečtěte informace v tomto článku.

### <a name="upgrade-considerations"></a>Pokyny k upgradu

-   Používáte správnou položku Marketplace, AKS Base Ubuntu 16,04-LTS image distribuce pro vaši verzi modulu AKS? Verze v části najdete v tématu [stažení nové AKS základní image Ubuntu a verze modulu AKS](#download-new-image-and-aks-engine).

-   Používáte pro cílový cluster správnou specifikaci clusteru (apimodel.jszapnuto) a skupinu prostředků? Po původním nasazení clusteru se tento soubor vygeneroval ve výstupním adresáři. Viz parametry příkazu Deploy [nasadit cluster Kubernetes](./azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster).

-   Používáte spolehlivý počítač ke spuštění modulu AKS a ze kterého provádíte operace upgradu?

-   Pokud aktualizujete provozní cluster s aktivními úlohami, můžete upgradovat, aniž by to ovlivnilo, za předpokladu, že je cluster za normální zátěží. Měli byste ale mít záložní cluster pro případ, že je potřeba přesměrovat uživatele do IT.

-   Pokud je to možné, spusťte příkaz z virtuálního počítače v prostředí Azure Stack hub, abyste snížili počet směrování sítě a potenciální problémy s připojením.

-   Ujistěte se, že vaše předplatné má dostatečnou kvótu pro celý proces. Proces během procesu přiděluje nové virtuální počítače. Výsledný počet virtuálních počítačů by byl stejný jako původní, ale naplánovalo se vytvoření dalších virtuálních počítačů během procesu.

-   Nejsou plánovány žádné aktualizace systému ani naplánované úlohy.

-   Nastavte připravený upgrade na cluster, který je nakonfigurovaný se stejnými hodnotami jako v produkčním clusteru, a před tím, než to uděláte, otestujte v produkčním clusteru.

### <a name="use-the-upgrade-command"></a>Použití příkazu Upgrade

Budete muset použít příkaz "upgrade AKS Engine", jak je popsáno v následujícím článku [upgrade clusteru Kubernetes v centru Azure Stack](./azure-stack-kubernetes-aks-engine-upgrade.md).

### <a name="upgrade-interruptions"></a>Aktualizace přerušení

Někdy neočekávané faktory přerušují upgrade clusteru. K přerušení může dojít v případě, že modul AKS hlásí chybu nebo něco nastane procesu provádění modulu AKS. Prověřte příčinu přerušení, vyřešte ji a znovu odešlete stejný příkaz upgradu, aby bylo možné pokračovat v procesu upgradu. Příkaz pro **upgrade** je idempotentní a po opětovném odeslání příkazu by měl pokračovat v upgradu clusteru. V normálním případě se přerušení zvyšují čas k dokončení aktualizace, ale neměla by mít vliv na jejich dokončení.

### <a name="estimated-upgrade-time"></a>Odhadovaná doba upgradu

Odhadovaná doba je od 12 do 15 minut na virtuální počítač v clusteru. Například cluster s 20 uzly může trvat přibližně pět (5) hodin, než se upgraduje.

## <a name="download-new-image-and-aks-engine"></a>Stáhnout nový Image a modul AKS

Stáhněte si nové verze AKS Base Ubuntu image a AKS Engine.

Jak je vysvětleno v dokumentaci pro modul AKS pro Azure Stack centra, nasazení clusteru Kubernetes vyžaduje dvě hlavní součásti: 
- Binární soubor AKS-Engine
- AKS Base Ubuntu 16,04-LTS image distribuce

V této aktualizaci jsou k dispozici nové verze těchto verzí:

-   Operátor centra Azure Stack bude muset stáhnout novou Ubuntu bitovou kopii základní AKS:

    -   Název: `AKS Base Ubuntu 16.04-LTS Image Distro, March 2020`
    -   Znění `2020.03.19`
    -   Postupujte podle pokynů v následujícím článku a [přidejte požadavky modulu Azure Kubernetes Services (AKS) na tržiště centra Azure Stack](../operator/azure-stack-aks-engine.md) .

-   Správce clusteru Kubernetes bude muset stáhnout novou verzi AKS-Engine 0.48.0. Přečtěte si pokyny v tomto článku, [nainstalujte modul AKS v systému Linux do centra Azure Stack](./azure-stack-kubernetes-aks-engine-deploy-linux.md). Můžete postupovat podle stejného procesu, který používáte k první instalaci clusteru. Tato aktualizace přepíše předchozí binární soubor. Pokud jste například použili `get-akse.sh` skript, postupujte podle kroků uvedených v článku [instalace do připojeného prostředí](./azure-stack-kubernetes-aks-engine-deploy-linux.md#install-in-a-connected-environment). Stejný postup platí v případě, že instalujete nástroj do systému Windows, článek [nainstaluje modul AKS ve Windows do centra Azure Stack](./azure-stack-kubernetes-aks-engine-deploy-windows.md).

## <a name="kubernetes-version-upgrade-path"></a>Cesta upgradu verze Kubernetes

Aktuální verzi a verzi upgradu najdete v následující tabulce pro Azure Stack hub. Nepoužívejte příkaz AKS-Engine, `get-versions` protože příkaz obsahuje taky verze podporované v globálním Azure. Následující tabulka verze a upgrade se vztahuje na cluster AKS Engine v centru Azure Stack.

| **Aktuální verze** | **Dostupný upgrade** |
| --------------------| ----------------------|
| 1.14.7 | 1.15.10 |
| 1.14.8 | 1.15.10 |
| 1.15.4 | 1.15.10 |
| 1.15.5 | 1.15.10 |

## <a name="whats-new"></a>Co je nového 

-   Podpora pro Kubernetes verze 1.15.10 ([ \# 2834](https://github.com/Azure/aks-engine/issues/2834)). Při nasazování nového clusteru Nezapomeňte zadat v souboru JSON modelu rozhraní API (a.k.s. definiční soubor clusteru) číslo verze vydání a číslo dílčí verze. Můžete najít příklad: [kubernetes-azurestack.jsv](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json):

    - `"orchestratorRelease": "1.15`,

    - `"orchestratorVersion": "1.15.10"`

    > [!NOTE]  
    > Pokud se verze Kubernetes explicitně neposkytuje v souboru JSON modelu rozhraní API, použije se verze `1.15` ([ \# 2932](https://github.com/Azure/aks-engine/issues/2932)) a orchestratorVersion se nastaví na výchozí hodnotu ` 1.15.11` , což způsobí chybu během nasazování clusteru.

-   Pomocí AKS-Engine v 0.43.1 se výchozí nastavení frekvence pro poskytovatele cloudu provede jeho kontrolní smyčka a další úkoly nefungují dobře s Správce prostředků mezními hodnotami Azure Stack centra pro příchozí požadavky. Tato aktualizace mění výchozí hodnoty pro centrum Azure Stack, aby se snížilo zatížení Azure Stack centra Správce prostředků ([ \# 2861](https://github.com/Azure/aks-engine/issues/2861)).

-   Nový krok ověření v AKS-Engine bude mít za následek zastavení nebo zobrazení upozornění, pokud soubor JSON modelu rozhraní API obsahuje vlastnosti, které nepodporují Azure Stack hub ([ \# 2717](https://github.com/Azure/aks-engine/issues/2717)).

-   Při nové kontrole ověřování AKS modul ověří dostupnost verze základní image AKS, která je potřebná pro verzi AKS modulu ([ \# 2342](https://github.com/Azure/aks-engine/issues/2342)). K tomu dojde po analýze souboru modelu rozhraní API a před voláním Správce prostředků centra Azure Stack.

-   New AKS – možnost "--Control-Only" v příkazu "upgrade" umožňuje uživateli provést upgrade operací pouze na hlavní Virtual Machines ([ \# 2635](https://github.com/Azure/aks-engine/issues/2635)).

-   Aktualizace jádra Linux verze 4.15.0-1071-Azure pro Ubuntu 16,04-LTS. Podrobnosti najdete v části[Package: linux-image-4.15.0-1071-Azure (4.15.0-1071.76) \[ Security \] ](https://packages.ubuntu.com/xenial/linux-image-4.15.0-1071-azure).

-   Nové aktualizace hyperkube pro podporu Kubernetes verzí 1.14.8 a 1.15.10.

-   Aktualizujte kubectl tak, aby odpovídalo verzi Kubernetes pro cluster... Tato součást je k dispozici v hlavních uzlech clusteru Kubernetes, můžete ji spustit pomocí SSH do hlavní větve.

-   Aktualizace pro doplněk Azure Container monitor s nejnovější [verzí února 2020](https://github.com/microsoft/Docker-Provider/blob/ci_feature_prod/README.md) ([ \# 2850](https://github.com/Azure/aks-engine/issues/2850)).

-   Upgrade `coredns` verze na verzi v 1.6.6 ([ \# 2555](https://github.com/Azure/aks-engine/issues/2555)).

-   Upgradujte `etcd` na verzi 3.3.18 ([ \# 2462](https://github.com/Azure/aks-engine/issues/2462)).

-   Upgradujte `moby` na verzi 3.0.11 ([ \# 2887](https://github.com/Azure/aks-engine/issues/2887)).

-   S tímto modulem AKS vydaných verzí vycházejí závislosti z k tomu, `k8s.gcr.io` aby teď `Kubernetes MCR registry @ mcr.microsoft.com` při sestavování imagí ([ \# 2722](https://github.com/Azure/aks-engine/issues/2722)) používali úředník.

## <a name="known-issues"></a>Známé problémy

-  Nasazení více Kubernetes služeb paralelně v jednom clusteru může vést k chybě v konfiguraci základního nástroje pro vyrovnávání zatížení. Nasazení jedné služby v čase, pokud je to možné.

-  Spuštění AKS get-versions vytvoří informace týkající se Azure a centra Azure Stack, ale neexistuje explicitní způsob, jak nerozlišuje, co odpovídá centru Azure Stack. Nepoužívejte tento příkaz k zjištění, které verze dostupné pro upgrade. Použijte referenční tabulku upgradu popsanou výše.

-  Vzhledem k tomu, že nástroj AKS je sdílená úložiště zdrojového kódu napříč Azure a centra Azure Stack. Prozkoumání velkého množství zpráv k vydání verze a žádostí o přijetí změn vás bude domnívat, že nástroj podporuje jiné verze Kubernetes a platformy operačního systému nad rámec výše uvedené výše, ignorujte je a jako oficiální průvodce pro tuto aktualizaci použijte tabulku verzí výše.

## <a name="reference"></a>Referenční informace

Následuje seznam některých opravených chyb a také kompletní sada poznámek k verzi z 0.44.0 do 0.48.0. Všimněte si, že druhý seznam bude zahrnovat položky centra Azure a Azure Stack.

### <a name="bug-fixes"></a>Opravy chyb

-   `userAssignedIdentityId`v systému Windows `azure.json` chybí uvozovky ([ \# 2327](https://github.com/Azure/aks-engine/issues/2327))

-   Doplňky `update config` jsou jenom upgradované ([ \# 2282](https://github.com/Azure/aks-engine/issues/2282))

-   Časový limit pro získání IP adresy pro správu v uzlech Windows ([ \# 2284](https://github.com/Azure/aks-engine/issues/2284))

-   Přidat 1.0.28 soubor zip Azure CNI do Windows VHD ([ \# 2268](https://github.com/Azure/aks-engine/issues/2268))

-   Správné pořadí výchozích hodnot pro nastavení IPAddressCount ([ \# 2358](https://github.com/Azure/aks-engine/issues/2358))

-   Aktualizace pro použití jedné omsagent YAML pro všechny verze k8s, aby nedocházelo k ručním chybám a snadné údržbě ([ \# 2692](https://github.com/Azure/aks-engine/issues/2692))

### <a name="release-notes"></a>Zpráva k vydání verze

Toto je kompletní sada poznámek k verzi pro Azure a centrum Azure Stack v kombinaci.

-   https://github.com/Azure/aks-engine/releases/tag/v0.44.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.45.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.3
-   https://github.com/Azure/aks-engine/releases/tag/v0.47.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.48.0
::: moniker-end
## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .
