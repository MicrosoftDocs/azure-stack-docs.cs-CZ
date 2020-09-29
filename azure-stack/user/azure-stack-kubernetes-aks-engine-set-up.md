---
title: Nastavení požadavků pro modul AKS v centru Azure Stack
description: Stanovte požadavky na spuštění stroje dotazů v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 09/08/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/08/2020
ms.openlocfilehash: 6b3443b64dae560451d4d04d653e097d055fa5c1
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573798"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>Nastavení požadavků pro modul AKS v centru Azure Stack

Modul AKS můžete nainstalovat ve svém prostředí do virtuálních počítačů (VM) nebo libovolného klientského počítače s přístupem k Správce prostředkůmu koncovému bodu centra Azure Stack. Před spuštěním tohoto modulu budete potřebovat následující věci: AKS Base Ubuntu Server a rozšíření vlastních skriptů pro Linux, které jsou k dispozici ve vašem předplatném, identitu instančního objektu přiřazenou roli přispěvatele a dvojici privátních a veřejných klíčů pro přístup SSH k vašemu serveru Ubuntu. Navíc platí, že pokud používáte Azure Stack Development Kit, bude nutné, aby počítač důvěřoval odpovídajícím certifikátům.

Pokud máte vaše požadavky, můžete začít s [definováním clusteru](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Pokud jste operátor cloudu pro Azure Stack centrum a chcete nabízet modul AKS, postupujte podle pokynů v části [Přidání modulu AKS na tržišti centra Azure Stack](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Předpoklady pro modul AKS

Chcete-li použít modul AKS, musíte mít k dispozici následující prostředky. Mějte na paměti, že modul AKS má být používán klienty centra Azure Stack k nasazení clusterů Kubernetes do svého předplatného tenanta. Jediná část, kde může být vyžadováno zapojení operátoru centra Azure Stack, je stažení položek Marketplace a vytvoření identity instančního objektu. Podrobnosti najdete v následující tabulce.

Váš operátor cloudu bude potřebovat, aby byly zavedeny následující položky.

| Požadavek | Popis | Povinné | Pokyny |
| --- | --- | --- | --- | --- |
| Azure Stack centra 1910 nebo vyšší | Modul AKS vyžaduje Azure Stack hub 1910 nebo vyšší. | Vyžadováno | Pokud si nejste jistí, jakou verzi centra Azure Stack máte, obraťte se na svého operátora cloudu. |
| Rozšíření vlastních skriptů pro Linux | Rozšíření vlastních skriptů pro Linux 2,0<br>Nabídka: vlastní skript pro Linux 2,0<br>Verze: 2.0.6 (nebo nejnovější verze)<br>Vydavatel: Microsoft Corp | Vyžadováno | Pokud ve svém předplatném nemáte tuto položku, obraťte se na svého operátora cloudu. |
| AKS Base Ubuntu image | AKS Base Ubuntu 16,04 – obrázek LTS<br>Podívejte se na Další informace o závislostech verze, viz [shodný modul se základní verzí image](#matching-engine-to-base-image-version) . | Vyžadováno | Pokud ve svém předplatném nemáte tuto položku, obraťte se na svého operátora cloudu.<br> Pokud jste operátor cloudu pro Azure Stack centrum a chcete nabízet modul AKS, postupujte podle pokynů v části [Přidání modulu AKS na tržišti centra Azure Stack](../operator/azure-stack-aks-engine.md). |
| Identita objektu služby (SPN) |  Aplikace, která potřebuje nasadit nebo nakonfigurovat prostředky prostřednictvím Azure Resource Manager, musí být reprezentována instančním objektem. | Vyžadováno | Pro tuto položku možná budete muset kontaktovat svého operátora centra Azure Stack.<br>Pokud se používá identita instančního objektu Azure Active Directory (Azure AD), z virtuálních počítačů v clusteru Kubernetes se vyžaduje přístup k Internetu, aby bylo možné instanční objekt ověřit pomocí služby Azure AD. Pokud není k dispozici přístup k Internetu, cluster Kubernetes nebude funkční.<br>Pokyny najdete v tématu [použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md) . |
| (SPN) přiřazená role **přispěvatele** | Aby mohla aplikace přistupovat k prostředkům ve vašem předplatném pomocí instančního objektu, musíte instančnímu objektu přiřadit roli pro konkrétní prostředek. | Vyžadováno | Pokyny najdete v tématu [přiřazení role](../operator/azure-stack-create-service-principals.md#assign-a-role) . |


Můžete nastavit následující položky.

| Požadavek | Popis | Povinné | Pokyny |
| --- | --- | --- | --- |
| Předplatné centra Azure Stack | Přistupujete k nabídkám centra Azure Stack v rámci předplatných. Nabídka obsahuje služby, které máte k dispozici. | Vyžadováno | Aby bylo možné v centru Azure Stack nasazovat jakékoli úlohy klientů, musíte nejdřív získat [předplatné služby Azure Stack hub](./azure-stack-subscribe-services.md). |
| Skupina prostředků | Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Pokud nezadáte žádnou existující skupinu prostředků, nástroj si ji pro vás vytvoří. | Volitelné | [Správa Azure Resource Manager skupin prostředků pomocí Azure Portal](/azure/azure-resource-manager/manage-resource-groups-portal) |
| Privátní veřejný klíč | Pokud chcete použít otevřené připojení SSH z vývojového počítače k VIRTUÁLNÍmu počítači serveru ve vaší instanci centra Azure Stack, který je hostitelem vaší webové aplikace, je potřeba vytvořit dvojici veřejného a privátního klíče Secure Shell (SSH). | Vyžadováno | Pokyny k vygenerování klíče najdete v tématu [generování klíče SSH](./azure-stack-dev-start-howto-ssh-public-key.md).|


> [!NOTE]  
> Můžete také vytvořit požadavky pro modul AKS pomocí rozhraní příkazového [řádku Azure CLI pro centra Azure Stack](./azure-stack-version-profiles-azurecli2.md) nebo [powershellu centra Azure Stack](../operator/azure-stack-powershell-install.md).

## <a name="matching-engine-to-base-image-version"></a>Shodný modul se základní verzí image

Modul AKS nasadí přizpůsobený serverový operační systém Ubuntu do jednotlivých imagí uzlů clusteru, **AKS Base Ubuntu 16,04-LTS image distribuce**. Jakákoli verze AKS stroje je závislá na konkrétní verzi image, která je k dispozici ve vašem Azure Stack centru pomocí operátoru centra Azure Stack. V [podporovaných verzích Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions)můžete najít tabulku se seznamem verzí modulu AKS a odpovídající podporované verze Kubernetes. Například verze stroje AKS `v0.55.0` závisí na verzi `2020.08.24` AKS Base Ubuntu 16,04-LTS image distribuce. Požádejte svého operátora centra Azure Stack, aby si stáhl konkrétní verzi image z Azure Marketplace na Marketplace centra pro Azure Stack.

Pokud není v tržišti Azure Stack hub k dispozici obrázek, aktivuje se a chyba. Pokud například aktuálně používáte modul AKS verze v 0.55.0 a AKS Base Ubuntu 16,04-LTS image distribuce verze `2020.08.24` není k dispozici, zobrazí se při spuštění modulu AKS následující chyba: 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-202003:2020.08.24' is not available. 
Verify that all fields in the storage profile are correct.
```

Aktuální verzi modulu AKS můžete zjistit spuštěním následujícího příkazu:

```bash  
$ aks-engine version
Version: v0.55.0
GitCommit: 44a35c00c
GitTreeState: clean
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení modulu AKS ve Windows v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Nasazení modulu AKS v systému Linux v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
