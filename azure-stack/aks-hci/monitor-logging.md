---
title: Monitorovat službu Azure Kubernetes v Azure Stack clusterech HCI
description: Monitorování a zobrazení dat protokolování pro službu Azure Kubernetes ve Azure Stack clusterech HCI
author: v-susbo
ms.topic: how-to
ms.date: 01/26/2021
ms.author: v-susbo
ms.reviewer: ''
ms.openlocfilehash: 7bed9a767728525ae1e40ce4f0e607cc6f034055
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116797"
---
# <a name="monitor-aks-on-azure-stack-hci-clusters"></a>Monitorování AKS v Azure Stack clusterech HCI

K dispozici jsou dva typy řešení monitorování a protokolování pro AKS na Azure Stack HCI. Tady je porovnání dvou řešení: 

| Řešení  | Konektivita Azure  | Podpora a služba  | Náklady | Nasazení |
| ------- |  ------------  | ---------  | --------------  | ---------------- |
| Azure Monitor | Vyžaduje připojení AKS v Azure Stack clusteru HCI k Azure pomocí ARC Azure pro Kubernetes. | Plně podporované a obsluhované Microsoftem | Vyžaduje registraci ke službě Azure Monitor. |  Použití ARC Azure pro [monitorování clusterů](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-overview) |
| Místní monitorování a protokolování | Nevyžaduje připojení Azure | Podporuje se jako open source software od Microsoftu (žádná smlouva o podpoře ani SLA), komunita a/nebo externí dodavatelé.  | Závislé na dodavateli | Řízené zákazníky, viz [monitorování clusterů pomocí místního monitorování](#use-on-premises-monitoring) |

Pokud chcete použít Azure Monitor s AKS v Azure Stack clusterech HCI, přečtěte si téma [přehled Azure monitor](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-overview). 

## <a name="use-on-premises-monitoring"></a>Použití místního monitorování

Monitorování stavu, výkonu a využití prostředků v uzlech řídicích rovin a zatížení v clusteru je klíčové při spouštění aplikací v produkčním prostředí. Informace o tom, jak nastavit řešení pro místní monitorování, najdete v tématu [instalace Prometheus a Grafana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring). Toto řešení monitorování zahrnuje následující dva nástroje: 

- **Prometheus** je sada nástrojů pro monitorování a upozorňování, kterou můžete použít k monitorování kontejnerových úloh. Prometheus pracuje s různými typy sběračů a agentů ke shromažďování metrik a jejich uložení v databázi, na které můžete zadávat dotazy a zobrazovat sestavy. 

- **Grafana** je nástroj, který slouží k zobrazení, dotazování a vizualizaci metrik na řídicích panelech Grafana. Je předem nakonfigurovaný tak, aby jako zdroj dat používal Prometheus. 

### <a name="install-monitoring-tools"></a>Nainstalovat nástroje pro monitorování

Pokud chcete ve svém prostředí rychle nastavit jednoduché řešení monitorování, spusťte skript [Setup-Monitoring.ps1](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#easy-steps-to-setup-monitoring-to-use-local-port-forward-to-access-grafana) PowerShellu. Tento skript zahrnuje všechny kroky konfigurace potřebné k rychlému zprovoznění a spouštění řešení monitorování. 

Pokud chcete do Grafana zahrnout kontroler příchozího přenosu dat, postupujte podle kroků pro [přístup k Grafana pomocí kontroleru příchozího](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Monitoring#detailed-steps-to-setup-monitoring-to-use-ingress-controller-to-access-grafana)přenosu dat. Kontroler příchozího přenosu dat je software, který poskytuje reverzní proxy, konfigurovatelné směrování provozu a ukončení TLS (Transport Layer Security) pro služby Kubernetes Services.

### <a name="on-premises-logging"></a>Místní protokolování

Protokolování je klíčové pro řešení potíží a diagnostiku. Řešení protokolování v AKS na Azure Stack HCI je založené na Elasticsearch, Fluent bitové a Kibana (EFK). Všechny tyto komponenty jsou nasazeny jako kontejnery: 

- Mikrobit je procesor protokolu a server pro přeposílání, který shromažďuje data a protokoly z různých zdrojů a pak je formátuje, sjednocuje a ukládá je v Elasticsearch. 
- Elasticsearch je distribuovaný vyhledávací a analytický stroj schopný centrálně ukládat protokoly pro rychlé vyhledávání a analýzu dat.  
- Kibana poskytuje interaktivní vizualizace na webovém řídicím panelu. Tento nástroj umožňuje zobrazení a dotazování protokolů uložených v Elasticsearch a jejich vizualizaci pomocí grafů a řídicích panelů.

Pokud chcete nastavit místní řešení protokolování, postupujte podle těchto kroků a [nastavte protokolování pro přístup k Kibana](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#easy-steps-to-setup-logging-to-use-local-port-forward-to-access-kibana). Tento článek obsahuje všechny součásti potřebné ke shromažďování, agregaci a dotazování protokolů kontejnerů napříč clusterem. 

Informace k pokročilým krokům konfigurace najdete v tématu [protokolování systému Windows](https://github.com/microsoft/AKS-HCI-Apps/tree/main/Logging#detailed-steps-to-setup-logging).

## <a name="next-steps"></a>Další kroky

- [Nasaďte aplikace pro Linux do clusteru Kubernetes](./deploy-linux-application.md).
- [Nasaďte aplikaci Windows serveru v clusteru Kubernetes](./deploy-windows-application.md).
- [Základní koncepty Kubernetes](kubernetes-concepts.md)
