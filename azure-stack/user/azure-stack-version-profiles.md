---
title: Správa profilů verzí rozhraní API v centru Azure Stack
description: Přečtěte si o profilech verzí rozhraní API v centru Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 08/28/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: 44638ca39dc5604ee436af5c0fa09c58fadf59eb
ms.sourcegitcommit: 28894abb31423046f6775aadef490c467f1b1820
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2020
ms.locfileid: "89089639"
---
# <a name="manage-api-version-profiles-in-azure-stack-hub"></a>Správa profilů verzí rozhraní API v centru Azure Stack

Profily rozhraní API určují poskytovatele prostředků Azure a verzi rozhraní API pro koncové body Azure REST. Pomocí profilů rozhraní API můžete vytvářet vlastní klienty v různých jazycích. Každý klient používá profil rozhraní API ke kontaktování správného poskytovatele prostředků a verze rozhraní API pro centrum Azure Stack.

Můžete vytvořit aplikaci pro práci s poskytovateli prostředků Azure bez nutnosti přesně zjistit, která verze rozhraní API poskytovatele prostředků je kompatibilní s Azure Stack hub. Stačí sjednotit aplikaci na profil a sada SDK se vrátí na správnou verzi rozhraní API.

Toto téma vám pomůže s těmito aktivitami:

- Pochopte profily rozhraní API pro centrum Azure Stack.
- Přečtěte si, jak můžete pomocí profilů rozhraní API vyvíjet vaše řešení.
- Informace o tom, kde najít doprovodné materiály pro konkrétní kód.

## <a name="summary-of-api-profiles"></a>Shrnutí profilů rozhraní API

- Profily rozhraní API slouží k reprezentaci sady poskytovatelů prostředků Azure a jejich verzí rozhraní API.
- Vytvořili jste profily rozhraní API, abyste mohli vytvářet šablony napříč několika cloudy Azure. Profily poskytují kompatibilní a stabilní rozhraní.
- Profily jsou vydávány čtyřikrát za rok.
- Používají se tři konvence pojmenování profilů:
  - **nejnovější**  
        Obsahuje nejnovější verze rozhraní API vydané v globálním Azure.
  - **RRRR-MM-DD – Hybrid**  
    Tato verze se zaměřuje na rok v rámci každoročního vydání a konzistence v různých cloudech. Tento profil cílí na optimální kompatibilitu centra Azure Stack.
  - **RRRR-MM-DD – profil** <br>
    Vyrovnává optimální stabilitu a nejnovější funkce.

## <a name="azure-api-profiles-and-azure-stack-hub-compatibility"></a>Azure API Profiles a kompatibilita centra Azure Stack

Nejnovější profily rozhraní Azure API nejsou kompatibilní s Azure Stack hub. Pomocí následujících zásad vytváření názvů Identifikujte profily, které se mají použít pro vaše řešení Azure Stack hub:

**Latest (Nejnovější)**  
Tento profil obsahuje nejaktuálnější verze rozhraní API, které najdete v globálním Azure, které nefungují v Azure Stack hub. **Nejnovější** má největší počet přerušujících změn. Tento profil přináší nestabilitu a kompatibilitu s jinými cloudy. Pokud se snažíte použít nejaktuálnější verze rozhraní API, **nejnovější** je profil, který byste měli použít.

**RRRR-MM-DD – Hybrid**  
Tento profil je vydaný v březnu a v září každý rok. Má optimální stabilitu a kompatibilitu s různými cloudy a je navržená pro cílení na globální Azure a centrum Azure Stack. Verze rozhraní API Azure uvedené v tomto profilu budou stejné jako ty, které jsou uvedené v Azure Stack hub. Tento profil slouží k vývoji kódu pro hybridní cloudová řešení.

**RRRR-MM-DD – profil**  
Tento profil je vydaný pro globální Azure v červnu a prosinci. Nefunguje s rozbočovačem Azure Stack a obvykle se jedná o mnoho konců změn. I když vyvažuje optimální stabilitu a nejnovější funkce, rozdíl mezi **nejnovějším** a tímto profilem je, že **nejnovější** verze rozhraní API se vždycky skládají z nejnovějších verzí rozhraní API bez ohledu na to, kdy rozhraní API uvolníte. Pokud je například pro výpočetní rozhraní API vytvořena nová verze rozhraní API, verze rozhraní API je uvedena v **nejnovějším**, ale ne v profilu **RRRR-MM-DD-profil** , protože tento profil již existuje. **RRRR-MM-DD – profil** pokrývá nejaktuálnější verze vydané před červenou nebo před prosinec.

## <a name="azure-resource-manager-api-profiles"></a>Azure Resource Manager profily rozhraní API

Centrum Azure Stack nepoužívá nejnovější verzi rozhraní API, které najdete v globálním Azure. Když vytváříte řešení, musíte najít verzi rozhraní API pro každého poskytovatele prostředků Azure, který je kompatibilní s Azure Stack hub.

Místo výzkumu každého poskytovatele prostředků a konkrétní verze, kterou podporuje centrum Azure Stack, můžete použít profil rozhraní API. Profil určuje sadu poskytovatelů prostředků a verze rozhraní API. Sada SDK nebo nástroj vytvořený v sadě SDK se vrátí k cíli `api-version` určenému v profilu. Pomocí profilů rozhraní API můžete zadat verzi profilu, která se vztahuje na celou šablonu. V době běhu vybírá Azure Resource Manager správnou verzi prostředku.

Profily rozhraní API fungují s nástroji, které používají Azure Resource Manager, jako je PowerShell, Azure CLI, kód poskytovaný v sadě SDK a Microsoft Visual Studio. Nástroje a sady SDK můžou používat profily ke čtení, které verze modulů a knihoven se mají zahrnout při sestavování aplikace.

Pokud například použijete PowerShell k vytvoření účtu úložiště pomocí poskytovatele prostředků **Microsoft. Storage** , který podporuje **rozhraní api-Version** 2016-03-30 a virtuální počítač s použitím poskytovatele prostředků **Microsoft. COMPUTE** s **rozhraním API-verze** 2015-12-01, je nutné vyhledat, který modul PowerShellu podporuje 2016-03-30 pro úložiště a který modul podporuje 2015-02-01 pro výpočetní prostředí a pak je nainstalovat. Místo toho můžete použít profil. Použijte rutinu `Install-Profile <profilename>` a PowerShell načte správnou verzi modulů.

Podobně při použití sady Python SDK k vytvoření aplikace založené na Pythonu můžete určit profil. Sada SDK načte správné moduly pro poskytovatele prostředků, které jste zadali ve vašem skriptu.

Jako vývojář se můžete soustředit na psaní řešení. Místo přehledání, které verze rozhraní API, poskytovatel prostředků a Cloud pracují společně, můžete použít profil a zjistit, že váš kód funguje napříč všemi cloudy, které tento profil podporují.

## <a name="api-profile-code-samples"></a>Ukázky kódu pro profil rozhraní API

Můžete najít ukázky kódu, které vám pomohou integrovat vaše řešení s preferovaným jazykem pomocí Azure Stack hub pomocí profilů. V současné době můžete najít doprovodné materiály a ukázky pro následující jazyky:

- **.NET** <br>
Použijte profil rozhraní .NET API k získání nejnovější a nejspolehlivější verze každého typu prostředku v balíčku poskytovatele prostředků. Další informace najdete v tématu [použití profilů verzí rozhraní API s rozhraním .NET v centru Azure Stack](azure-stack-version-profiles-net.md).
- **PowerShell**  
Pomocí modulu  **AzureRM. zaváděcího nástroje** , který je k dispozici prostřednictvím Galerie prostředí PowerShell, získáte rutiny prostředí PowerShell vyžadované pro práci s profily verze rozhraní API. Informace najdete v tématu [použití profilů verzí rozhraní API pro PowerShell](../operator/azure-stack-powershell-install.md?view=azs-2002).
- **Azure CLI**  
Aktualizujte konfiguraci prostředí tak, aby používala profil konkrétní verze rozhraní API centra Azure Stack. Informace najdete v tématu [použití profilů verzí rozhraní API pro Azure CLI](azure-stack-version-profiles-azurecli2.md).
- **Přejít**  
V sadě SDK sady SDK je profil kombinací různých typů prostředků s různými verzemi z různých služeb. Profily jsou k dispozici v profilech/cestách s jejich verzí ve formátu **RRRR-MM-DD** . Informace najdete v tématu [použití profilů verzí rozhraní API pro přejít](azure-stack-version-profiles-go.md).
- **Ruby**  
Sada Ruby SDK pro centrum Azure Stack Správce prostředků poskytuje nástroje, které vám pomůžou sestavovat a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK zahrnují výpočetní prostředky, virtuální sítě a úložiště s využitím jazyka Ruby. Informace najdete v tématu [použití profilů verzí rozhraní API s Ruby](azure-stack-version-profiles-ruby.md).
- **Python**  
Python SDK podporuje profily verzí rozhraní API pro cílení na různé cloudové platformy, jako je Azure Stack hub a globální Azure. Pomocí profilů rozhraní API můžete vytvářet řešení pro hybridní cloud. Informace najdete v tématu [použití profilů verzí rozhraní API v Pythonu](azure-stack-version-profiles-python.md).
- **Node.js**  
Sada Node.js SDK pro Azure Stack centra Správce prostředků poskytuje nástroje, které vám pomůžou sestavovat a spravovat infrastrukturu. Další informace najdete v tématu [použití profilů verzí rozhraní API s Node.js](azure-stack-version-profile-nodejs.md).

## <a name="next-steps"></a>Další kroky

- [Instalace PowerShellu pro centrum Azure Stack](../operator/azure-stack-powershell-install.md)
- [Konfigurace prostředí PowerShell uživatele centra Azure Stack](azure-stack-powershell-configure-user.md)
- [Zkontrolujte podrobnosti o verzích rozhraní API poskytovatele prostředků podporovaných profily](azure-stack-profiles-azure-resource-manager-versions.md).
