---
title: PowerShell ve službě Azure Stack | Dokumentace Microsoftu
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
ms.openlocfilehash: 0cef39147fdbc62fe0652b1e387aa23f5ecb8487
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782906"
---
# <a name="get-started-with-powershell-on-azure-stack"></a>Začínáme s prostředím PowerShell ve službě Azure Stack

PowerShell je navržená pro správu prostředků z příkazového řádku. Pokud chcete vytvářet automatizované nástroje, které používají model Azure Resource Manageru, můžete použít PowerShell. Modul Powershellu můžete definované jako sada funkcí Powershellu, které jsou seskupeny ke správě všech aspektů ke konkrétní oblasti. Chcete-li pracovat se službou Azure Stack, je potřeba přehlednější jinou sadu rutin Powershellu.

Tento článek vám pomůže využít orient sami těchto různých modulů prostředí PowerShell použít ve službě Azure Stack. K dispozici jsou čtyři sady rozhraní API, která může pracovat při použití prostředí PowerShell ve službě Azure Stack.

| Rozhraní API | PowerShell – reference | Reference k rozhraní REST |
| --- | --- | --- |
| 1. Globální Azure Resource Manageru | [Moduly Azure Powershellu](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Prohlížeč rozhraní REST API](https://docs.microsoft.com/rest/api/) |
| 2. Azure Stack Resource Manageru | [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md) | [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md) |
| 3. Koncové body Azure Stack správce | [Modul Správce služby Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [Rozhraní REST API Browseru – Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| 4.  Azure Stack privilegovaného koncového bodu | [Používání privilegovaného koncového bodu ve službě Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Každé rozhraní kontaktuje poskytovatelů prostředků v globálního Azure nebo ve službě Azure Stack. Poskytovatelé prostředků povolit funkce pro Azure. Například poskytovatel prostředků Azure Compute poskytuje programový přístup k vytváření a správa virtuálních počítačů a jejich pomocným prostředkům.

Poskytovatelé prostředků poskytují funkce, ale také poskytnout ovládací prvky pro správu a konfiguraci prostředků. Programový přístup k poskytovateli prostředků pomocí Azure Resource Manageru a pak rozhraní poskytuje plochu pro PowerShell, rozhraní příkazového řádku Azure a klienty REST.

## <a name="where-to-find-azure-stack-powershell"></a>Kde najít Azure Stack Powershellu

Následující blok diagram znázorňuje vztah mezi každou sadu moduly Powershellu. Z vašeho počítače můžete načíst modulů prostředí PowerShell a spravovat globální Azure i Azure Stack.

![Azure Stack Powershellu](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Globální Azure

Prostředí Azure PowerShell obsahuje sadu rutin, které použijte aktuální verzi Azure Resource Manageru pro práci s prostředky Azure. Azure PowerShell používá .NET Standard. To znamená, že verze prostředí PowerShell můžete použít s Windows, macOS a Linux. Azure PowerShell je k dispozici také ve službě Azure Cloud Shell. Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manageru

Azure Stack Powershellu poskytuje sadu rutin, které používají předchozí verze Azure Resource Manageru. Tyto rutiny jsou kompatibilní s poskytovateli prostředků ve službě Azure Stack. Každý poskytovatel prostředků ve službě Azure Stack používá starší verzi poskytovatele nalezen v globálním Azure. Můžete koordinuje verzi každý poskytovatel podporuje Azure Stack, můžete použít profily rozhraní API. Azure Stack Powershellu pomocí prostředí PowerShell 5.1 a je dostupná jenom ve Windows. Další informace najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Správce služby Azure Stack

Azure Stack zveřejňuje sadu poskytovatele prostředků pro operátor cloudu tak, aby bylo možné instalovat a udržovat Azure Stack. Tato interakce je v global Azure abstrahovaná od uživatele a zpracování na pozadí v rámci Azure. Azure Stack, ale umožňuje podnikům podporují privátního cloudu. Aby bylo možné provádět tyto úlohy, operátor, který komunikuje s rozhraními API pro správu Azure Stack. Další informace najdete v tématu [instalace Powershellu pro Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Azure Stack privilegovaného koncového bodu

Operátory pro operátor aktivity ve službě Azure Stack, jako je například testování instalace a přístup k protokolům, může zasahovat privileged koncový bod (období). OBDOBÍ je předem nakonfigurované vzdálené konzoly Powershellu, poskytující operátory s právě dostatečnou přístupem můžete líp provádějí konkrétní úlohy. Koncový bod využívá k tomu omezenou sadu rutin Powershellu JEA (Just Enough Administration). Další informace najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azurestack-tools"></a>AzureStack nástroje

Kromě toho Azure Stack nabízí skripty a další rutiny k dispozici v úložišti GitHub, nástroje pro Azure Stack. Hostitelé nástroje AzureStack moduly Powershellu pro správu a nasazování prostředků do služby Azure Stack. Pokud máte v úmyslu vytvořit připojení k síti VPN, můžete stáhnout tyto moduly Powershellu pro Azure Stack Development Kit, nebo externí klienta se systémem Windows. Další informace najdete v tématu [AzureStack nástroje](https://github.com/Azure/AzureStack-Tools).

## <a name="working-with-powershell-on-azure-stack"></a>Práce s prostředím PowerShell ve službě Azure Stack

Prostředí PowerShell poskytuje programový způsob, jak pracovat s Azure Resource Manageru. Můžete pracovat s interaktivního příkazového řádku nebo psaní skriptů, pokud k automatizaci úloh.

Pokud strávíte spoustu času, práce s Azure Stack Powershellu, které zorientujete instalace a přeinstalace moduly. To může být náročné, pokud pracujete s globální Azure v době, protože bude potřeba odinstalovat a znovu nainstalujte moduly v závislosti na cílové. Kontejnery Dockeru můžete použít k izolaci jednotlivých verzi Powershellu v místním počítači. [Spusťte prostředí PowerShell pomocí Dockeru](azure-stack-powershell-user-docker.md) vypadá tak, že můžete přepnout z modulu PowerShell nastavit do sady modulu prostředí PowerShell s využitím kontejnerů Dockeru.


## <a name="next-steps"></a>Další postup

- Přečtěte si informace o [profily rozhraní API prostředí PowerShell pro](azure-stack-version-profiles.md) v Azure stacku.
- [Instalace Azure Stack Powershellu](../operator/azure-stack-powershell-install.md)
- Přečtěte si informace o vytváření [šablon Azure Resource Manageru](azure-stack-develop-templates.md) konzistence cloudu.