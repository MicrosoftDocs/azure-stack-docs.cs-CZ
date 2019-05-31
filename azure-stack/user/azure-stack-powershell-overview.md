---
title: Prostředí PowerShell ve službě Azure Stack | Dokumentace Microsoftu
description: PowerShell ve službě Azure Stack se počet modulů a kontexty.
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 8f5d97969c96e8f3546d37ffed28b6332d80dc05
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394423"
---
# <a name="get-started-with-powershell-in-azure-stack"></a>Začínáme s prostředím PowerShell ve službě Azure Stack

PowerShell je navržená pro správu prostředků z příkazového řádku. Pokud chcete vytvářet automatizované nástroje, které používají model Azure Resource Manageru, můžete použít PowerShell. Modul Powershellu můžete definované jako sada funkcí Powershellu, které jsou seskupeny ke správě všech aspektů ke konkrétní oblasti. Pro práci s Azure Stack, budete muset přehlednější různé sady rutin prostředí PowerShell.

Tento článek pomůže zorientovat sami na řadu modulů Powershellu, které se používají ve službě Azure Stack. Při použití prostředí PowerShell ve službě Azure Stack můžete pracovat s některou ze čtyř sad rozhraní API, jak je znázorněno v následující tabulce:

| Rozhraní API | PowerShell – reference | Reference k rozhraní REST |
| --- | --- | --- |
| Globální Azure Resource Manageru | [Moduly Azure Powershellu](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Prohlížeč rozhraní REST API](https://docs.microsoft.com/rest/api/) |
| Azure Stack Resource Manageru | [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md) | [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md) |
| Koncové body Správce služby Azure Stack | [Modul Správce služby Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [Prohlížeč rozhraní REST API – Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Koncový bod Azure Stack privilegovaný | [Použití privilegovaných koncového bodu ve službě Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Každé rozhraní kontaktuje poskytovatelů prostředků v globálního Azure nebo ve službě Azure Stack. Poskytovatelé prostředků povolit funkce pro Azure. Například poskytovatel prostředků Azure Compute poskytuje programový přístup k vytváření a správa virtuálních počítačů a jejich pomocným prostředkům.

Zprostředkovatelé prostředků poskytovat funkcí a ovládacích prvků pro správu a konfiguraci prostředku. Poskytovatelé prostředků můžete programově přistupovat pomocí Azure Resource Manageru. Naopak rozhraní poskytuje plochu pro PowerShell, rozhraní příkazového řádku Azure a klienty REST.

## <a name="where-to-find-azure-stack-powershell"></a>Kde najít Azure Stack Powershellu

Následující blok diagram znázorňuje vztahy mezi těmito sadami moduly Powershellu. Z vašeho počítače můžete načíst modulů prostředí PowerShell a spravovat globální Azure i Azure Stack.

![Azure Stack Powershellu](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Global Azure

Prostředí Azure PowerShell obsahuje sadu rutin, které použijte aktuální verzi Azure Resource Manageru pro práci s prostředky Azure. Používá prostředí Azure PowerShell verze .NET Standard, což znamená, že verze prostředí PowerShell můžete použít s Windows, macOS a Linux. Azure PowerShell je k dispozici také ve službě Azure Cloud Shell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manageru

Azure Stack Powershellu poskytuje sadu rutin, které používají předchozí verze Azure Resource Manageru. Tyto rutiny jsou kompatibilní s poskytovateli prostředků ve službě Azure Stack. Každý poskytovatel prostředků ve službě Azure Stack používá starší verzi poskytovatele nalezen v globálním Azure. Můžete koordinuje verzi každého poskytovatele, který podporuje Azure Stack, můžete použít profily rozhraní API. Azure Stack Powershellu pomocí prostředí PowerShell 5.1 a je k dispozici pouze na Windows. Další informace najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Správce služby Azure Stack

Azure Stack zveřejňuje sadu poskytovatele prostředků pro operátor cloudu tak, aby operátor, který můžete nainstalovat a spravovat služby Azure Stack. V globální Azure je tato interakce abstrahovaná od uživatele a zpracování na pozadí v rámci Azure. Pomocí služby Azure Stack ale podniky může podporovat privátní cloud. K provedení těchto úloh, operátor, který komunikuje s rozhraními API pro správu Azure Stack. Další informace najdete v tématu [instalace Powershellu pro Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Koncový bod Azure Stack privilegovaný

Operátory pro operátor aktivity ve službě Azure Stack, jako je například testování instalace a přístup k protokolům, může zasahovat privileged koncový bod (období). OBDOBÍ je předem nakonfigurované vzdálené konzole Powershellu, která nabízí operátory dostatečná oprávnění k provádění konkrétních úkolů. Koncový bod využívá k tomu omezenou sadu rutin Powershellu Just Enough Administration (JEA). Další informace najdete v tématu [použít privilegovaný koncový bod ve službě Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-tools"></a>Nástroje pro Azure Stack

Azure Stack umožňuje skripty a další rutiny k dispozici v úložišti GitHub, *AzureStack nástroje*. Hostitelé nástroje AzureStack moduly Powershellu pro správu a nasazování prostředků do služby Azure Stack. Pokud máte v plánu k navázání připojení k síti VPN, můžete stáhnout tyto moduly Powershellu pro Azure Stack Development Kit, nebo externí klienta se systémem Windows. Další informace najdete v části [AzureStack nástroje](https://github.com/Azure/AzureStack-Tools) stránky.

## <a name="work-with-powershell-in-azure-stack"></a>Práci s prostředím PowerShell ve službě Azure Stack

Prostředí PowerShell poskytuje programový způsob, jak pracovat s Azure Resource Manageru. Můžete pracovat pomocí interaktivního příkazového řádku, nebo pokud používáte automatizaci úkolů, můžete psát skripty.

Pokud strávíte spoustu času, práce s Azure Stack Powershellu, které zorientujete instalace a přeinstalace moduly. Pokud pracujete s globální Azure ve stejnou dobu, může být tato rutina náročné, protože je potřeba odinstalovat a znovu nainstalujte moduly v závislosti na cílové. 

Kontejnery Dockeru můžete použít k izolaci jednotlivých verzi Powershellu v místním počítači. Kontejnery Dockeru používají, můžete přepnout z modulu PowerShell nastavit do sady modulu prostředí PowerShell, najdete v článku [pomocí Dockeru nutné spustit PowerShell](azure-stack-powershell-user-docker.md).


## <a name="next-steps"></a>Další postup

- Přečtěte si informace o [profily rozhraní API prostředí PowerShell pro](azure-stack-version-profiles.md) ve službě Azure Stack.
- Nainstalujte [Azure Stack Powershellu](../operator/azure-stack-powershell-install.md).
- Přečtěte si informace o vytváření [šablon Azure Resource Manageru](azure-stack-develop-templates.md) konzistence cloudu.
