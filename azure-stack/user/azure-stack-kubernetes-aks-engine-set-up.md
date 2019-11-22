---
title: Nastavení požadavků pro modul AKS v Azure Stack | Microsoft Docs
description: Stanovte požadavky na spuštění stroje dotazů na vašem Azure Stack.
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
ms.openlocfilehash: aa60a2fec7cb8c06d855b070fb93cdf26a5481ba
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310188"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack"></a>Nastavení požadavků pro modul AKS na Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Modul AKS můžete nainstalovat na virtuální počítač ve vašem prostředí nebo na kterýkoli klientský počítač s přístupem ke svému koncovému bodu Azure Stack Správce prostředků. Před spuštěním tohoto modulu budete potřebovat následující věci: AKS Base Ubuntu Server a rozšíření vlastních skriptů pro Linux, které jsou k dispozici ve vašem předplatném, identitu instančního objektu přiřazenou roli přispěvatele a dvojici privátních a veřejných klíčů pro přístup SSH k vašemu serveru Ubuntu. Navíc platí, že pokud používáte Azure Stack Development Kit, bude nutné, aby počítač důvěřoval odpovídajícím certifikátům.

Pokud máte vaše požadavky, můžete začít s [definováním clusteru](azure-stack-kubernetes-aks-engine-deploy-cluster.md).

Pokud jste operátor cloudu pro Azure Stack a chcete nabízet modul AKS, postupujte podle pokynů v části [Přidání modulu AKS do tržiště služby Azure Stack](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Předpoklady pro modul AKS

Pokud chcete používat modul AKS, musíte mít k dispozici následující prostředky. Mějte na paměti, že modul AKS má být používán klienty Azure Stack k nasazení clusterů Kubernetes do svého předplatného tenanta. Jediná část, kde může být požadováno zapojení operátoru Azure Stack, je stažení položek Marketplace a vytvoření identity instančního objektu. Podrobnosti najdete v následující tabulce.

| Požadavek | Popis | Požaduje se | Pokyny |
| --- | --- | --- | --- |
| Rozšíření vlastních skriptů pro Linux | Rozšíření vlastních skriptů pro Linux 2,0<br>Nabídka: vlastní skript pro Linux 2,0<br>Verze: 2.0.6 (nebo nejnovější verze)<br>Vydavatel: Microsoft Corp | Požaduje se | Pokud ve svém předplatném nemáte tuto položku, obraťte se na svého operátora cloudu. |
| AKS Base Ubuntu image | Základní obrázek AKS<br>Nabídka: AKS<br> 2019.10.24 (nebo novější verze)<br>Vydavatel: Microsoft-AKS<br>SKU: AKS-Ubuntu-1604-201910 | Požaduje se | Pokud ve svém předplatném nemáte tuto položku, obraťte se na svého operátora cloudu. Podívejte se na Další informace o závislostech verze, viz [shodný modul se základní verzí image](#matching-engine-to-base-image-version).<br> Pokud jste operátor cloudu pro Azure Stack a chcete nabízet modul AKS, postupujte podle pokynů v části [Přidání modulu AKS do tržiště služby Azure Stack](../operator/azure-stack-aks-engine.md). |
| Předplatné Azure Stack | K nabídkám ve Azure Stack přistupujete prostřednictvím předplatných. Nabídka obsahuje služby, které máte k dispozici. | Požaduje se | Aby bylo možné nasadit jakékoli klientské úlohy v Azure Stack, musíte nejprve získat [Azure Stack předplatné](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| Identita objektu služby (SPN) |  Aplikace, která potřebuje nasadit nebo nakonfigurovat prostředky prostřednictvím Azure Resource Manager, musí být reprezentována instančním objektem. | Požaduje se | Pro tuto položku bude možná potřeba kontaktovat operátor Azure Stack.  Pokyny najdete v tématu [použití identity aplikace pro přístup k prostředkům](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals) . |
| (SPN) přiřazená role **přispěvatele** | Aby mohla aplikace přistupovat k prostředkům ve vašem předplatném pomocí instančního objektu, musíte instančnímu objektu přiřadit roli pro konkrétní prostředek. | Požaduje se | Pokyny najdete v tématu [přiřazení role](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role) . |
| Skupina prostředků | Skupina prostředků je kontejner, který obsahuje související prostředky pro řešení Azure. Pokud nezadáte žádnou existující skupinu prostředků, nástroj si ji pro vás vytvoří. | Nepovinné | [Správa Azure Resource Manager skupin prostředků pomocí Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Privátní veřejný klíč | Pokud chcete použít otevřené připojení SSH z vývojového počítače k VIRTUÁLNÍmu počítači serveru ve vaší instanci Azure Stack, která je hostitelem vaší webové aplikace, je potřeba vytvořit dvojici veřejného a privátního klíče Secure Shell (SSH). | Požaduje se | Pokyny k vygenerování klíče najdete v tématu [generování klíče SSH](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).|

> [!Note]  
> Můžete také vytvořit požadavky pro modul AKS pomocí [Azure CLI pro Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) nebo [Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install).

## <a name="matching-engine-to-base-image-version"></a>Shodný modul se základní verzí image

Modul AKS používá sestavenou image, **základní image AKS**. Jakákoli verze AKS stroje je závislá na konkrétní verzi image, která je k dispozici ve vašem Azure Stack vaším operátorem Azure Stack. V [podporovaných verzích Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)můžete najít tabulku se seznamem verzí modulu AKS a odpovídající podporované verze Kubernetes. Například AKS stroje verze `v0.43.0` závisí na verzi `2019.10.24` základní image AKS. Požádejte svého operátora Azure Stack o stažení konkrétní verze image z Azure Marketplace na web Azure Stack Marketplace.

Pokud není ve vašem Azure Stack Marketplace k dispozici obrázek, aktivuje se a zobrazí se chyba. Pokud například aktuálně používáte modul AKS verze v 0.43.0 a `2019.10.24` verze AKS Base image nejsou k dispozici, při spuštění modulu AKS se zobrazí následující chyba: 

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
> [Nasazení modulu AKS ve Windows v Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Nasazení modulu AKS v systému Linux v Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)