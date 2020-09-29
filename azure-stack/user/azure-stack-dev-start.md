---
title: Nastavení vývojového prostředí v centru Azure Stack
description: Začněte vyvíjet aplikace pro centrum Azure Stack.
author: mattbriggs
ms.topic: overview
ms.date: 7/23/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: f54c8a442b9df31ff112f1e385b15c441c7c0ffd
ms.sourcegitcommit: a5d3cbe1a10c2a63de95b9e72391dd83473ee299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2020
ms.locfileid: "88920674"
---
# <a name="set-up-a-development-environment-in-azure-stack-hub"></a>Nastavení vývojového prostředí v centru Azure Stack 

Pro vývoj aplikací pro Azure Stack Hub můžete použít pracovní stanici s Windows 10, Linux nebo macOS. V tomto článku se podíváme na: 

- Různé kontexty, ve kterých aplikace běží v centru Azure Stack. 
- Postup pro instalaci s Windows 10, Linux nebo macOS pracovní stanice vám umožní. 
- Postup pro vytváření prostředků v centru Azure Stack a jejich nasazování do aplikace. 

## <a name="azure-stack-hub-context-and-your-code"></a>Kontext centra Azure Stack a váš kód 

Můžete psát skripty a aplikace, abyste mohli provádět mnoho úkolů v centru Azure Stack. Je ale vhodné omezit rozsah svého oboru na následující tři režimy: 

1. V prvním režimu můžete vytvářet aplikace, které zřídí prostředky v Azure Stack centru pomocí šablon Azure Resource Manager. Můžete například napsat skript, který vytvoří šablonu Azure Resource Manager, která zase vytvoří virtuální síť a virtuální počítače, které budou hostovat vaši aplikaci. 

2. V druhém režimu pracujete přímo s koncovými body pomocí REST API a klienta REST, který byl vytvořen ve vašem kódu. V tomto režimu byste napsali skript, který vytvoří virtuální síť a virtuální počítače odesláním požadavků do rozhraní API. 

3. V třetím režimu můžete použít svůj kód k vytvoření aplikace, která je hostovaná v Azure Stack hub. Po vytvoření infrastruktury v centru Azure Stack pro hostování vaší aplikace nasadíte aplikaci do infrastruktury. Obvykle připravíte prostředí a potom do něj nasadíte svoji aplikaci. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infrastruktura jako služba a platforma jako služba 

Pro cloudovou platformu Azure Stack centrum podporuje: 

- Infrastruktura jako služba (IaaS) 
- Platforma jako služba (PaaS) 

IaaS i PaaS informují, jak nastavit vývojový počítač. 

IaaS je virtualizace částí datacentra, které pocházejí ze síťového ozubeného kolace, sítě a serverů. Když nasadíte aplikaci na virtuální počítač, který je hostitelem webového serveru, pracujete v modelu IaaS. V tomto modelu Azure Stack hub spravuje virtuální ozubené kolečko a vaše aplikace je na virtuálním serveru. Poskytovatelé prostředků Azure Stack centra podporují síťové součásti a virtuální servery. 

PaaS vyabstrakce vrstvu infrastruktury, abyste aplikaci nasadili do koncového bodu, ve kterém se aplikace spouští. V modelu PaaS můžete použít kontejnery pro hostování aplikace a pak nasadit aplikaci s kontejnerem do služby, která spouští kontejner. Nebo můžete aplikaci přímo odeslat do služby, na které běží aplikace. Ke spuštění Azure App Service a Kubernetes můžete použít centrum Azure Stack. 

### <a name="azure-stack-hub-resource-manager"></a>Správce prostředků centra Azure Stack 

Tři výše zmíněné režimy, stejně jako PaaS nebo IaaS, jsou povoleny ve verzi Azure Resource Manager centra Azure Stack. Toto rozhraní pro správu umožňuje nasazovat, spravovat a monitorovat prostředky centra Azure Stack. Umožňuje pracovat s prostředky jako se skupinou v rámci jedné operace. Další informace o práci s Správce prostředků centra Azure Stack najdete v tématu [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-hub-sdks"></a>Sady SDK centra Azure Stack 

Azure Stack hub používá Azure Stack centra verze Azure Resource Manager. Abychom vám pomohli pracovat s Azure Stack centra Správce prostředků pomocí vlastního kódu, poskytujeme řadu sad SDK, včetně: 

- [.NET/C #](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Přejít](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>Než začnete 

Než začnete s nastavováním prostředí, budete potřebovat: 

- Přístup k portálu User Portal centra Azure Stack. 
- Název vašeho tenanta. 
- Chcete-li zjistit, zda používáte jako správce identit Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS). 

Pokud máte nějaké dotazy týkající se centra Azure Stack, obraťte se na svého operátora cloudu. 

## <a name="windows-10"></a>Windows 10 

Pokud používáte počítač s Windows 10, můžete pracovat s PowerShellem 5,0 a sadou Visual Studio. A pokud pracujete s Azure Stack Development Kit (ASDK), můžete se připojit k vašemu prostředí pomocí připojení VPN. 

### <a name="set-up-your-tools"></a>Nastavení nástrojů 

1. Nastavte si prostředí PowerShell. Pokyny najdete v tématu [Install Azure Stack hub PowerShell](../operator/azure-stack-powershell-install.md). 

2. Stáhněte si nástroje Azure Stack hub. Pokyny najdete v tématu [Stažení nástrojů centra Azure Stack z GitHubu](../operator/azure-stack-powershell-download.md). 

3. Pokud používáte ASDK, nainstalujte a nakonfigurujte [připojení VPN pro Azure Stack hub](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn). 

4. Instalace a konfigurace rozhraní příkazového řádku Azure CLI. Pokyny najdete v tématu [použití profilů verzí rozhraní API s rozhraním příkazového řádku Azure v centru Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Instalace a konfigurace Průzkumník služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště Azure Stack hub. Pokyny najdete v tématu [připojení Průzkumník služby Storage k předplatnému centra Azure Stack nebo k účtu úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalace integrovaného vývojového prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE) v závislosti na vašem základu kódu a předvolbách. 

     - Visual Studio Code (Python, přejít, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [Code.VisualStudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community Edition z [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Zatmění (Java) Stáhněte si zatmění z [Eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte sadu SDK pro váš kód: 

     - [.NET/C #](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Přejít](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Pokud používáte počítač se systémem Linux, můžete pracovat s rozhraním příkazového řádku Azure CLI, Visual Studio Codem nebo vlastním preferovaným integrovaným vývojovým prostředím. 

> [!NOTE]   
> Pokud používáte počítač se systémem Linux s ASDK, musí být vzdálený počítač ve stejné síti jako ASDK. Nebudete se moct připojit pomocí připojení k virtuální privátní síti. 

### <a name="set-up-your-tools"></a>Nastavení nástrojů 

1. Instalace a konfigurace rozhraní příkazového řádku Azure CLI. Pokyny najdete v tématu [použití profilů verzí rozhraní API s rozhraním příkazového řádku Azure v centru Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instalace a konfigurace Průzkumník služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště Azure Stack hub. Pokyny najdete v tématu [připojení Průzkumník služby Storage k předplatnému centra Azure Stack nebo k účtu úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalace integrovaného vývojového prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE) v závislosti na vašem základu kódu a předvolbách. 

     - Visual Studio Code (Python, přejít, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [Code.VisualStudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community Edition z [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Zatmění (Java) Stáhněte si zatmění z [Eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte sadu SDK pro váš kód: 

     - [.NET/C #](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Přejít](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

MacOS počítač vám umožní pracovat s Azure CLI a Visual Studio Code nebo vlastním preferovaným vývojovým prostředím. 

> [!NOTE]   
> Pokud používáte počítač s macOS s ASDK, musí být váš vzdálený počítač ve stejné síti jako ASDK. Nebudete se moct připojit pomocí připojení k virtuální privátní síti. 

### <a name="set-up-your-tools"></a>Nastavení nástrojů 

1. Instalace a konfigurace rozhraní příkazového řádku Azure CLI. Pokyny najdete v tématu [použití profilů verzí rozhraní API s rozhraním příkazového řádku Azure v centru Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instalace a konfigurace Průzkumník služby Azure Storage. Průzkumník služby Storage je samostatná aplikace, která umožňuje pracovat s daty úložiště Azure Stack hub. Pokyny najdete v tématu [připojení Průzkumník služby Storage k předplatnému centra Azure Stack nebo k účtu úložiště](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalace integrovaného vývojového prostředí 

1. Nainstalujte integrované vývojové prostředí (IDE) v závislosti na vašem základu kódu a předvolbách. 

     - Visual Studio Code (Python, přejít, NodeJS). Stáhněte si Visual Studio Code pro váš počítač z [Code.VisualStudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Stáhněte si Visual Studio Community Edition z [VisualStudio.Microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Zatmění (Java) Stáhněte si zatmění z [Eclipse.org](https://www.eclipse.org/downloads/). 

2. Nainstalujte sadu SDK pro váš kód: 

     - [.NET/C #](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Přejít](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Další kroky 

Pokud chcete nasadit aplikaci do prostředků v centru Azure Stack, přečtěte si téma [společná nasazení pro centrum Azure Stack](azure-stack-dev-start-deploy-app.md).
