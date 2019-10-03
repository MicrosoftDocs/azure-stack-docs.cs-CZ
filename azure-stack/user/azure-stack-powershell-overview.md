---
title: PowerShell v Azure Stack | Microsoft Docs
description: PowerShell v Azure Stack má několik modulů a kontextů.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 1c2727562fe287c5450a4977803a7c6fce14f27a
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824289"
---
# <a name="get-started-with-powershell-in-azure-stack"></a>Začínáme s PowerShellem v Azure Stack

PowerShell je určený pro správu a správu prostředků z příkazového řádku. PowerShell můžete použít, když chcete sestavit automatizované nástroje, které používají model Azure Resource Manager. Modul prostředí PowerShell lze definovat jako sadu funkcí prostředí PowerShell, které jsou seskupeny za účelem správy všech aspektů určité oblasti. Pokud chcete pracovat s Azure Stack, potřebujete juggle různé sady rutin PowerShellu.

Tento článek vám pomůže orientovat v různých modulech PowerShellu, které se používají v Azure Stack. Když v Azure Stack používáte PowerShell, můžete pracovat s kteroukoli ze čtyř sad rozhraní API, jak je znázorněno v následující tabulce:

| rozhraní API | PowerShell – reference | Odkaz REST |
| --- | --- | --- |
| Globální Azure Resource Manager | [Moduly Azure PowerShell](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Prohlížeč REST API](https://docs.microsoft.com/rest/api/) |
| Azure Stack Správce prostředků | [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md) | [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md) |
| Koncové body správce Azure Stack | [Modul správce Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST API prohlížeč – Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Azure Stack privilegovaného koncového bodu | [Použití privilegovaného koncového bodu v Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Každé rozhraní kontaktuje poskytovatele prostředků v globálním Azure nebo Azure Stack. Poskytovatelé prostředků umožňují využít možnosti Azure. Poskytovatel prostředků Azure COMPUTE například poskytuje programový přístup k vytváření a správě virtuálních počítačů a jejich podpůrných prostředků.

Poskytovatelé prostředků poskytují funkce i ovládací prvky pro správu a konfiguraci prostředku. K poskytovatelům prostředků můžete programově přistupovat pomocí Azure Resource Manager. Rozhraní zase poskytuje plochu pro PowerShell, Azure CLI a vlastní klienty REST.

## <a name="where-to-find-azure-stack-powershell"></a>Kde najít Azure Stack PowerShell

Následující blokový diagram znázorňuje vztahy mezi sadami modulů PowerShellu. Z počítače můžete načíst moduly PowerShellu a spravovat globální Azure i Azure Stack.

![Azure Stack PowerShell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Globální Azure

Azure PowerShell obsahuje sadu rutin, které používají aktuální verzi Azure Resource Manager pro práci s prostředky Azure. Azure PowerShell používá .NET Standard verzi, což znamená, že můžete používat verze PowerShellu se systémy Windows, macOS a Linux. Azure PowerShell je k dispozici také ve službě Azure Cloud Shell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Azure Stack Správce prostředků

Azure Stack PowerShell poskytuje sadu rutin, které používají předchozí verze Azure Resource Manager. Tyto rutiny jsou kompatibilní s poskytovateli prostředků v Azure Stack. Každý poskytovatel prostředků v Azure Stack používá starší verzi poskytovatele, kterou najdete v globálním Azure. K usnadnění koordinace verze každého zprostředkovatele, který podporuje Azure Stack, můžete použít profily rozhraní API. Azure Stack PowerShell používá PowerShell 5,1 a je dostupný jenom v systému Windows. Další informace najdete v tématu [Správa profilů verzí rozhraní API v Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Správce Azure Stack

Azure Stack zveřejňuje sadu poskytovatelů prostředků pro operátor cloudu, aby mohl operátor nainstalovat a udržovat Azure Stack. V globálním prostředí Azure je tato interakce od uživatele abstraktní a na pozadí se zpracovává jako součást Azure. U Azure Stack ale podniky můžou podporovat privátní cloud. K provedení těchto úloh operátor komunikuje s rozhraními API správce Azure Stack. Další informace najdete v tématu [instalace PowerShellu pro Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Azure Stack privilegovaného koncového bodu

Pro aktivity operátorů v Azure Stack, jako je testování instalace a přístupu k protokolům, můžou operátory pracovat s privilegovaným koncovým bodem (PEP). PEP je předem nakonfigurovaná konzola vzdáleného prostředí PowerShell, která dává přístup k určitým úkolům pro obsluhu dostatečná. Koncový bod používá k vystavení omezené sady rutin prostředí PowerShell jenom dostatečná Správa (JEA). Další informace najdete v tématu [použití privilegovaného koncového bodu v Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-tools"></a>Nástroje Azure Stack

Azure Stack zpřístupňuje skripty a další rutiny v úložišti GitHubu, které nabízí *AzureStack nástroje*. AzureStack – nástroje hostují moduly PowerShellu pro správu a nasazování prostředků do Azure Stack. Pokud plánujete navázat připojení k síti VPN, můžete tyto moduly PowerShell stáhnout do Azure Stack Development Kit nebo do externího klienta založeného na systému Windows. Další informace najdete na stránce [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) .

## <a name="work-with-powershell-in-azure-stack"></a>Práce s PowerShellem v Azure Stack

Prostředí PowerShell poskytuje programový způsob, jak pracovat s Azure Resource Manager. Můžete pracovat s interaktivním příkazovým řádkem nebo, pokud automatizujete úlohy, můžete psát skripty.

Pokud při Azure Stack PowerShellu strávíte spoustu času, najdete si instalaci a přeinstalaci modulů. Pokud pracujete s globálním prostředím Azure současně, může být tato rutina náročná, protože budete muset moduly odinstalovat a přeinstalovat v závislosti na vašem cíli. 

Kontejnery Docker můžete použít k izolaci každé verze PowerShellu na místním počítači. Pokud chcete použít kontejnery Docker, abyste se mohli přepínat z modulu PowerShellu nastaveného na sada modulů PowerShellu, přečtěte si téma [použití Docker ke spuštění PowerShellu](azure-stack-powershell-user-docker.md).


## <a name="next-steps"></a>Další kroky

- Přečtěte si o [profilech rozhraní API pro PowerShell](azure-stack-version-profiles.md) v Azure Stack.
- Nainstalujte [Azure Stack PowerShell](../operator/azure-stack-powershell-install.md).
- Přečtěte si o vytváření [šablon Azure Resource Manager](azure-stack-develop-templates.md) pro cloudovou konzistenci.
