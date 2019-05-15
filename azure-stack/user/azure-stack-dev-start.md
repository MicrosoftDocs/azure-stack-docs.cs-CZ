---
title: Nastavení vývojového prostředí ve službě Azure Stack | Dokumentace Microsoftu
description: Začněte s vývojem aplikací pro Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 06c462ab46ce6bbae8d5c3bd6fcb757e14417edf
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617636"
---
# <a name="set-up-a-development-environment-in-azure-stack"></a>Nastavení vývojového prostředí ve službě Azure Stack 

Vývoj aplikací pro Azure Stack pomocí Windows 10, Linuxu nebo macOS pracovní stanice. V tomto článku se podíváme na: 

- Různých kontextech, ve kterých běží vaše aplikace ve službě Azure Stack. 
- Jak postupovat, které vám pomůžou nastavit s Windows 10, Linuxu nebo macOS pracovní stanici. 
- Kroky pro vytváření prostředků ve službě Azure Stack a jejich nasazení do aplikace. 

## <a name="azure-stack-context-and-your-code"></a>Kontext Azure Stack a kódu 

Můžete psát skripty a aplikace provádět mnoho úloh ve službě Azure Stack. Je však vhodné omezit obor na následujících tří režimů: 

1. V první režimu můžete vytvářet aplikace, které zřizovat prostředky ve službě Azure Stack pomocí šablon Azure Resource Manageru. Můžete napsat skript, který vytvoří šablonu Azure Resource Manageru, kterou pak vytvoří virtuální síť a virtuální počítače hostující vaši aplikaci. 

2. V druhém režimu práci přímo s koncovými body pomocí rozhraní REST API a klienta REST, které se vytvořily ve vašem kódu. V tomto režimu měli byste napsat skript, který se vytvoří virtuální síť a virtuální počítače pomocí zasílání požadavků na rozhraní API. 

3. Ve třetí režimu můžete použít kód k vytvoření aplikace, které je hostované ve službě Azure Stack. Po vytvoření infrastruktury v Azure stacku pro hostování vaší aplikace, nasazení aplikace do infrastruktury. Obvykle budete připravovat své prostředí a nasadíme do ní vaši aplikaci. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastruktura jako služba a platforma jako služba 

Azure Stack jako cloudová platforma podporuje obě: 

- Infrastruktura jako služba (IaaS) 
- Platforma jako služba (PaaS) 

IaaS a PaaS informovat o nastavení vývojového počítače. 

IaaS je virtualizace částí, které pocházejí ze síťových zařízení, sítě a serverů datového centra. Nasazení aplikace do virtuálního počítače, který je hostitelem webového serveru, už pracujete v modelu IaaS. V tomto modelu služby Azure Stack spravuje virtuální zařízení a vaše aplikace je na virtuálním serveru. Poskytovatelé prostředků Azure Stack podporují síťovými součástmi a virtuálních serverů. 

PaaS abstrahuje vrstvy infrastruktury tak, aby nasazení aplikace do koncového bodu, který pak spustí aplikaci. V modelu PaaS můžete použít k hostování vaší aplikace a pak nasadit kontejnerizovanou aplikaci do služby, na kterém běží kontejneru kontejnery. Nebo aplikaci může doručit přímo ke službě, na kterém běží aplikace. Azure Stack můžete použít ke spuštění služby Azure App Service a Kubernetes. 

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manageru 

Tři už jsme zmínili, že jsou povoleny režimy, jakož i PaaS nebo IaaS, podle verze Azure Stack Azure Resource Manageru. Toto rozhraní správy můžete nasadit, spravovat a monitorovat prostředky Azure Stack. To umožňuje pracovat s prostředky jako skupinu v rámci jedné operace. Další informace o práci s Azure Resource Manageru zásobníku, naleznete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-sdks"></a>Sady SDK služby Azure Stack 

Azure Stack používá verzi služby Azure Stack Azure Resource Manageru. Vám pomohou při práci s Azure Resource Manageru zásobníku pomocí kódu dle výběru, poskytujeme řady sad SDK, včetně: 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Než začnete 

Před zahájením nastavení prostředí, budete potřebovat: 

- Přístup k portálu user portal pro Azure Stack. 
- Název tenanta. 
- Chcete-li zjistit, zda používáte Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS), jako identity Manageru. 

Pokud máte nějaké dotazy ohledně služby Azure Stack, obraťte se na váš operátor cloudu. 

## <a name="windows-10"></a>Windows 10 

Pokud používáte počítač s Windows 10, můžete pracovat pomocí Powershellu 5.0 a Visual Studio. A pokud pracujete s Azure Stack Development Kit (ASDK), můžete připojit k prostředí pomocí připojení VPN. 

### <a name="set-up-your-tools"></a>Nastavte si vaše nástroje 

1. Nastavte pomocí prostředí PowerShell. Pokyny najdete v tématu [instalace Azure Stack Powershellu](../operator/azure-stack-powershell-install.md). 

2. Stáhněte si nástroje pro Azure Stack. Pokyny najdete v tématu [nástroje stáhnout Azure Stack z Githubu](../operator/azure-stack-powershell-download.md). 

3. Pokud používáte ASDK, nainstalujte a nakonfigurujte [připojení VPN ke službě Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Instalace a konfigurace rozhraní příkazového řádku Azure. Pokyny najdete v tématu [profilů verzí použití rozhraní API pomocí Azure CLI ve službě Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Instalace a konfigurace Průzkumníka služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště služby Azure Stack. Pokyny najdete v tématu [připojení Storage Exploreru k předplatnému Azure Stack nebo účet úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Nainstalovat integrované vývojové prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE), v závislosti na vaší kódové základny a předvolby. 

     - Visual Studio Code (Python, Go, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community edition z [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Stáhněte si Eclipse z [webu eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte si SDK pro váš kód: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Pokud používáte počítač s Linuxem, můžete pracovat s Azure CLI, Visual Studio Code nebo vlastní upřednostňované integrovaného vývojového prostředí. 

> [!Note]   
> Pokud používáte počítač s Linuxem s ASDK, musí být ve stejné síti jako ASDK vzdálený počítač. Nebudete moct připojit pomocí připojení virtuální privátní sítě. 

### <a name="set-up-your-tools"></a>Nastavte si vaše nástroje 

1. Instalace a konfigurace rozhraní příkazového řádku Azure. Pokyny najdete v tématu [profilů verzí použití rozhraní API pomocí Azure CLI ve službě Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instalace a konfigurace Průzkumníka služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště služby Azure Stack. Pokyny najdete v tématu [připojení Storage Exploreru k předplatnému Azure Stack nebo účet úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Nainstalovat integrované vývojové prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE), v závislosti na vaší kódové základny a předvolby. 

     - Visual Studio Code (Python, Go, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community edition z [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Stáhněte si Eclipse z [webu eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte si SDK pro váš kód: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

Počítače s macOS vám umožní pracovat s pomocí rozhraní příkazového řádku Azure a Visual Studio Code nebo vlastní upřednostňované integrovaného vývojového prostředí. 

> [!Note]   
> Pokud používáte počítač s macOS s ASDK, musí být ve stejné síti jako ASDK vzdálený počítač. Nebudete moct připojit pomocí připojení virtuální privátní sítě. 

### <a name="set-up-your-tools"></a>Nastavte si vaše nástroje 

1. Instalace a konfigurace rozhraní příkazového řádku Azure. Pokyny najdete v tématu [profilů verzí použití rozhraní API pomocí Azure CLI ve službě Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instalace a konfigurace Průzkumníka služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště služby Azure Stack. Pokyny najdete v tématu [připojení Storage Exploreru k předplatnému Azure Stack nebo účet úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Nainstalovat integrované vývojové prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE), v závislosti na vaší kódové základny a předvolby. 

     - Visual Studio Code (Python, Go, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community edition z [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Stáhněte si Eclipse z [webu eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte si SDK pro váš kód: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Další postup 

Nasazení aplikace do prostředků ve službě Azure Stack, najdete v článku [běžné nasazení pro službu Azure Stack](azure-stack-dev-start-deploy-app.md).
