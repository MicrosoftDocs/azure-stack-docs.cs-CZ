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
ms.openlocfilehash: 9b3d58e67d7c6ca7016b3ecb51c09171aae7c06a
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490021"
---
# <a name="set-up-a-development-environment-on-azure-stack"></a>Nastavení vývojového prostředí ve službě Azure Stack 

Můžete vyvíjet aplikace pro Azure Stack pomocí Windows 10, Linuxu nebo macOS pracovní stanice. V tomto článku projdeme: 

- Různých kontextech, ve kterých běží vaše aplikace ve službě Azure Stack. 
- Kroky, které můžete provést, abyste vám pomůžou nastavit s Windows 10, Linuxu nebo macOS pracovní stanice. 
- Kroky k vytvoření prostředku v Azure stacku a nasazení aplikace. 

## <a name="azure-stack-context-and-your-code"></a>Kontext Azure Stack a kódu 

Můžete psát skripty a aplikace, které můžou provádět vše, co ve službě Azure Stack. Však může být užitečné pro omezení, co se pokoušíte provést do tří různých režimech. 

1. V první režimu můžete vytvářet aplikace, které se budou zřizovat prostředky ve službě Azure Stack pomocí šablon Azure Resource Manageru. Například můžete vytvořit skript, který bude konstrukce šablonu Azure Resource Manageru, který se vytvoří virtuální síť a virtuální počítače hostující vaši aplikaci. 

2. V druhém režimu pracovat přímo se koncové body pomocí rozhraní REST API a klienta REST vytvoří ve vašem kódu. V tomto režimu by vytvořit skript, který se vytvoří virtuální síť a virtuální počítače pomocí zasílání požadavků na rozhraní API. 

3. Ve třetí režimu můžete použít kód k vytvoření aplikace, které je hostované ve službě Azure Stack. Jakmile máte infrastrukturu v Azure stacku k hostování vaší aplikace, nasazení aplikace do infrastruktury. Obvykle se připravit vaše prostředí a pak nasadíte aplikaci do daného prostředí. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastruktura jako služba a platforma jako služba 

Stejně jako na cloud Azure Stack podporuje obě: 

- Infrastructure-as--Service (IaaS) 
- Platform-as-a-Service (PaaS) 

IaaS a PaaS informovat, jak byste chtěli nastavení vývojového počítače. 

IaaS je virtualizace částí ze síťových zařízení, sítě a serverů datového centra. Když nasadíte aplikaci k virtuálnímu počítači, který je hostitelem webového serveru, pracujete v paradigma IaaS. V toto paradigma Azure Stack spravuje virtuální zařízení a vaše aplikace je na virtuálním serveru. Poskytovatelé prostředků Azure Stack podporují síťovými součástmi a virtuálních serverů. 

PaaS abstrahuje vrstvy infrastruktury tak, aby nasazení aplikace do koncového bodu, který pak spustí vaši aplikaci. V paradigma PaaS můžou pomocí kontejnerů k hostování vaší aplikace a pak nasadit kontejnerizovanou aplikaci do služby, na kterém běží kontejneru, nebo vaší aplikace může doručit přímo ke službě, na kterém běží vaše aplikace. Azure Stack je možné ke spouštění služby Azure App service a Kubernetes. 

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manageru 

Ve verzi služby Azure Stack Azure Resource Manageru jsou povolené tyto tři režimy i PaaS nebo IaaS. Rozhraní správy můžete nasadit, spravovat a monitorovat prostředky Azure Stack. Správce vám umožňuje spolupracovat s následujícími položkami jako skupiny v rámci jedné operace. Další informace o informace o práci s správce prostředků Azure Stack, najdete v článku [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-development-kits"></a>Azure Stack development Kit 

Azure Stack používá verzi služby Azure Stack Azure Resource Manageru.  Aby bylo možné vám pomohou při práci s Azure Stack Resource Manageru pomocí kódu dle výběru, nabízíme řadu software development Kit. Mezi ně patří: 

- [.Net/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Než začnete 

Budete potřebovat: 

- Přístup k portálu user portal pro Azure Stack. 
- Název tenanta. 
- Vědět, pokud používáte Azure Active directory (Azure AD) nebo Active Directory Federated Services (AD FS) jako vaše identity Manageru. 

Pokud máte nějaké otázky o Azure Stack, obraťte se na váš operátor cloudu. 

## <a name="windows-10"></a>Windows 10 

Počítač s Windows 10 vám umožní pracovat s Powershellu 5.0, Visual Studio a pokud pracujete ASDK připojení k prostředí pomocí připojení VPN. 

### <a name="set-up-your-tools"></a>Nastavte si vaše nástroje 

1. Nastavte pomocí prostředí PowerShell. Instrukce, postupujte podle kroků v [instalace Azure Stack Powershellu](../operator/azure-stack-powershell-install.md). 

2. Stáhněte si nástroje pro Azure Stack. Pokyny, postupujte podle kroků v [nástroje stáhnout Azure Stack z Githubu](../operator/azure-stack-powershell-download.md). 

3. Pokud používáte ASDK, nainstalujte a nakonfigurujte [připojení VPN ke službě Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Instalace a konfigurace rozhraní příkazového řádku Azure. Pokyny, postupujte podle kroků v [profilů verzí použití rozhraní API pomocí Azure CLI ve službě Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Instalace a konfigurace Průzkumníka služby Azure storage. Průzkumník služby Azure storage je samostatná aplikace, která umožňuje pracovat s daty úložiště služby Azure Stack.  Pokyny, postupujte podle kroků v [připojení storage Exploreru k předplatnému Azure Stack nebo účet úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Nainstalovat integrované vývojové prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE), v závislosti na vaší kódové základny a předvolby. 

     - Visual Studio Code (Python, Go, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net/C#). Stáhněte si Visual Studio Community edition z [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Stáhněte si Eclipse z [webu eclipse.org](https://www.eclipse.org/downloads/). 

2. Instalace sady software development kit (SDK) pro váš kód. 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Počítače s Linuxem vám umožní pracovat s Azure CLI a Visual Studio Code nebo vlastní upřednostňované integrovaného vývojového prostředí. 

> [!Note]   
> Pokud používáte počítač s Linuxem pomocí ASDK, váš vzdálený počítač bude muset být ve stejné síti jako ASDK. Nebudete moct připojit pomocí připojení virtuální privátní sítě. 

### <a name="set-up-your-tools"></a>Nastavte si vaše nástroje 

1. Instalace a konfigurace rozhraní příkazového řádku Azure. Pokyny, postupujte podle kroků v [profilů verzí použití rozhraní API pomocí Azure CLI ve službě Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instalace a konfigurace Průzkumníka služby Azure storage. Průzkumník služby Azure storage je samostatná aplikace, která umožňuje pracovat s daty úložiště služby Azure Stack.  Pokyny, postupujte podle kroků v [připojení storage Exploreru k předplatnému Azure Stack nebo účet úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Nainstalovat integrované vývojové prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE), v závislosti na vaší kódové základny a předvolby. 

     - Visual Studio Code (Python, Go, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net/C#). Stáhněte si Visual Studio Community edition z [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Stáhněte si Eclipse z [webu eclipse.org](https://www.eclipse.org/downloads/). 

2. Instalace sady software development kit (SDK) pro váš kód. 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOs 

Počítače s macOS vám umožní pracovat s Azure CLI a Visual Studio Code nebo vlastní upřednostňované integrovaného vývojového prostředí. 

> [!Note]   
> Pokud používáte počítač s macOS s ASDK, váš vzdálený počítač bude muset být ve stejné síti jako ASDK. Nebudete moct připojit pomocí připojení virtuální privátní sítě. 

### <a name="set-up-your-tools"></a>Nastavte si vaše nástroje 

1. Instalace a konfigurace rozhraní příkazového řádku Azure. Pokyny, postupujte podle kroků v [profilů verzí použití rozhraní API pomocí Azure CLI ve službě Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instalace a konfigurace Průzkumníka služby Azure storage. Průzkumník služby Azure storage je samostatná aplikace, která umožňuje pracovat s daty úložiště služby Azure Stack. Pokyny, postupujte podle kroků v [připojení storage Exploreru k předplatnému Azure Stack nebo účet úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Nainstalovat integrované vývojové prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE), v závislosti na vaší kódové základny a předvolby. 

     - Visual Studio Code (Python, Go, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net/C#). Stáhněte si Visual Studio Community edition z [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Stáhněte si Eclipse z [webu eclipse.org](https://www.eclipse.org/downloads/). 

2. Instalace sady software development kit (SDK) pro váš kód. 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Další postup 

Nasazení aplikace do prostředků ve službě Azure Stack. Kroky v [běžné nasazení pro službu Azure Stack](azure-stack-dev-start-deploy-app.md).