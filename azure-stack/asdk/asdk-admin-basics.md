---
title: Základy správy ASDK
description: Přečtěte si, jak provádět základní úlohy správy pro Azure Stack Development Kit (ASDK).
author: justinha
ms.topic: article
ms.date: 02/19/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: c320b0fa20746da3e14a3cb4af2c8fcd1a9775ea
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489823"
---
# <a name="asdk-admin-basics"></a>Základy správy ASDK
Pokud s Azure Stack Development Kit (ASDK) nebudete mít jistotu, budete potřebovat několik věcí. Tento návod poskytuje přehled vaší role jako operátor Azure Stack ve zkušebním prostředí. Familiarizing se s těmito informacemi, zajistíte, aby vaši testovací uživatelé byli co nejrychleji produktivní.

Nejdřív byste si měli projít článek [co je Azure Stack Development Kit?](asdk-what-is.md) , abyste se ujistili, že rozumíte účelu ASDK a jeho omezením. Tuto vývojovou sadu byste měli používat jako izolovaný prostor (sandbox), kde můžete vyhodnotit Azure Stack pro vývoj a testování aplikací v neprodukčním prostředí. 

Podobně jako u Azure se Azure Stack inovace rychle, takže pravidelně vydáváme nová sestavení ASDK. ASDK však nemůžete upgradovat, protože můžete Azure Stack nasazení integrovaných systémů. Takže pokud chcete přejít na nejnovější sestavení, je nutné zcela [znovu nasadit ASDK](asdk-redeploy.md). Nemůžete použít balíčky aktualizací. Tento proces trvá čas, ale výhodou je, že můžete vyzkoušet nejnovější funkce, jakmile budou k dispozici. 

## <a name="what-account-should-i-use"></a>Jaký účet mám použít?
K dispozici je několik důležitých informací o účtech, které byste měli znát při správě Azure Stack. To platí hlavně v nasazeních, která používají Windows Server Active Directory Federation Services (AD FS) (AD FS) jako zprostředkovatele identity místo Azure Active Directory (Azure AD). Následující požadavky na účet platí pro Azure Stack integrovaných systémů i nasazení ASDK:

|Účet|Azure AD|AD FS|
|-----|-----|-----|
|Místní správce (.\Administrator)|Správce hostitele ASDK|Správce hostitele ASDK|
|AzureStack\AzureStackAdmin|Správce hostitele ASDK<br><br>Dá se použít k přihlášení na portál správce Azure Stack.<br><br>Přístup k zobrazení a správě Service Fabricch okruhů|Správce hostitele ASDK<br><br>Žádný přístup k portálu pro správu Azure Stack<br><br>Přístup k zobrazení a správě Service Fabricch okruhů<br><br>Už není vlastníkem předplatného výchozího poskytovatele (DPS).|
|AzureStack\CloudAdmin|Umožňuje přístup k povoleným příkazům v rámci privilegovaného koncového bodu a jejich spouštění.|Umožňuje přístup k povoleným příkazům v rámci privilegovaného koncového bodu a jejich spouštění.<br><br>Nejde se přihlásit k hostiteli ASDK<br><br>Vlastník předplatného výchozího poskytovatele (DPS)|
|Globální správce Azure AD|Používá se během instalace.<br><br>Vlastník předplatného výchozího poskytovatele (DPS)|-|
|

## <a name="what-tools-do-i-use-to-manage"></a>Jaké nástroje se používají ke správě?
Ke správě Azure Stack můžete použít portál správce Azure Stack `https://adminportal.local.azurestack.external` nebo PowerShell. Nejjednodušší způsob, jak se naučit základní koncepty, je prostřednictvím portálu. Pokud chcete používat PowerShell, musíte nainstalovat [PowerShell pro Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) a [stáhnout nástroje Azure Stack z GitHubu](asdk-post-deploy.md#download-the-azure-stack-tools).

Azure Stack používá Azure Resource Manager jako základní mechanismus nasazení, správy a organizace. Pokud se chystáte spravovat Azure Stack a pomáhat uživatelům podpory, měli byste se dozvědět víc o Azure Resource Manager. Další informace najdete v [Začínáme Azure Resource Manager dokumentu White Paper](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf).

## <a name="your-typical-responsibilities"></a>Vaše typické povinnosti
Vaši uživatelé chtějí používat služby. Z perspektivy je hlavním úkolem, aby jim byly dostupné tyto služby. Pomocí ASDK můžete zjistit, které služby se mají nabízet, a jak tyto služby zpřístupnit [vytvořením plánů, nabídek a kvót](../operator/tutorial-offer-services.md?view=azs-2002). Budete také muset přidat položky na web Marketplace, například image virtuálních počítačů. Nejjednodušším způsobem je [stáhnout z Azure položky Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) do Azure Stack.

> [!NOTE]
> Pokud chcete testovat vaše plány, nabídky a služby, měli byste použít portál User Portal, `https://portal.local.azurestack.external` ne portál pro správu `https://adminportal.local.azurestack.external` .

Kromě poskytování služeb se musíte postarat o všechny běžné povinnosti Azure Stackho operátora, abyste zachovali ASDK a provoz. Mezi tyto funkce patří následující věci:
- Přidejte uživatelské účty pro nasazení Azure AD nebo AD FS.
- Přiřazení rolí řízení přístupu na základě role (RBAC) (není omezeno pouze na správce).
- Monitorování stavu infrastruktury
- Správa prostředků sítě a úložiště
- Nahraďte hardware hostitelského počítače s neúspěšnou sadou pro vývoj.

## <a name="where-to-get-support"></a>Kde získat podporu
Pro ASDK se můžete zeptat na dotazy související s podporou na [Azure Stack Fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

Přístup k fórům můžete zobrazit také tak, že v pravém horním rohu portálu pro správu kliknete na **Nápověda** (otazník). Pak kliknutím na tlačítko **help + podpora** otevřete **Přehled**pomocníka + support, který obsahuje odkaz na fórum. Fóra MSDN se pravidelně monitorují.  

> [!IMPORTANT]
> Vzhledem k tomu, že ASDK je zkušební prostředí, není k dispozici žádná oficiální podpora prostřednictvím podpora Microsoftu.

## <a name="next-steps"></a>Další kroky
[Nasazení rozhraní ASDK](asdk-install.md)
