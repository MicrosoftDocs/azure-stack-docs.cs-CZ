---
title: PowerShell v centru Azure Stack
description: Prostředí PowerShell v centru Azure Stack má několik modulů a kontextů.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 76dd8c2275c0d75132a508d6849d8356ff2e1f67
ms.sourcegitcommit: 3ee7e9ddffe2ca44af24052e60d808fbef42cf4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82643454"
---
# <a name="get-started-with-powershell-in-azure-stack-hub"></a>Začínáme s PowerShellem v centru Azure Stack

PowerShell je určený pro správu a správu prostředků z příkazového řádku. PowerShell můžete použít, když chcete sestavit automatizované nástroje, které používají model Azure Resource Manager. Modul prostředí PowerShell lze definovat jako sadu funkcí prostředí PowerShell, které jsou seskupeny za účelem správy všech aspektů určité oblasti. Pokud chcete pracovat s Azure Stack hub, musíte juggleovat různé sady rutin PowerShellu.

Tento článek vám pomůže orientovat v různých modulech PowerShellu, které se používají v Azure Stack hub. Když v Azure Stackovém centru použijete PowerShell, můžete pracovat s kteroukoli ze čtyř sad rozhraní API, jak je znázorněno v následující tabulce:

| Rozhraní API | Referenční informace prostředí | Referenční materiály k rozhraní REST |
| --- | --- | --- |
| Globální Azure Resource Manager | [Moduly Azure PowerShellu](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Prohlížeč REST API](https://docs.microsoft.com/rest/api/) |
| Správce prostředků centra Azure Stack | [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md) | [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md) |
| Koncové body Správce centra Azure Stack | [Modul Správce centra Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST API prohlížeč – centrum Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Privilegovaný koncový bod centra Azure Stack | [Použití privilegovaného koncového bodu v centru Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Každé rozhraní kontaktuje poskytovatele prostředků v globálním centru Azure nebo Azure Stack. Poskytovatelé prostředků umožňují využít možnosti Azure. Poskytovatel prostředků Azure COMPUTE například poskytuje programový přístup k vytváření a správě virtuálních počítačů a jejich podpůrných prostředků.

Poskytovatelé prostředků poskytují funkce i ovládací prvky pro správu a konfiguraci prostředku. K poskytovatelům prostředků můžete programově přistupovat pomocí Azure Resource Manager. Rozhraní zase poskytuje plochu pro PowerShell, Azure CLI a vlastní klienty REST.

## <a name="where-to-find-azure-stack-hub-powershell"></a>Kde najít Azure Stack centra PowerShell

Následující blokový diagram znázorňuje vztahy mezi sadami modulů PowerShellu. Z počítače můžete načíst moduly PowerShellu a spravovat globální centrum Azure i Azure Stack.

![Prostředí PowerShell centra Azure Stack](media/azure-stack-powershell-overview/azure-stack-powerShell.svg)

### <a name="global-azure"></a>Globální Azure

Azure PowerShell obsahuje sadu rutin, které používají aktuální verzi Azure Resource Manager pro práci s prostředky Azure. Azure PowerShell používá .NET Standard verzi, což znamená, že můžete používat verze PowerShellu se systémy Windows, macOS a Linux. Azure PowerShell je k dispozici také ve službě Azure Cloud Shell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-hub-resource-manager"></a>Správce prostředků centra Azure Stack

Centrum Azure Stack PowerShell poskytuje sadu rutin, které používají předchozí verze Azure Resource Manager. Tyto rutiny jsou kompatibilní s poskytovateli prostředků v Azure Stack hub. Každý poskytovatel prostředků v centru Azure Stack používá starší verzi poskytovatele, kterou najdete v globálním Azure. K usnadnění koordinace verze každého zprostředkovatele podporovaného Azure Stack Hub můžete použít profily rozhraní API. Azure Stack centrum PowerShell používá PowerShell 5,1 a je k dispozici pouze ve Windows. Další informace najdete v tématu [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-hub-administrator"></a>Správce centra Azure Stack

Centrum Azure Stack zpřístupňuje sadu poskytovatelů prostředků pro operátor cloudu, aby mohl operátor instalovat a spravovat centrum Azure Stack. V globálním prostředí Azure je tato interakce od uživatele abstraktní a na pozadí se zpracovává jako součást Azure. U Azure Stack hub ale podniky můžou podporovat privátní cloud. K provedení těchto úloh operátor komunikuje s rozhraními API Správce centra Azure Stack. Další informace najdete v tématu [instalace PowerShellu pro centrum Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-hub-privileged-endpoint"></a>Privilegovaný koncový bod centra Azure Stack

Pro aktivity operátora v centru Azure Stack, jako je testování instalace a přístup k protokolům, můžou operátory pracovat s privilegovaným koncovým bodem (PEP). PEP je předem nakonfigurovaná konzola vzdáleného prostředí PowerShell, která dává přístup k určitým úkolům pro obsluhu dostatečná. Koncový bod používá k vystavení omezené sady rutin prostředí PowerShell jenom dostatečná Správa (JEA). Další informace najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-hub-tools"></a>Nástroje centra Azure Stack

Centrum Azure Stack zpřístupňuje skripty a další rutiny, které jsou dostupné v úložišti GitHubu, *AzureStack-Tools*. AzureStack – nástroje hostují moduly PowerShellu pro správu a nasazování prostředků do centra Azure Stack. Pokud plánujete navázat připojení k síti VPN, můžete tyto moduly PowerShell stáhnout do Azure Stack Development Kit nebo do externího klienta založeného na systému Windows. Další informace najdete na stránce [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) .

## <a name="work-with-powershell-in-azure-stack-hub"></a>Práce s PowerShellem v centru Azure Stack

Prostředí PowerShell poskytuje programový způsob, jak pracovat s Azure Resource Manager. Můžete pracovat s interaktivním příkazovým řádkem nebo, pokud automatizujete úlohy, můžete psát skripty.

Pokud strávíte spoustu času při práci s centrem Azure Stack PowerShellu, najdete instalace a přeinstalace modulů sami. Pokud pracujete s globálním prostředím Azure současně, může být tato rutina náročná, protože budete muset moduly odinstalovat a přeinstalovat v závislosti na vašem cíli. 

Kontejnery Docker můžete použít k izolaci každé verze PowerShellu na místním počítači. Pokud chcete použít kontejnery Docker, abyste se mohli přepínat z modulu PowerShellu nastaveného na sada modulů PowerShellu, přečtěte si téma [použití Docker ke spuštění PowerShellu](azure-stack-powershell-user-docker.md).


## <a name="next-steps"></a>Další kroky

- Přečtěte si o [profilech rozhraní API pro PowerShell](azure-stack-version-profiles.md) v centru Azure Stack.
- Nainstalujte [PowerShell Azure Stack hub](../operator/azure-stack-powershell-install.md).
- Přečtěte si o vytváření [šablon Azure Resource Manager](azure-stack-develop-templates.md) pro cloudovou konzistenci.
