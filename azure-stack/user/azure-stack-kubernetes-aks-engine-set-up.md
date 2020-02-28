---
title: Nastavení požadavků pro modul AKS v centru Azure Stack
description: Stanovte požadavky na spuštění stroje dotazů v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 2/27/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/27/2020
ms.openlocfilehash: 1f307a554eca0b9b1bc2af9d8e64a8ec585da078
ms.sourcegitcommit: bbc4023c9a673c146de4a9e242311d429f7781eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77782817"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>Nastavení požadavků pro modul AKS v centru Azure Stack

Modul AKS můžete nainstalovat na virtuální počítač ve vašem prostředí nebo na kterýkoli klientský počítač s přístupem ke svému Správce prostředkůmu koncovému bodu centra Azure Stack. Před spuštěním tohoto modulu budete potřebovat následující věci: AKS Base Ubuntu Server a rozšíření vlastních skriptů pro Linux, které jsou k dispozici ve vašem předplatném, identitu instančního objektu přiřazenou roli přispěvatele a dvojici privátních a veřejných klíčů pro přístup SSH k vašemu serveru Ubuntu. Navíc platí, že pokud používáte Azure Stack Development Kit, bude nutné, aby počítač důvěřoval odpovídajícím certifikátům.

Pokud máte vaše požadavky, můžete začít s [definováním clusteru](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Pokud jste operátor cloudu pro Azure Stack centrum a chcete nabízet modul AKS, postupujte podle pokynů v části [Přidání modulu AKS na tržišti centra Azure Stack](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Předpoklady pro modul AKS

Chcete-li použít modul AKS, musíte mít k dispozici následující prostředky. Mějte na paměti, že modul AKS má být používán klienty centra Azure Stack k nasazení clusterů Kubernetes do svého předplatného tenanta. Jediná část, kde může být vyžadováno zapojení operátoru centra Azure Stack, je stažení položek Marketplace a vytvoření identity instančního objektu. Podrobnosti najdete v následující tabulce.

Váš operátor cloudu bude potřebovat, aby byly zavedeny následující položky.

| Požadavek | Popis | Požadováno | Pokyny |
| --- | --- | --- | --- | --- |
| Azure Stack centra 1910 nebo vyšší | Modul AKS vyžaduje Azure Stack hub 1910 nebo vyšší. | Požadováno | Pokud si nejste jistí, jakou verzi centra Azure Stack máte, obraťte se na svého operátora cloudu. |
| Rozšíření vlastních skriptů pro Linux | Rozšíření vlastních skriptů pro Linux 2,0<br>Nabídka: vlastní skript pro Linux 2,0<br>Verze: 2.0.6 (nebo nejnovější verze)<br>Vydavatel: Microsoft Corp | Požadováno | Pokud ve svém předplatném nemáte tuto položku, obraťte se na svého operátora cloudu. |
| AKS Base Ubuntu image | Základní obrázek AKS<br>Nabídka: AKS<br> 2019.10.24 (nebo novější verze)<br>Vydavatel: Microsoft-AKS<br>SKU: AKS-Ubuntu-1604-201910 | Požadováno | Pokud ve svém předplatném nemáte tuto položku, obraťte se na svého operátora cloudu. Podívejte se na Další informace o závislostech verze, viz [shodný modul se základní verzí image](#matching-engine-to-base-image-version).<br> Pokud jste operátor cloudu pro Azure Stack centrum a chcete nabízet modul AKS, postupujte podle pokynů v části [Přidání modulu AKS na tržišti centra Azure Stack](../operator/azure-stack-aks-engine.md). |
| Identita objektu služby (SPN) |  Aplikace, která potřebuje nasadit nebo nakonfigurovat prostředky prostřednictvím Azure Resource Manager, musí být reprezentována instančním objektem. | Požadováno | Pro tuto položku možná budete muset kontaktovat svého operátora centra Azure Stack.  Pokyny najdete v tématu [použití identity aplikace pro přístup k prostředkům](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals) . |
| (SPN) přiřazená role **přispěvatele** | Aby mohla aplikace přistupovat k prostředkům ve vašem předplatném pomocí instančního objektu, musíte instančnímu objektu přiřadit roli pro konkrétní prostředek. | Požadováno | Pokyny najdete v tématu [přiřazení role](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role) . |

Můžete nastavit následující položky.

| Požadavek | Popis | Požadováno | Pokyny |
| --- | --- | --- | --- |
| Předplatné centra Azure Stack | Přistupujete k nabídkám centra Azure Stack v rámci předplatných. Nabídka obsahuje služby, které máte k dispozici. | Požadováno | Aby bylo možné v centru Azure Stack nasazovat jakékoli úlohy klientů, musíte nejdřív získat [předplatné služby Azure Stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| Skupina prostředků | Skupina prostředků je kontejner, který uchovává související prostředky pro řešení Azure. Pokud nezadáte žádnou existující skupinu prostředků, nástroj si ji pro vás vytvoří. | Volitelné | [Správa Azure Resource Manager skupin prostředků pomocí Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Privátní veřejný klíč | Pokud chcete použít otevřené připojení SSH z vývojového počítače k VIRTUÁLNÍmu počítači serveru ve vaší instanci centra Azure Stack, který je hostitelem vaší webové aplikace, je potřeba vytvořit dvojici veřejného a privátního klíče Secure Shell (SSH). | Požadováno | Pokyny k vygenerování klíče najdete v tématu [generování klíče SSH](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).|


> [!Note]  
> Můžete také vytvořit požadavky pro modul AKS pomocí rozhraní příkazového [řádku Azure CLI pro centra Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) nebo [powershellu centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install).

## <a name="matching-engine-to-base-image-version"></a>Shodný modul se základní verzí image

Modul AKS používá sestavenou image, **základní image AKS**. Jakákoli verze AKS stroje je závislá na konkrétní verzi image, která je k dispozici ve vašem Azure Stack centru pomocí operátoru centra Azure Stack. V [podporovaných verzích Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)můžete najít tabulku se seznamem verzí modulu AKS a odpovídající podporované verze Kubernetes. Například AKS stroje verze `v0.43.0` závisí na verzi `2019.10.24` základní image AKS. Požádejte svého operátora centra Azure Stack, aby si stáhl konkrétní verzi image z Azure Marketplace na Marketplace centra pro Azure Stack.

Pokud není v tržišti Azure Stack hub k dispozici obrázek, aktivuje se a chyba. Pokud například aktuálně používáte modul AKS verze v 0.43.0 a `2019.10.24` verze AKS Base image nejsou k dispozici, při spuštění modulu AKS se zobrazí následující chyba: 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-201908:2019.08.09' is not available. 
Verify that all fields in the storage profile are correct.
```

Aktuální verzi modulu AKS můžete zjistit spuštěním následujícího příkazu:

```bash  
$ aks-engine version
Version: v0.39.1
GitCommit: 6fff62731
GitTreeState: clean
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nasazení modulu AKS ve Windows v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Nasazení modulu AKS v systému Linux v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)