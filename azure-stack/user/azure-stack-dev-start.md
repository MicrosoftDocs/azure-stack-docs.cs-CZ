---
title: Nastavení vývojového prostředí v Azure Stack | Microsoft Docs
description: Začněte vyvíjet aplikace pro Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 437963355c8077c31ca5f0f55acdd344bb0060e2
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955727"
---
# <a name="set-up-a-development-environment-in-azure-stack"></a>Nastavení vývojového prostředí v Azure Stack 

Můžete vyvíjet aplikace pro Azure Stack pomocí pracovní stanice s Windows 10, Linux nebo macOS. V tomto článku se podíváme na: 

- Různé kontexty, ve kterých aplikace běží v Azure Stack. 
- Postup pro instalaci s Windows 10, Linux nebo macOS pracovní stanice vám umožní. 
- Postup vytvoření prostředků v Azure Stack a jejich nasazení do aplikace. 

## <a name="azure-stack-context-and-your-code"></a>Azure Stack kontext a váš kód 

Můžete psát skripty a aplikace, abyste mohli provádět mnoho úkolů v Azure Stack. Je ale vhodné omezit rozsah svého oboru na následující tři režimy: 

1. V prvním režimu můžete vytvářet aplikace, které zřídí prostředky v Azure Stack pomocí šablon Azure Resource Manager. Můžete například napsat skript, který vytvoří šablonu Azure Resource Manager, která zase vytvoří virtuální síť a virtuální počítače, které budou hostovat vaši aplikaci. 

2. V druhém režimu pracujete přímo s koncovými body pomocí REST API a klienta REST, který byl vytvořen ve vašem kódu. V tomto režimu byste napsali skript, který vytvoří virtuální síť a virtuální počítače odesláním požadavků do rozhraní API. 

3. V třetím režimu můžete použít svůj kód k vytvoření aplikace, která je hostovaná v Azure Stack. Po vytvoření infrastruktury v Azure Stack pro hostování vaší aplikace nasadíte aplikaci do infrastruktury. Obvykle připravíte prostředí a potom do něj nasadíte svoji aplikaci. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastruktura jako služba a platforma jako služba 

Jako produkt pro cloudovou platformu Azure Stack podporuje obojí: 

- Infrastruktura jako služba (IaaS) 
- Platforma jako služba (PaaS) 

IaaS i PaaS informují, jak nastavit vývojový počítač. 

IaaS je virtualizace částí datacentra, které pocházejí ze síťového ozubeného kolace, sítě a serverů. Když nasadíte aplikaci na virtuální počítač, který je hostitelem webového serveru, pracujete v modelu IaaS. V tomto modelu Azure Stack spravuje virtuální ozubené kolečko a vaše aplikace se nachází na virtuálním serveru. Poskytovatelé prostředků Azure Stack podporují síťové součásti a virtuální servery. 

PaaS vyabstrakce vrstvu infrastruktury, abyste aplikaci nasadili do koncového bodu, ve kterém se aplikace spouští. V modelu PaaS můžete použít kontejnery pro hostování aplikace a pak nasadit aplikaci s kontejnerem do služby, která spouští kontejner. Nebo můžete aplikaci přímo odeslat do služby, na které běží aplikace. Ke spuštění Azure App Service a Kubernetes můžete použít Azure Stack. 

### <a name="azure-stack-resource-manager"></a>Azure Stack Správce prostředků 

Tři výše zmíněné režimy, stejně jako PaaS nebo IaaS, jsou povoleny Azure Stack verzí Azure Resource Manager. Toto rozhraní pro správu umožňuje nasadit, spravovat a monitorovat prostředky Azure Stack. Umožňuje pracovat s prostředky jako se skupinou v rámci jedné operace. Další informace o práci s Azure Stack Správce prostředků najdete v tématu [Správa profilů verzí rozhraní API v Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-sdks"></a>Sady Azure Stack SDK 

Azure Stack používá Azure Stack verzi Azure Resource Manager. Abychom vám pomohli pracovat s Azure Stack Správce prostředků pomocí vlastního kódu, poskytujeme řadu sad SDK, včetně těchto: 

- [PohybyC#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>Než začnete 

Než začnete s nastavováním prostředí, budete potřebovat: 

- Přístup k portálu Azure Stack User Portal. 
- Název vašeho tenanta. 
- Chcete-li zjistit, zda používáte jako správce identit Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS). 

Pokud máte nějaké dotazy týkající se Azure Stack, obraťte se na svého operátora cloudu. 

## <a name="windows-10"></a>Windows 10 

Pokud používáte počítač s Windows 10, můžete pracovat s PowerShellem 5,0 a sadou Visual Studio. A pokud pracujete s Azure Stack Development Kit (ASDK), můžete se připojit k vašemu prostředí pomocí připojení VPN. 

### <a name="set-up-your-tools"></a>Nastavení nástrojů 

1. Nastavte si prostředí PowerShell. Pokyny najdete v tématu [instalace Azure Stack PowerShellu](../operator/azure-stack-powershell-install.md). 

2. Stažení Azure Stackch nástrojů. Pokyny najdete v tématu [stažení Azure Stack nástrojů z GitHubu](../operator/azure-stack-powershell-download.md). 

3. Pokud používáte ASDK, nainstalujte a nakonfigurujte [připojení VPN pro Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Instalace a konfigurace rozhraní příkazového řádku Azure CLI. Pokyny najdete v tématu [použití profilů verzí rozhraní API s rozhraním příkazového řádku Azure v Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Instalace a konfigurace Průzkumník služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště Azure Stack. Pokyny najdete v tématu [připojení Průzkumník služby Storage k předplatnému Azure Stack nebo účtu úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalace integrovaného vývojového prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE) v závislosti na vašem základu kódu a předvolbách. 

     - Visual Studio Code (Python, přejít, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [Code.VisualStudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community Edition z [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Zatmění (Java) Stáhněte si zatmění z [Eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte sadu SDK pro váš kód: 

     - [PohybyC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Pokud používáte počítač se systémem Linux, můžete pracovat s rozhraním příkazového řádku Azure CLI, Visual Studio Codem nebo vlastním preferovaným integrovaným vývojovým prostředím. 

> [!Note]   
> Pokud používáte počítač se systémem Linux s ASDK, musí být vzdálený počítač ve stejné síti jako ASDK. Nebudete se moct připojit pomocí připojení k virtuální privátní síti. 

### <a name="set-up-your-tools"></a>Nastavení nástrojů 

1. Instalace a konfigurace rozhraní příkazového řádku Azure CLI. Pokyny najdete v tématu [použití profilů verzí rozhraní API s rozhraním příkazového řádku Azure v Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instalace a konfigurace Průzkumník služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště Azure Stack. Pokyny najdete v tématu [připojení Průzkumník služby Storage k předplatnému Azure Stack nebo účtu úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalace integrovaného vývojového prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE) v závislosti na vašem základu kódu a předvolbách. 

     - Visual Studio Code (Python, přejít, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [Code.VisualStudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community Edition z [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Zatmění (Java) Stáhněte si zatmění z [Eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte sadu SDK pro váš kód: 

     - [PohybyC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

MacOS počítač vám umožní pracovat s Azure CLI a Visual Studio Code nebo vlastním preferovaným vývojovým prostředím. 

> [!Note]   
> Pokud používáte počítač s macOS s ASDK, musí být váš vzdálený počítač ve stejné síti jako ASDK. Nebudete se moct připojit pomocí připojení k virtuální privátní síti. 

### <a name="set-up-your-tools"></a>Nastavení nástrojů 

1. Instalace a konfigurace rozhraní příkazového řádku Azure CLI. Pokyny najdete v tématu [použití profilů verzí rozhraní API s rozhraním příkazového řádku Azure v Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instalace a konfigurace Průzkumník služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště Azure Stack. Pokyny najdete v tématu [připojení Průzkumník služby Storage k předplatnému Azure Stack nebo účtu úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalace integrovaného vývojového prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE) v závislosti na vašem základu kódu a předvolbách. 

     - Visual Studio Code (Python, přejít, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [Code.VisualStudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community Edition z [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Zatmění (Java) Stáhněte si zatmění z [Eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte sadu SDK pro váš kód: 

     - [PohybyC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Další kroky 

Pokud chcete nasadit aplikaci do prostředků v Azure Stack, přečtěte si téma [společná nasazení pro Azure Stack](azure-stack-dev-start-deploy-app.md).
