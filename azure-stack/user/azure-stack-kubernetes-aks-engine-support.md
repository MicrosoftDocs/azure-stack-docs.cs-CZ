---
title: Zásady podpory pro modul AKS v Azure Stack | Microsoft Docs
description: Toto téma obsahuje zásady podpory pro modul AKS v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 1aa8aa65f0b19efcbafc85887e644c1c2b773bde
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310167"
---
# <a name="support-policies-for-aks-engine-on-azure-stack"></a>Zásady podpory pro modul AKS v Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek poskytuje podrobné informace o zásadách technické podpory a omezeních pro AKS modul na Azure Stack. Článek také podrobně popisuje Kubernetes položku Marketplace, komponenty Open-Source třetích stran a zabezpečení nebo správu oprav. 

## <a name="self-managed-kubernetes-clusters-on-azure-stack-with-aks-engine"></a>Samoobslužné Kubernetes clustery na Azure Stack s modulem AKS

Cloudové komponenty infrastruktury jako služby (IaaS), jako jsou výpočetní nebo síťové komponenty, poskytují uživatelům přístup k ovládacím prvkům nízké úrovně a možnostem přizpůsobení. AKS Engine umožňuje uživatelům laydown Kubernetes clustery, které využívají tyto IaaS komponenty transparentně. uživatelé mají přístup a mají vliv na všechny aspekty jejich nasazení.

Po vytvoření clusteru definuje zákazník hlavní Kubernetes a pracovní uzly, které vytvoří modul AKS. Na těchto uzlech se spouštějí úlohy zákazníka. Vlastníci a můžou zobrazit nebo upravit hlavní a pracovní uzly. Carelessly změněné uzly můžou způsobit ztráty dat a úloh a můžou vykreslovat nefunkční cluster. I operace AKS Engine, jako je například upgrade nebo škálování, přepíší všechny změny mimo hranice. Pokud například cluster obsahuje statické lusky, nebudou zachovány po operaci upgradu AKS Engine.

Vzhledem k tomu, že uzly clusterů zákazníka spouštějí soukromý kód a ukládají citlivá data, podpora Microsoftu k nim přistupovat jenom tak, že jsou jenom omezené. Podpora Microsoftu se nemůže přihlásit, spustit příkazy v nebo zobrazit protokoly pro tyto uzly bez výslovného svolení nebo asistence zákazníka.

## <a name="aks-engine-supported-areas"></a>Oblasti podporované modulem AKS

Společnost Microsoft poskytuje technickou podporu pro následující:

-  Problémy s příkazy modulu AKS: nasazení, generování, upgrade a škálování. Nástroj by měl být v souladu s chováním v Azure.
-  Problémy s clusterem Kubernetes nasazeným podle [přehledu modulu AKS](azure-stack-kubernetes-aks-engine-overview.md).
-  Problémy s připojením k jiným Azure Stack službám 
-  Problémy s připojením rozhraní API Kubernetes
-  Problémy s funkcemi poskytovatele Azure Stack Kubernetes a možností připojení Azure Resource Manager
-  Problémy s konfigurací Azure Stackch nativních artefaktů, jako jsou nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, virtuální sítě, podsítě, síťová rozhraní, směrovací tabulka, sady dostupnosti, veřejné IP adresy, účet úložiště a počítače virtuálních počítačů, vygenerovaná modulem AKS. 
-  Problémy se síťovým výkonem a latencí
-  Problémy se základní imagí AKS, kterou modul AKS používá v odpojených nasazeních. 

## <a name="aks-engine-areas-not-supported"></a>Oblasti AKS Engine se nepodporují.

Společnost Microsoft neposkytuje technickou podporu pro následující:

-  Použití modulu AKS v Azure.
-  Azure Stack Kubernetes položku Marketplace.
-  Pomocí následujících možností definice clusteru AKS Engine a doplňků.
    -  Nepodporované doplňky:  
            -Identity AAD pod  
            – Konektor ACI  
            – Blobfuse pružný svazek  
            – Automatické škálování clusteru  
            – Monitorování kontejnerů  
            – Svazek pro vývoj klíčů v trezoru  
            – Modul plug-in zařízení NVIDIA  
            -Rescheduleer  
            – Svazek Flex protokolu SMB  
        
    -  Nepodporované možnosti definice clusteru:  
            – Pod KubernetesConfig:  
                    - cloudControllerManagerConfig  
                    - enableDataEncryptionAtRest  
                    - enableEncryptionWithExternalKms  
                    - enablePodSecurityPolicy  
                    - etcdEncryptionKey  
                    - useInstanceMetadata  
                    - useManagedIdentity  
                    - azureCNIURLLinux  
                    - azureCNIURLWindows  
            – Pod profilech masterprofile:  
                    - availabilityZones  
            – Pod agentPoolProfiles:  
                    - availabilityZones  
                    – singlePlacementGroup  
                    - scaleSetPriority  
                    - scaleSetEvictionPolicy  
                    - acceleratedNetworkingEnabled  
                    - acceleratedNetworkingEnabledWindows

-  Změny konfigurace Kubernetes se zachovaly mimo etcd úložiště konfigurace Kubernetes. Například statické lusky běžící v uzlech clusteru.
-  Dotazy týkající se použití Kubernetes Podpora Microsoftu například neposkytuje Rady k vytváření vlastních řadičů příchozího přenosu dat, použití aplikačních úloh nebo k použití softwarových balíčků nebo nástrojů třetích stran nebo open source.
-  Projekty Open Source třetích stran, které nejsou poskytovány jako součást clusteru Kubernetes nasazeného modulem AKS Engine. Tyto projekty mohou zahrnovat Kubeadm, Kubespray, Native, Istio, Helm, zástupné nebo jiné.
-  Použití modulu AKS ve scénářích použití mimo ty, které jsou určené v [podporovaných scénářích s modulem AKS](azure-stack-kubernetes-aks-engine-overview.md#supported-scenarios-with-the-aks-engine).
-  Software třetí strany. Tento software může obsahovat nástroje pro kontrolu zabezpečení a síťová zařízení nebo software.
-  Problémy týkající se více než cloudových nebo více dodavatelů sestavení Microsoft například nepodporuje problémy související se spouštěním řešení pro Cloud ve federaci s více veřejnými cloudy.
-  Vlastní nastavení sítě, kromě těch, které jsou uvedeny v části [oblasti podporované modulem AKS](#aks-engine-supported-areas) .

##  <a name="security-issues-and-patching"></a>Problémy se zabezpečením a opravy

Pokud je chyba zabezpečení zjištěná v jedné nebo více součástech modulu AKS nebo poskytovatele Kubernetes pro Azure Stack, Microsoft zpřístupní zákazníkům opravu ovlivněných clusterů za účelem zmírnění problému. Případně tým poskytne pokyny k upgradu uživatelů. Všimněte si, že opravy mohou vyžadovat výpadky clusteru. Pokud se vyžaduje restartování, společnost Microsoft bude informovat zákazníky tohoto požadavku. Pokud uživatelé nepoužijí opravy podle pokynů Microsoftu, jejich cluster bude i nadále ohrožen problémem se zabezpečením.

## <a name="kubernetes-marketplace-item"></a>Kubernetes položku Marketplace

Uživatelé si můžou stáhnout položku Kubernetes Marketplace, která umožňuje uživatelům nasadit clustery Kubernetes pomocí modulu AKS nepřímo prostřednictvím šablony na portálu Azure Stack User Portal, což zjednodušuje použití stroje AKS přímo. Tento nástroj je užitečný pro rychlé nastavení clusterů pro ukázky, testování a vývoj. Není určená pro produkční prostředí, protože není součástí sady položek podporovaných Microsoftem.

## <a name="preview-features"></a>Funkce Preview

Pro funkce a funkce, které vyžadují rozšířené testování a zpětnou vazbu od uživatelů, vydává společnost Microsoft nové funkce nebo funkce verze Preview za příznakem funkce. Tyto funkce zvažte jako předprodejní nebo beta funkce. Funkce verze Preview nebo funkce příznaku funkcí nejsou určeny pro produkční prostředí. Průběžné změny funkčnosti a chování, opravy chyb a další změny můžou vést k nestabilním clusterům a výpadkům. Microsoft nepodporuje tyto funkce.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS na Azure Stack](azure-stack-kubernetes-aks-engine-overview.md)