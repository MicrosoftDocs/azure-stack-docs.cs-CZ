---
title: Správa profilů verzí rozhraní API v Azure Stack | Microsoft Docs
description: Přečtěte si o profilech verzí rozhraní API v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 9d0597d4e2687c6563c365470be57c1603bbe616
ms.sourcegitcommit: 72d45bb935db0db172d4d7c37d8e48e79e25af64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68376743"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Správa profilů verzí rozhraní API v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Profily rozhraní API zadejte poskytovatel prostředků Azure a verze rozhraní API pro koncové body Azure REST. Pomocí profilů rozhraní API můžete vytvářet vlastní klienty v různých jazycích. Všichni klienti používají profil rozhraní API ke kontaktování správného poskytovatele prostředků a verze rozhraní API pro Azure Stack.

Můžete vytvořit aplikaci pro práci s poskytovateli prostředků Azure, aniž byste museli přesně vyřadit, která verze rozhraní API poskytovatele prostředků je kompatibilní s Azure Stack. Stačí sjednotit aplikaci na profil a sada SDK se vrátí na správnou verzi rozhraní API.

Toto téma vám pomůže:

 - Principy profilů rozhraní API pro Azure Stack.
 - Přečtěte si, jak můžete pomocí profilů rozhraní API vyvíjet vaše řešení.
 - Informace o tom, kde najít doprovodné materiály pro konkrétní kód.

## <a name="summary-of-api-profiles"></a>Shrnutí profilů rozhraní API

- Profily rozhraní API slouží k reprezentaci sady poskytovatelů prostředků Azure a jejich verzí rozhraní API.
- Vytvořili jste profily rozhraní API, abyste mohli vytvářet šablony napříč několika cloudy Azure. Profily poskytují kompatibilní a stabilní rozhraní.
- Profily jsou vydávány čtyřikrát za rok.
- Používají se tři konvence pojmenování profilů:
    - **nejnovější**  
        Obsahuje nejnovější verze rozhraní API vydané v globálním Azure.
    - **yyyy-mm-dd-hybrid**  
    Vydaná verze biannually se zaměřuje na konzistenci a stabilitu napříč několika cloudy. Tento profil cílí na optimální kompatibilitu Azure Stack.
    - **yyyy-mm-dd-profile** <br>
    Vyrovnává optimální stabilitu a nejnovější funkce.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API – profily a Azure Stack kompatibilita

Nejnovější profily rozhraní API Azure nejsou kompatibilní s Azure Stack. Pomocí následujících zásad vytváření názvů Identifikujte profily, které se mají použít pro Azure Stack řešení:

**Nejnovější**  
Tento profil obsahuje nejaktuálnější verze rozhraní API, které najdete v globálním Azure, které nefungují v Azure Stack. **Nejnovější** má největší počet přerušujících změn. Tento profil přináší nestabilitu a kompatibilitu s jinými cloudy. Pokud se snažíte použít nejaktuálnější verze rozhraní API, **nejnovější** je profil, který byste měli použít.

**Yyyy-mm-dd-hybrid**  
Tento profil je vydaný v březnu a v září každý rok. Má optimální stabilitu a kompatibilitu s různými cloudy. **RRRR-MM-DD – Hybrid** je navržený tak, aby se zacílely na globální Azure a Azure Stack. Verze rozhraní API Azure uvedené v tomto profilu budou stejné jako ty, které jsou uvedené v Azure Stack. Tento profil slouží k vývoji kódu pro hybridní cloudová řešení.

**yyyy-mm-dd-profile**  
Tento profil je vydaný pro globální Azure v červnu a prosinci. Nefunguje s Azure Stack a obvykle se jedná o mnoho konců změn. I když vyvažuje optimální stabilitu a nejnovější funkce, rozdíl mezi **nejnovějším** a tímto profilem je, že **nejnovější** verze rozhraní API se vždycky skládají z nejnovějších verzí rozhraní API bez ohledu na to, kdy rozhraní API uvolníte. Pokud je například pro výpočetní rozhraní API vytvořena nová verze rozhraní API, verze rozhraní API je uvedena v nejnovějším, ale ne v profilu **RRRR-MM-DD-profil** , protože tento profil již existuje. **RRRR-MM-DD – profil** pokrývá nejaktuálnější verze vydané před červenou nebo před prosinec.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager profily rozhraní API

Azure Stack nepoužívá nejnovější verzi verzí rozhraní API, které najdete v globálním Azure. Při vytváření řešení musíte najít verzi rozhraní API pro každého poskytovatele prostředků Azure, který je kompatibilní s Azure Stack.

Místo výzkumu každého poskytovatele prostředků a konkrétní verze, kterou podporuje Azure Stack, můžete použít profil rozhraní API. Profil určuje sadu poskytovatelů prostředků a verze rozhraní API. Sada SDK nebo nástroj vytvořený v sadě SDK se vrátí k cíli `api-version` určenému v profilu. Pomocí profilů rozhraní API můžete zadat verzi profilu, která se vztahuje na celou šablonu. V době běhu vybírá Azure Resource Manager správnou verzi prostředku.

Profily rozhraní API fungují s nástroji, které používají Azure Resource Manager, jako je PowerShell, Azure CLI, kód poskytovaný v sadě SDK a Microsoft Visual Studio. Nástroje a sady SDK můžou používat profily ke čtení, které verze modulů a knihoven se mají zahrnout při sestavování aplikace.

Pokud například použijete PowerShell k vytvoření účtu úložiště pomocí poskytovatele prostředků **Microsoft. Storage** , který podporuje **rozhraní api-Version** 2016-03-30 a virtuální počítač s použitím poskytovatele prostředků **Microsoft. COMPUTE** s **rozhraním API-Version 2015-12-01, je nutné vyhledat, který modul PowerShellu podporuje 2016-03-30 pro úložiště a který modul 2015-02-01 podporuje COMPUTE, a pak je nainstalovat.** Místo toho můžete použít profil. Použijte rutinu `Install-Profile <profilename>`a PowerShell načte správnou verzi modulů.

Podobně při použití sady Python SDK k vytvoření aplikace založené na Pythonu můžete určit profil. Sada SDK načte správné moduly pro poskytovatele prostředků, které jste zadali ve vašem skriptu.

Jako vývojář se můžete soustředit na psaní řešení. Místo přehledání, které verze rozhraní API, poskytovatel prostředků a Cloud pracují společně, můžete použít profil a zjistit, že váš kód funguje napříč všemi cloudy, které tento profil podporují.

## <a name="api-profile-code-samples"></a>Ukázky kódu pro profil rozhraní API

Můžete najít ukázky kódu, které vám pomohou integrovat vaše řešení s preferovaným jazykem pomocí Azure Stack pomocí profilů. V současné době můžete najít doprovodné materiály a ukázky pro následující jazyky:

- **.NET** <br>
Použijte profil rozhraní .NET API k získání nejnovější a nejspolehlivější verze každého typu prostředku v balíčku poskytovatele prostředků. Další informace najdete v tématu [použití profilů verzí rozhraní API s rozhraním .NET v Azure Stack](azure-stack-version-profiles-net.md).
- **PowerShell**  
Pomocí modulu **AzureRM. zaváděcího nástroje** , který je k dispozici prostřednictvím Galerie prostředí PowerShell, získáte rutiny prostředí PowerShell vyžadované pro práci s profily verze rozhraní API. Informace najdete v tématu [použití profilů verzí rozhraní API pro PowerShell](azure-stack-version-profiles-powershell.md).
- **Azure CLI**  
Aktualizujte konfiguraci prostředí tak, aby používala konkrétní profil verze rozhraní API Azure Stack. Informace najdete v tématu [použití profilů verzí rozhraní API pro Azure CLI](azure-stack-version-profiles-azurecli2.md).
- **Go**  
V sadě SDK sady SDK je profil kombinací různých typů prostředků s různými verzemi z různých služeb. Profily jsou k dispozici v profilech/cestách s jejich verzí ve formátu **RRRR-MM-DD** . Informace najdete v tématu [použití profilů verzí rozhraní API pro přejít](azure-stack-version-profiles-go.md).
- **Ruby**  
Sada Ruby SDK pro Azure Stack Správce prostředků poskytuje nástroje, které vám pomůžou při sestavování a správě vaší infrastruktury. Poskytovatelé prostředků v sadě SDK zahrnují výpočetní prostředky, virtuální sítě a úložiště s využitím jazyka Ruby. Informace najdete v tématu [použití profilů verzí rozhraní API s Ruby](azure-stack-version-profiles-ruby.md).
- **Python**  
Python SDK podporuje profily verzí rozhraní API pro cílení na různé cloudové platformy, jako je Azure Stack a globální Azure. Pomocí profilů rozhraní API můžete vytvářet řešení pro hybridní cloud. Informace najdete v tématu [použití profilů verzí rozhraní API v Pythonu](azure-stack-version-profiles-python.md).

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](../operator/azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShellu Azure Stackho uživatele](azure-stack-powershell-configure-user.md)
* [Zkontrolujte podrobnosti o verzích rozhraní API poskytovatele prostředků podporovaných profily](azure-stack-profiles-azure-resource-manager-versions.md).
