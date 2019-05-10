---
title: Azure Stack využití dat sestavy do Azure | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu vytváření sestav ve službě Azure Stack data o využití.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: c744a686be2a00418f48b769a5971997a603693f
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172657"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Azure Stack využití dat sestavy do Azure

Data o využití, nazývané také data o spotřebě, představuje objem prostředků používá.

Azure Stack systémech s více uzly, které používají model fakturace založený na spotřebě hlásit data o využití do Azure pro účely fakturace. Operátoři Azure stacku měli nakonfigurovat svou instanci služby Azure Stack data využívání na Azure.

> [!IMPORTANT]
> Všechny úlohy [musí být nasazen v rámci předplatného tenanta](#are-users-charged-for-the-infrastructure-vms) pro dosažení souladu s licenční podmínky služby Azure Stack.

Generování sestav dat využití je vyžadován pro uživatele Azure stacku více uzly, kteří licencí v rámci modelu plateb jako využití. Zadání je volitelné pro zákazníky, kteří licencují v rámci modelu kapacity (najdete v článku [jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/) stránky). Pro uživatele Azure Stack Development Kit můžete operátorům Azure stacku sestavy dat o využití a otestovat funkci. Uživatelé se však nebude účtovat za jakékoliv využití, které způsobují.

![fakturační toku](media/azure-stack-usage-reporting/billing-flow.png)

Využití odeslání dat ze služby Azure Stack na Azure prostřednictvím Azure mostu. V Azure systému obchodování zpracovává data o využití a generuje faktury. Po vygenerování faktury, vlastník předplatného Azure můžete zobrazovat a stahovat z [centra účtů Azure](https://account.windowsazure.com/subscriptions). Další informace o tom, jak je licencován Azure Stack, najdete v článku [Azure Stack, balení a ceny dokumentu](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Nastavení sestav využití dat

K nastavení vytváření sestav dat využití, musíte [registraci vaší instance služby Azure Stack v Azure](azure-stack-registration.md). Jako součást procesu registrace Azure most komponenta služby Azure Stack, který Azure Stack se připojí k Azure a odesílá data o využití, nakonfigurovaná. Následující data využití se odesílají ze služby Azure Stack na Azure:

- **ID měřiče** -jedinečné ID prostředku, který spotřebovával.
- **Množství** -objem využití prostředků.
- **Umístění** – umístění, ve kterém je nasazená aktuální prostředek služby Azure Stack.
- **Identifikátor URI prostředku** – plně kvalifikovaný identifikátor URI prostředku, pro kterou je hlášena využití.
- **ID předplatného** – ID předplatného uživatele služby Azure Stack, což je místní (služby Azure Stack) předplatné.
- **Čas** – počáteční a koncový čas dat o využití. Je určitá prodleva mezi dobou, když jsou tyto prostředky spotřebované ve službě Azure Stack a když se dat o využití se oznamuje službě obchodování. Azure Stack agreguje data o využití pro každých 24 hodin a vykazování dat o využití do kanálu obchodování v Azure jiného trvá několik hodin. Proto se využití, která nastane těsně před půlnocí může zobrazit v Azure následující den.

## <a name="generate-usage-data-reporting"></a>Generování sestav dat využití

- K otestování generování sestav dat využití, vytvořte několik prostředků ve službě Azure Stack. Například můžete vytvořit [účtu úložiště](azure-stack-provision-storage-account.md), [virtuálního počítače s Windows serverem](../user/azure-stack-create-vm-template.md)a virtuální počítač s Linuxem pomocí základní a standardní SKU najdete v článku způsob hlášení využití jader. Pro různé typy zdrojů dat o využití jsou hlášeny podle jiných měřičů.

- Nechte vaše prostředky spuštěné po dobu několika hodin. Informace o využití se shromažďují přibližně jednou za hodinu. Po shromáždění, je tato data přenést do Azure a zpracování do systému Azure commerce. Tento proces může trvat až několik hodin.

## <a name="view-usage---csp-subscriptions"></a>Zobrazit využití - předplatných CSP

Pokud jste zaregistrovali služby Azure Stack pomocí předplatného poskytovatele CSP, můžete zobrazit využití a poplatků stejným způsobem, ve kterém můžete zobrazit využití Azure. Využití služby Azure Stack je součástí vaší faktuře a v kontrolním souboru, k dispozici prostřednictvím [partnerského centra](https://partnercenter.microsoft.com/partner/home). Kontrolním souboru se aktualizuje každý měsíc. Pokud potřebujete přístup k aktuální informace o používání služby Azure Stack, můžete použít rozhraní API Center partnera.

![Partnerské centrum](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Zobrazit využití - předplatných pro smlouvy Enterprise

Pokud jste zaregistrovali služby Azure Stack pomocí s předplatným Enterprise Agreement, můžete zobrazit využití a poplatků [portál EA](https://ea.azure.com/). Využití služby Azure Stack je součástí rozšířené soubory ke stažení, společně s Azure využití na tomto portálu v části sestavy.

## <a name="view-usage---other-subscriptions"></a>Zobrazit využití – další předplatná

Pokud jste zaregistrovali služby Azure Stack pomocí kteréhokoli jiného předplatného zadejte; například předplatné s průběžnými platbami, můžete zobrazit využití a poplatků v centru účtů Azure. Přihlaste se k [centra účtů Azure](https://account.windowsazure.com/subscriptions) jako Azure účtu správce a vyberte předplatné Azure, který jste použili k registraci Azure Stack. Můžete zobrazit data využití služby Azure Stack, velikost, účtují se poplatky za každý použitých prostředků, jak je znázorněno na následujícím obrázku:

![fakturační toku](media/azure-stack-usage-reporting/pricing-details.png)

Pro Azure Stack Development Kit se neúčtují prostředky služby Azure Stack, takže cena uvedená 0.00 $.

## <a name="which-azure-stack-deployments-are-charged"></a>Které nasazení Azure Stack se účtují?

Využití prostředků je zdarma pro Azure Stack Development Kit. Azure Stack několikauzlovými systémy, úloh virtuálních počítačů, služby Storage a aplikační služby, se účtují.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Uživatelé se účtují pro infrastrukturu virtuálních počítačů?

Ne. Data o využití pro některé poskytovatele prostředků služby Azure Stack označené virtuální počítače do Azure, ale neúčtují žádné poplatky pro tyto virtuální počítače ani pro virtuální počítače vytvořené během nasazení umožňuje infrastruktura Azure stacku.  

Uživatelé se účtují jenom pro virtuální počítače, na kterých běží v rámci předplatného tenanta. Všechny úlohy musí být nasazený v rámci předplatného tenanta pro dosažení souladu s licenční podmínky služby Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Mám licenci k Windows serveru, který chci používat ve službě Azure Stack, jak to udělám?

Použitím stávajících licencí se vyhnete generování měřiče využití. Můžete použít stávající licence Windows serveru ve službě Azure Stack, jak je popsáno v části "Použití existující software pomocí služby Azure Stack" [Průvodce licencováním Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536). Chcete-li použít svoje stávající licence, zákazníci musí nasadit své virtuální počítače s Windows serverem jak je popsáno v [Hybrid benefit pro licence Windows serveru](/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Které předplatné se účtuje za spotřebované prostředky?

Předplatné, které je k dispozici při [registrace Azure Stack s využitím Azure](azure-stack-registration.md) se účtuje.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Jaké druhy předplatného jsou podporovány pro generování sestav dat využití?

Pro více uzly služby Azure Stack se smlouvou Enterprise (EA) a zprostředkovatele kryptografických služeb odběry podporovány. Pro průběžné platby Azure Stack Development Kit, smlouvy Enterprise (EA), zprostředkovatele kryptografických služeb a MSDN odběry podporují, generování sestav dat využití.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Provádí práci v suverénních cloudech generování sestav dat využití?

V Azure Stack Development Kit generování sestav dat využití vyžaduje předplatné, které jsou vytvořené v globálním systému Azure. Předplatné vytvořené v některém z suverénních cloudech (cloudy Azure Government, Azure Germany a Azure China) nelze registrovat s Azure, takže nepodporují generování sestav dat využití.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Proč neodpovídá využití ohlášena v zásobníku Azure sestava vygenerována z centra účtů Azure?

Vždy dochází ke zpoždění mezi využití data, která Azure Stack využití rozhraní API a dat o využití v centru účtů Azure. Toto zpoždění je čas potřebný k odesílat data o využití z Azure stacku pro Azure commerce. Kvůli tomuto zpoždění, ke které dochází krátce před půlnocí můžou uvádět v Azure následující den. Pokud používáte [využití rozhraní API služby Azure Stack](azure-stack-provider-resource-api.md)a porovnat výsledky pro využití v fakturačním portálu Azure, uvidíte rozdíl.

## <a name="next-steps"></a>Další postup

- [Rozhraní API využití zprostředkovatele](azure-stack-provider-resource-api.md)  
- [Rozhraní API využití tenanta](azure-stack-tenant-resource-usage-api.md)
- [Nejčastější dotazy k využití](azure-stack-usage-related-faq.md)
- [Spravovat využití a fakturace jako poskytovatele cloudových služeb](azure-stack-add-manage-billing-as-a-csp.md)
