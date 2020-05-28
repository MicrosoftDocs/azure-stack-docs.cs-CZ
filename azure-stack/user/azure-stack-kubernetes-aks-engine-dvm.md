---
title: Přesuňte svůj cluster položek Marketplace do modulu AKS v centru Azure Stack.
description: Naučte se, jak přesunout svůj cluster položek Marketplace do modulu AKS na rozbočovači Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: 7fd43ca4e9ccd6eda5f09198a7b2efe1e9da073a
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111408"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack-hub"></a>Přesuňte svůj cluster položek Marketplace do modulu AKS v centru Azure Stack.

Položka Kubernetes Azure Stack hub na webu Marketplace používá šablonu Azure Resource Manager k nasazení virtuálního počítače nasazení, ke stažení a instalaci modulu AKS a vygenerování vstupního modelu rozhraní API použitého k popisu clusteru, po spuštění tohoto modulu AKS Engine na virtuálním počítači a nasazeném clusteru. V tomto článku se dozvíte, jak získat přístup k modulu AKS a odpovídajícím souborům, abyste ho pak mohli použít k provádění operací aktualizace a škálování v clusteru Kubernetes.

## <a name="access-aks-engine-in-the-dvm"></a>Přístup k AKS Engine v DVM

Po úspěšném dokončení nasazení iniciované Kubernetes Azure Stack centra webu Marketplace můžete najít modul AKS, který se používá k nasazení clusteru nainstalovaného na virtuálním počítači pro nasazení vytvořeného ve skupině prostředků, kterou jste zadali pro cluster, a tento virtuální počítač není součástí clusteru Kubernetes, který se vytvoří ve virtuální síti VNet. Tady je postup, jak najít virtuální počítač a vyhledat v něm modul AKS:

1.  Otevřete portál Azure Stack User Portal a vyhledejte skupinu prostředků, kterou jste zadali pro cluster Kubernetes.
2.  Ve skupině prostředků Najděte virtuální počítač nasazení. Název začíná předponou: **VMD-**.
3.  Vyberte virtuální počítač nasazení. V části Přehled * * Najděte veřejnou IP adresu. Tuto adresu a konzolovou aplikaci, jako je například výstup, použijte k navázání relace SSH k virtuálnímu počítači.
4.  Ve vaší relaci na VIRTUÁLNÍm počítači pro nasazení najdete modul AKS v následující cestě:`./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  Vyhledejte `.json` soubor, který popisuje clustery používané jako vstup do modulu AKS. Soubor jako v `/var/lib/waagent/custom-script/download/0/bin/azurestack.json` . Všimněte si, že soubor obsahuje pověření instančního objektu použité k nasazení clusteru. Pokud se rozhodnete soubor zachovat, je nutné ho přenést do chráněného úložiště.
6.  Vyhledejte výstupní adresář generovaný modulem AKS na adrese `/var/lib/waagent/custom-script/download/0/_output/<resource group name>` . V tomto adresáři Najděte výstup v `apimodel.json` cestě `/var/lib/waagent/custom-script/download/0/bin/apimodel.json` . Adresář a `apimodel.json` soubor obsahují všechny vygenerované certifikáty, klíče a přihlašovací údaje, které jste potřebovali k nasazení clusteru Kubernetes. Uložte tyto prostředky do zabezpečeného umístění.
<<<<<<< HEAD
7.  Vyhledejte konfigurační soubor Kubernetes, který se často označuje jako soubor **kubeconfig** , v cestě `/var/lib/waagent/custom-script/download/0/_output/k8smpi00/kubeconfig/kubeconfig.<location>.json` , kde **<location>** odpovídá vašemu Azure Stack identifikátoru umístění centra. Tento soubor je užitečný v případě, že plánujete nastavit **kubectl** pro přístup ke clusteru Kubernetes.
=======
7.  Vyhledejte konfigurační soubor Kubernetes, který se často označuje jako soubor **kubeconfig** , v cestě `/var/lib/waagent/custom-script/download/0/_output/k8smpi00/kubeconfig/kubeconfig.<location>.json` , kde **`<location>`** odpovídá vašemu Azure Stack identifikátoru umístění centra. Tento soubor je užitečný v případě, že plánujete nastavit **kubectl** pro přístup ke clusteru Kubernetes.

>>>>>>> bd9784471c39194c2918fd281a81b031ee90bafb

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Použití modulu AKS s nově vytvořeným clusterem

Jakmile najdete soubor AKS, Input apimodel. JSON, výstupní adresář a výstupní soubor apimodel. JSON, uložte je do zabezpečeného umístění, můžete použít binární a výstupní modul AKS `apimodel.json` v jakémkoli virtuálním počítači se systémem Linux.

1.  Pokud chcete i nadále používat modul AKS, abyste mohli provádět operace, jako je například **upgrade** a **škálování**, zkopírujte binární soubor **AKS-Engine** do cílového počítače. Pokud používáte stejný **VMD-** Machine do adresáře.

2.  Vytvořte adresář s názvem clusteru nebo jiným názvem mnemotechnic, který odkazuje na nový cluster, a uložte do něj výstupní soubor apimodel. JSON. Ujistěte se, že se jedná o chráněné místo, protože tento soubor obsahuje přihlašovací údaje. Po spuštění můžete spustit modul AKS a spustit tak operace, jako je například [škálování](azure-stack-kubernetes-aks-engine-scale.md) nebo [upgrade](azure-stack-kubernetes-aks-engine-upgrade.md) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .  
- [Řešení potíží s modulem AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

