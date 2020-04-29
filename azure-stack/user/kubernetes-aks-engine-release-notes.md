---
title: Poznámky k verzi modulu Azure Kubernetes Service (AKS) v centru Azure Stack
description: Seznamte se s kroky, které je třeba provést s aktualizací pro AKS Engine v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 4/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 4/23/2020
ms.openlocfilehash: 1e58943e4ff315d2b5c2a9370f623846e920443d
ms.sourcegitcommit: c51e7787e36c49d34ee86cabf9f823fb98b61026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82220723"
---
# <a name="release-notes-for-the-aks-engine-on-azure-stack-hub"></a>Poznámky k verzi pro modul AKS v centru Azure Stack

Tento článek popisuje obsah modulu Azure Kubernetes Service (AKS) v aktualizaci centra Azure Stack. Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější vydání AKS modulu zaměřeného na platformu centra Azure Stack. Všimněte si, že nemá v úmyslu zdokumentovat informace o vydání pro modul AKS pro globální Azure.

## <a name="update-planning"></a>Plánování aktualizací

Příkaz pro upgrade AKS Engine plně automatizuje proces upgradu vašeho clusteru, postará se o virtuální počítače (VM), sítě, úložiště, Kubernetes a úlohy orchestrace. Před použitím aktualizace si přečtěte informace v tomto článku.

### <a name="upgrade-considerations"></a>Pokyny k upgradu

-   Používáte správnou položku Marketplace, AKS Base Ubuntu 16,04-LTS image distribuce pro vaši verzi modulu AKS? Verze v části najdete v tématu [stažení nové AKS základní image Ubuntu a verze modulu AKS](#download-new-image-and-aks-engine).

-   Používáte pro cílový cluster správnou specifikaci clusteru (apimodel. JSON) a skupinu prostředků? Po původním nasazení clusteru se tento soubor vygeneroval ve výstupním adresáři. Viz parametry příkazu Deploy [nasadit cluster Kubernetes](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-cluster#deploy-a-kubernetes-cluster).

-   Používáte spolehlivý počítač ke spuštění modulu AKS a ze kterého provádíte operace upgradu?

-   Pokud aktualizujete provozní cluster s aktivními úlohami, můžete upgradovat, aniž by to ovlivnilo, za předpokladu, že je cluster za normální zátěží. Měli byste ale mít záložní cluster pro případ, že je potřeba přesměrovat uživatele do IT.

-   Pokud je to možné, spusťte příkaz z virtuálního počítače v prostředí Azure Stack hub, abyste snížili počet směrování sítě a potenciální problémy s připojením.

-   Ujistěte se, že vaše předplatné má dostatečnou kvótu pro celý proces. Proces během procesu přiděluje nové virtuální počítače. Výsledný počet virtuálních počítačů by byl stejný jako původní, ale naplánovalo se vytvoření dalších virtuálních počítačů během procesu.

-   Nejsou plánovány žádné aktualizace systému ani naplánované úlohy.

-   Nastavte připravený upgrade na cluster, který je nakonfigurovaný se stejnými hodnotami jako v produkčním clusteru, a před tím, než to uděláte, otestujte v produkčním clusteru.

### <a name="use-the-upgrade-command"></a>Použití příkazu Upgrade

Budete muset použít příkaz "upgrade AKS Engine", jak je popsáno v následujícím článku [upgrade clusteru Kubernetes v centru Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-upgrade).

### <a name="upgrade-interruptions"></a>Aktualizace přerušení

Někdy neočekávané faktory přerušují upgrade clusteru. K přerušení může dojít v případě, že modul AKS hlásí chybu nebo něco nastane procesu provádění modulu AKS. Prověřte příčinu přerušení, vyřešte ji a znovu odešlete stejný příkaz upgradu, aby bylo možné pokračovat v procesu upgradu. Příkaz pro **upgrade** je idempotentní a po opětovném odeslání příkazu by měl pokračovat v upgradu clusteru. V normálním případě se přerušení zvyšují čas k dokončení aktualizace, ale neměla by mít vliv na jejich dokončení.

### <a name="estimated-upgrade-time"></a>Odhadovaná doba upgradu

Odhadovaná doba je od 12 do 15 minut na virtuální počítač v clusteru. Například cluster s 20 uzly může trvat přibližně pět (5) hodin, než se upgraduje.

## <a name="download-new-image-and-aks-engine"></a>Stáhnout nový Image a modul AKS

Stáhněte si nové verze AKS Base Ubuntu image a AKS Engine.

Jak je vysvětleno v dokumentaci ke službě AKS Engine for Azure Stack hub, nasazení clusteru Kubernetes vyžaduje dvě hlavní součásti: 
- Binární soubor AKS-Engine
- AKS Base Ubuntu 16,04-LTS image distribuce

V této aktualizaci jsou k dispozici nové verze těchto verzí:

-   Operátor centra Azure Stack bude muset stáhnout novou Ubuntu bitovou kopii základní AKS:

    -   Jméno:`AKS Base Ubuntu 16.04-LTS Image Distro, March 2020`
    -   Znění`2020.03.19`
    -   Postupujte podle pokynů v následujícím článku a [přidejte požadavky modulu Azure Kubernetes Services (AKS) na tržiště centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-aks-engine) .

-   Správce clusteru Kubernetes bude muset stáhnout novou verzi AKS-Engine 0.48.0. Přečtěte si pokyny v tomto článku, [nainstalujte modul AKS v systému Linux do centra Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux). Můžete postupovat podle stejného procesu, který používáte k první instalaci clusteru. Tato aktualizace přepíše předchozí binární soubor. Pokud jste například použili `get-akse.sh` skript, postupujte podle kroků uvedených v článku [instalace do připojeného prostředí](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux#install-in-a-connected-environment). Stejný postup platí v případě, že instalujete nástroj do systému Windows, článek [nainstaluje modul AKS ve Windows do centra Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-windows).

## <a name="kubernetes-version-upgrade-path"></a>Cesta upgradu verze Kubernetes

Aktuální verzi a verzi upgradu najdete v následující tabulce pro Azure Stack hub. Nepoužívejte příkaz AKS-Engine `get-versions` , protože příkaz obsahuje taky verze podporované v globálním Azure. Následující tabulka verze a upgrade se vztahuje na cluster AKS Engine v centru Azure Stack.

| **Aktuální verze** | **Dostupný upgrade** |
| --------------------| ----------------------|
| 1.14.7 | 1.15.10 |
| 1.14.8 | 1.15.10 |
| 1.15.4 | 1.15.10 |
| 1.15.5 | 1.15.10 |

## <a name="whats-new"></a>Co je nového 

-   Podpora pro Kubernetes verze 1.15.10 ([\#2834](https://github.com/Azure/aks-engine/issues/2834)). Při nasazování nového clusteru Nezapomeňte zadat v souboru JSON modelu rozhraní API (a.k.s. definiční soubor clusteru) číslo verze vydání a číslo dílčí verze. Můžete najít příklad: [Kubernetes-azurestack. JSON](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json):

    - `"orchestratorRelease": "1.15`,

    - `"orchestratorVersion": "1.15.10"`

    > [!Note]  
    > Pokud se verze Kubernetes explicitně neposkytuje v souboru JSON modelu rozhraní API, použije se `1.15` verze ([\#2932](https://github.com/Azure/aks-engine/issues/2932)) a orchestratorVersion se nastaví na` 1.15.11`výchozí hodnotu, což způsobí chybu během nasazování clusteru.

-   Pomocí AKS-Engine v 0.43.1 se výchozí nastavení frekvence pro poskytovatele cloudu provede jeho kontrolní smyčka a další úkoly nefungují dobře s Správce prostředků mezními hodnotami Azure Stack centra pro příchozí požadavky. Tato aktualizace mění výchozí hodnoty pro centrum Azure Stack, aby se snížilo zatížení Azure Stack centra správce prostředků ([\#2861](https://github.com/Azure/aks-engine/issues/2861)).

-   Nový krok ověření v AKS-Engine bude mít za následek zastavení nebo zobrazení upozornění, pokud soubor JSON modelu rozhraní API obsahuje vlastnosti, které nepodporují Azure Stack hub ([\#2717](https://github.com/Azure/aks-engine/issues/2717)).

-   Při nové kontrole ověřování AKS modul ověří dostupnost verze základní image AKS, která je potřebná pro verzi AKS modulu ([\#2342](https://github.com/Azure/aks-engine/issues/2342)). K tomu dojde po analýze souboru modelu rozhraní API a před voláním Správce prostředků centra Azure Stack.

-   New AKS – možnost "--Control-Only" v příkazu "upgrade" umožňuje uživateli provést upgrade operací pouze na hlavní Virtual Machines ([\#2635](https://github.com/Azure/aks-engine/issues/2635)).

-   Aktualizace jádra Linux verze 4.15.0-1071-Azure pro Ubuntu 16,04-LTS. Podrobnosti najdete v části[Package: linux-image-4.15.0-1071-Azure (4.15.0- \[1071.76\]) Security](https://packages.ubuntu.com/xenial/linux-image-4.15.0-1071-azure).

-   Nové aktualizace hyperkube pro podporu Kubernetes verzí 1.14.8 a 1.15.10.

-   Aktualizujte kubectl tak, aby odpovídalo verzi Kubernetes pro cluster... Tato součást je k dispozici v hlavních uzlech clusteru Kubernetes, můžete ji spustit pomocí SSH do hlavní větve.

-   Aktualizace pro doplněk Azure Container monitor s nejnovější [verzí února 2020](https://github.com/microsoft/Docker-Provider/blob/ci_feature_prod/README.md) ([\#2850](https://github.com/Azure/aks-engine/issues/2850)).

-   Upgrade verze `coredns` na verzi v 1.6.6 ([\#2555](https://github.com/Azure/aks-engine/issues/2555)).

-   Upgradujte `etcd` na verzi 3.3.18 ([\#2462](https://github.com/Azure/aks-engine/issues/2462)).

-   Upgradujte `moby` na verzi 3.0.11 ([\#2887](https://github.com/Azure/aks-engine/issues/2887)).

-   S tímto modulem AKS vydaných `k8s.gcr.io` verzí vycházejí závislosti z `Kubernetes MCR registry @ mcr.microsoft.com` k tomu, aby teď při sestavování imagí ([\#2722](https://github.com/Azure/aks-engine/issues/2722)) používali úředník.

## <a name="known-issues"></a>Známé problémy

-  Nasazení více Kubernetes služeb paralelně v jednom clusteru může vést k chybě v konfiguraci základního nástroje pro vyrovnávání zatížení. Nasazení jedné služby v čase, pokud je to možné.

-  Spuštění AKS get-versions vytvoří informace týkající se Azure a centra Azure Stack, ale neexistuje explicitní způsob, jak nerozlišuje, co odpovídá centru Azure Stack. Nepoužívejte tento příkaz k zjištění, které verze dostupné pro upgrade. Použijte referenční tabulku upgradu popsanou výše.

-  Vzhledem k tomu, že nástroj AKS je sdílená úložiště zdrojového kódu napříč Azure a centra Azure Stack. Prozkoumání velkého množství zpráv k vydání verze a žádostí o přijetí změn vás bude domnívat, že nástroj podporuje jiné verze Kubernetes a platformy operačního systému nad rámec výše uvedené výše, ignorujte je a jako oficiální průvodce pro tuto aktualizaci použijte tabulku verzí výše.

## <a name="reference"></a>Referenční informace

Následuje seznam některých opravených chyb a také kompletní sada poznámek k verzi z 0.44.0 do 0.48.0. Všimněte si, že druhý seznam bude zahrnovat položky centra Azure a Azure Stack.

### <a name="bug-fixes"></a>Opravy chyb

-   `userAssignedIdentityId`v systému `azure.json` Windows chybí uvozovky ([\#2327](https://github.com/Azure/aks-engine/issues/2327))

-   Doplňky `update config` jsou jenom upgradované ([\#2282](https://github.com/Azure/aks-engine/issues/2282))

-   Časový limit pro získání IP adresy pro správu v uzlech Windows ([\#2284](https://github.com/Azure/aks-engine/issues/2284))

-   Přidat 1.0.28 soubor zip Azure CNI do Windows VHD ([\#2268](https://github.com/Azure/aks-engine/issues/2268))

-   Správné pořadí výchozích hodnot pro nastavení IPAddressCount ([\#2358](https://github.com/Azure/aks-engine/issues/2358))

-   Aktualizace pro použití jedné omsagent YAML pro všechny verze k8s, aby nedocházelo k ručním chybám a snadné údržbě ([\#2692](https://github.com/Azure/aks-engine/issues/2692))

### <a name="release-notes"></a>Poznámky k verzi

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

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .
