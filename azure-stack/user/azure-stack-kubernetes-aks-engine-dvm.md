---
title: Přesuňte cluster položek Marketplace do modulu AKS v Azure Stack | Microsoft Docs
description: Naučte se, jak přesunout svůj cluster položek Marketplace do modulu AKS na Azure Stack.
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
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/29/2019
ms.openlocfilehash: 4236f1dff939621aa391927341c0ee1136a64b58
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595308"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack"></a>Přesuňte svůj cluster položek Marketplace do modulu AKS na Azure Stack

Položka Kubernetes Azure Stack Marketplace používá šablonu Azure Resource Manager k nasazení virtuálního počítače nasazení (VM) ke stažení a instalaci modulu AKS a k vygenerování vstupního modelu rozhraní API, který se používá k popisu clusteru, po tom, co se modul AKS spustí na virtuálním počítači. a nasazený cluster. V tomto článku se dozvíte, jak získat přístup k modulu AKS a odpovídajícím souborům, abyste ho pak mohli použít k provádění operací aktualizace a škálování v clusteru Kubernetes.

## <a name="access-aks-engine-in-the-dvm"></a>Přístup k AKS Engine v DVM

Po úspěšném dokončení nasazení iniciované položky Kubernetes Azure Stack Marketplace můžete najít modul AKS, který se používá k nasazení clusteru nainstalovaného na virtuálním počítači pro nasazení ve skupině prostředků, kterou jste pro cluster zadali. Tento virtuální počítač není součástí cluster Kubernetes se vytvoří ve virtuální síti VNet. Tady je postup, jak najít virtuální počítač a vyhledat v něm modul AKS:

1.  Otevřete Azure Stack User Portal a vyhledejte skupinu prostředků, kterou jste zadali pro cluster Kubernetes.
2.  Ve skupině prostředků Najděte virtuální počítač nasazení. Název začíná předponou: **VMD-** .
3.  Vyberte virtuální počítač nasazení. V části Přehled * * Najděte veřejnou IP adresu. Tuto adresu a konzolovou aplikaci, jako je například výstup, použijte k navázání relace SSH k virtuálnímu počítači.
4.  Ve vaší relaci na VIRTUÁLNÍm počítači pro nasazení najdete modul AKS v následující cestě: `./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  Vyhledejte soubor `.json`, který popisuje clustery používané jako vstup do modulu AKS. Soubor jako v `/var/lib/waagent/custom-script/download/0/bin/azurestack.json`. Všimněte si, že soubor obsahuje pověření instančního objektu použité k nasazení clusteru. Pokud se rozhodnete soubor zachovat, je nutné ho přenést do chráněného úložiště.
6.  V `/var/lib/waagent/custom-script/download/0/_output/<resource group name>`Najděte výstupní adresář generovaný modulem AKS. V tomto adresáři Najděte výstup `apimodel.json` na cestě `/var/lib/waagent/custom-script/download/0/bin/apimodel.json`. Adresář a `apimodel.json` soubor obsahují všechny generované certifikáty, klíče a přihlašovací údaje, které jste potřebovali k nasazení clusteru Kubernetes. Uložte tyto prostředky do zabezpečeného umístění.
7.  Vyhledejte konfigurační soubor Kubernetes, který se často označuje jako soubor **kubeconfig** , v cestě:, kde odpovídá vašemu identifikátoru Azure Stackho umístění. Tento soubor je užitečný v případě, že plánujete nastavit **kubectl** pro přístup ke clusteru Kubernetes.

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>Použití modulu AKS s nově vytvořeným clusterem

Jakmile najdete soubor AKS, Input apimodel. JSON, výstupní adresář a výstupní soubor apimodel. JSON, uložte je do zabezpečeného umístění, můžete použít binární a výstupní `apimodel.json` stroje AKS na jakémkoli virtuálním počítači se systémem Linux.

1.  Pokud chcete i nadále používat modul AKS, abyste mohli provádět operace, jako je například **upgrade** a **škálování**, zkopírujte binární soubor **AKS-Engine** do cílového počítače. Pokud používáte stejný **VMD-** Machine do adresáře.

2.  Vytvořte adresář s názvem clusteru nebo jiným názvem mnemotechnic, který odkazuje na nový cluster, a uložte do něj výstupní soubor apimodel. JSON. Ujistěte se, že se jedná o chráněné místo, protože tento soubor obsahuje přihlašovací údaje. Po spuštění můžete spustit modul AKS a spustit tak operace, jako je například [škálování](azure-stack-kubernetes-aks-engine-scale.md) nebo [upgrade](azure-stack-kubernetes-aks-engine-upgrade.md) .

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS na Azure Stack](azure-stack-kubernetes-aks-engine-overview.md)  
- [Řešení potíží s modulem AKS na Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

