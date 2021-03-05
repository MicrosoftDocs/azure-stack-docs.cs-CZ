---
title: Zásady podpory pro modul AKS v centru Azure Stack
description: Toto téma obsahuje zásady podpory pro modul AKS v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/0102020
ms.openlocfilehash: 9969447ab737c6913576c73b2e53453a6cb8e1f6
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231757"
---
# <a name="support-policies-for-aks-engine-on-azure-stack-hub"></a>Zásady podpory pro modul AKS v centru Azure Stack

Tento článek poskytuje podrobné informace o zásadách a omezeních technické podpory pro modul AKS v centru Azure Stack. Článek také podrobně popisuje Kubernetes položku Marketplace, komponenty Open-Source třetích stran a zabezpečení nebo správu oprav. 

## <a name="self-managed-kubernetes-clusters-on-azure-stack-hub-with-aks-engine"></a>Samoobslužné clustery Kubernetes v centru Azure Stack s modulem AKS

Cloudové komponenty infrastruktury jako služby (IaaS), jako jsou výpočetní nebo síťové komponenty, poskytují uživatelům přístup k ovládacím prvkům nízké úrovně a možnostem přizpůsobení. AKS Engine umožňuje uživatelům laydown Kubernetes clustery s využitím těchto IaaS komponent transparentně, takže uživatelé mají přístup a mají vliv na všechny aspekty jejich nasazení.

Po vytvoření clusteru definuje zákazník hlavní Kubernetes a pracovní uzly, které vytvoří modul AKS. Na těchto uzlech se spouštějí úlohy zákazníka. Vlastníci a můžou zobrazit nebo upravit hlavní a pracovní uzly. Carelessly změněné uzly můžou způsobit ztráty dat a úloh a můžou vykreslovat nefunkční cluster. I operace AKS Engine, jako je například upgrade nebo škálování, přepíší všechny změny mimo hranice. Pokud má cluster například statické lusky, nezachovají se po operaci upgradu AKS Engine.

Vzhledem k tomu, že uzly clusterů zákazníka spouštějí soukromý kód a ukládají citlivá data, podpora Microsoftu k nim přistupovat jenom tak, že jsou jenom omezené. Podpora Microsoftu se nemůže přihlásit, spustit příkazy v nebo zobrazit protokoly pro tyto uzly bez výslovného svolení nebo asistence zákazníka.

## <a name="version-support"></a>Podpora verzí

Podpora verze stroje AKS se řídí stejným vzorem, který je založený na zbývajících zásadách podpory centra Azure Stack, což je podpora verze modulu AKS v centru Azure Stack je založená na vzorci n-2. Pokud je například nejnovější verze modulu AKS je v 0.55.0, sada podporovaných verzí je: 0.48.0, 0.51.0, 0.55.0. Důležité je také použít verzi aktualizace centra Azure Stack a odpovídající mapování na verzi podporovanou modulem AKS, která se udržuje ve zprávách k [vydání verze AKS Engine](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping).

## <a name="aks-engine-supported-areas"></a>Oblasti podporované modulem AKS

Společnost Microsoft poskytuje technickou podporu pro následující:

-  Problémy s příkazy modulu AKS: nasazení, generování, upgrade a škálování. Nástroj by měl být v souladu s chováním v Azure.
-  Problémy s clusterem Kubernetes nasazeným podle [přehledu modulu AKS](azure-stack-kubernetes-aks-engine-overview.md).
-  Problémy s připojením k ostatním službám Azure Stack hub. 
-  Problémy s připojením rozhraní API Kubernetes
-  Problémy s funkcemi poskytovatele služby Azure Stack hub Kubernetes a připojením k Azure Resource Manager.
-  Problémy s konfigurací vygenerovanou pomocí modulu AKS Azure Stack nativní artefakty centra, jako jsou nástroje pro vyrovnávání zatížení, skupiny zabezpečení sítě, virtuální sítě, podsítě, síťová rozhraní, směrovací tabulka, sady dostupnosti, veřejné IP adresy, účet úložiště a počítače virtuálních počítačů. 
-  Problémy se síťovým výkonem a latencí.
-  Problémy se základní imagí AKS, kterou modul AKS používá v odpojených nasazeních. 

## <a name="aks-engine-areas-not-supported"></a>Oblasti AKS Engine se nepodporují.

Společnost Microsoft neposkytuje technickou podporu pro následující:

-  Použití modulu AKS v Azure.
-  Položka Kubernetes centra webu Marketplace. Azure Stack
-  Pomocí následujících možností definice clusteru AKS Engine a doplňků.
    -  Nepodporované doplňky:  
            – Azure AD pod identitou  
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
-  Software třetí strany. Tento software může obsahovat nástroje pro kontrolu zabezpečení a síťová zařízení nebo software.
-  Problémy týkající se více než cloudových nebo více dodavatelů sestavení Microsoft například nepodporuje problémy související se spouštěním řešení pro Cloud ve federaci s více veřejnými cloudy.
-  Vlastní nastavení sítě, kromě těch, které jsou uvedeny v části [oblasti podporované modulem AKS](#aks-engine-supported-areas) .
-  V produkčních prostředích by se měly používat jenom vysoce dostupné clustery Kubernetes, tj. clustery nasazené s minimálně třemi hlavními servery a třemi uzly agentů. V produkčních nasazeních není žádná podpora podporovaná.

##  <a name="security-issues-and-patching"></a>Problémy se zabezpečením a opravy

Pokud se chyba zabezpečení nachází v jedné nebo více součástech modulu AKS nebo poskytovatele Kubernetes pro centrum Azure Stack, Microsoft zpřístupní zákazníkům opravu ovlivněných clusterů za účelem zmírnění problému. Případně tým poskytne pokyny k upgradu uživatelů. Všimněte si, že opravy mohou vyžadovat výpadky clusteru. Pokud se vyžaduje restartování, společnost Microsoft bude informovat zákazníky tohoto požadavku. Pokud uživatelé nepoužijí opravy podle pokynů Microsoftu, jejich cluster bude i nadále ohrožen problémem se zabezpečením.

## <a name="kubernetes-marketplace-item"></a>Kubernetes položku Marketplace

Uživatelé si můžou stáhnout položku Kubernetes Marketplace, která umožňuje uživatelům nasadit clustery Kubernetes pomocí modulu AKS nepřímo prostřednictvím šablony na portálu User Portal centra Azure Stack. Díky tomu je jednodušší používat modul AKS přímo. Položka Kubernetes Marketplace je užitečným nástrojem pro rychlé nastavení clusterů pro ukázky, testování a vývoj. Není určená pro produkční prostředí, takže není zahrnutá v sadě položek, které Microsoft podporuje.

## <a name="preview-features"></a>Funkce ve verzi Preview

Pro funkce a funkce, které vyžadují rozšířené testování a zpětnou vazbu od uživatelů, vydává společnost Microsoft nové funkce nebo funkce verze Preview za příznakem funkce. Tyto funkce zvažte jako předprodejní nebo beta funkce. Funkce verze Preview nebo funkce příznaku funkcí nejsou určeny pro produkční prostředí. Průběžné změny funkčnosti a chování, opravy chyb a další změny můžou vést k nestabilním clusterům a výpadkům. Microsoft nepodporuje tyto funkce.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .