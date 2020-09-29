---
title: Sestava Azure Stack data o využití centra do Azure
titleSuffix: Azure Stack Hub
description: Naučte se, jak ohlásit data o využití centra Azure Stack do Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
ms.topic: article
ms.date: 07/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 8dff2f6debb12b8579e3b8e0b451b8d6bac17cad
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250346"
---
# <a name="report-azure-stack-hub-usage-data-to-azure"></a>Sestava Azure Stack data o využití centra do Azure

Data o využití, označovaná také jako data o spotřebě, představují množství využitých prostředků.

Systémy s více uzly centra Azure Stack, které používají model fakturace založené na spotřebě, by měly pro účely fakturace nahlásit data o využití do Azure. Operátoři centra Azure Stack by měli nakonfigurovat svoji Azure Stack instanci centra, aby nahlásila data o využití do Azure.

> [!IMPORTANT]
> Všechny úlohy [musí být nasazené v rámci předplatných tenantů](#are-users-charged-for-the-infrastructure-vms) , aby byly v souladu s licenčními podmínkami centra Azure Stack.

Vytváření sestav dat o využití se vyžaduje pro uživatele s více uzly Azure Stack, kteří používají licenci v rámci modelu průběžných plateb. Je volitelný pro zákazníky, kteří mají licenci v rámci kapacity modelu (viz stránka [Jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/) ). Pro uživatele Azure Stack Development Kit (ASDK) mohou operátoři služby Azure Stack hub nahlásit data o využití a otestovat funkci. Uživatelům se ale nebude účtovat žádné využití, které tyto náklady účtují.

![Postup účtování pro data o využití v centru Azure Stack](media/azure-stack-usage-reporting/billing-flow.svg)

Data o využití se odesílají z centra Azure Stack do Azure prostřednictvím Azure Bridge. V Azure systém obchodu zpracovává data o využití a vygeneruje vyúčtování. Po vygenerování faktury ho vlastník předplatného Azure může zobrazit a stáhnout z [centrum účtů Azure](https://account.windowsazure.com/subscriptions). Další informace o tom, jak je centrum Azure Stack licencováno, najdete v tématu [Azure Stack centra a](https://go.microsoft.com/fwlink/?LinkId=842847) dokumentu s cenami.

## <a name="set-up-usage-data-reporting"></a>Nastavení generování sestav dat využití

Pokud chcete nastavit vytváření sestav dat o využití, musíte [svou instanci služby Azure Stack hub zaregistrovat v Azure](azure-stack-registration.md). V rámci procesu registrace je nakonfigurované Azure Bridge komponenta centra Azure Stack. Tato součást Azure Bridge připojuje Azure Stack centrum k Azure. Následující data o využití se odesílají z centra Azure Stack do Azure:

- **ID měřiče** – jedinečné ID spotřebovaného prostředku.
- **Množství** využití prostředků.
- **Umístění** – umístění, kde je nasazený aktuální prostředek centra Azure Stack.
- **Identifikátor URI prostředku** – plně kvalifikovaný identifikátor URI prostředku, pro který se vykazuje využití.
- **ID předplatného** – ID předplatného uživatele centra Azure Stack, což je místní předplatné (Azure Stack hub).
- **Čas – počáteční** a koncový čas dat o využití. Mezi časem, kdy se tyto prostředky spotřebují v Azure Stackovém centru, a když se data o využití hlásí do obchodu, dojde k prodlevě. Centrum Azure Stack agreguje data o využití po dobu 24 hodin a vykazování údajů o využití do kanálu Commerce v Azure trvá několik minut. Proto se může v Azure během následujícího dne objevit využití, ke kterému dochází krátce před půlnocí.

## <a name="generate-usage-data-reporting"></a>Generování sestav dat využití

- Pokud chcete testovat vytváření sestav dat o využití, vytvořte v centru Azure Stack několik prostředků. Můžete například vytvořit [účet úložiště](azure-stack-provision-storage-account.md), [virtuální počítač s Windows serverem](../user/azure-stack-create-vm-template.md)a virtuální počítač Linux se základními a standardními SKU, abyste viděli, jak se nahlásí základní využití. Data o využití pro různé typy prostředků se nahlásí v různých měřičích.

- Ponechte své prostředky běžet po dobu několik hodin. Informace o použití jsou shromažďovány přibližně jednou za hodinu. Po shromáždění se tato data přenáší do Azure a zpracují do systému Azure Commerce. Tento proces může trvat až několik hodin.

## <a name="view-usage---csp-subscriptions"></a>Zobrazení využití – předplatná CSP

Pokud jste zaregistrovali Azure Stackového centra pomocí předplatného CSP, můžete si prohlédnout využití a poplatky stejným způsobem jako při prohlížení využití Azure. Použití centra Azure Stack je zahrnuté ve vaší faktuře a v souboru pro odsouhlasení, který je k dispozici prostřednictvím [partnerského centra](https://partnercenter.microsoft.com/partner/home). Soubor pro odsouhlasení se aktualizuje měsíčně. Pokud potřebujete přístup k nejnovějším informacím o použití centra Azure Stack, můžete použít rozhraní API partnerského centra.

![Zobrazení údajů o fakturaci a využití centra Azure Stack v partnerském centru Microsoftu](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Zobrazení předplatných smlouva Enterprise využití

Pokud jste své centrum Azure Stack zaregistrovali pomocí smlouva Enterprise předplatného, můžete si na [portálu EA](https://ea.azure.com/)zobrazit své využití a poplatky. Použití centra Azure Stack je součástí rozšířených souborů ke stažení spolu s využitím Azure v části sestavy na tomto portálu.

## <a name="view-usage---other-subscriptions"></a>Zobrazit využití – ostatní předplatná

Pokud jste své centrum Azure Stack zaregistrovali pomocí jiného typu předplatného (například předplatné s průběžnými platbami), můžete zobrazit využití a poplatky v Centrum účtů Azure. Přihlaste se k [centrum účtů Azure](https://account.windowsazure.com/subscriptions) jako správce účtu Azure a vyberte předplatné Azure, které jste použili k registraci centra Azure Stack. Můžete zobrazit data o využití centra Azure Stack a částku účtovanou za jednotlivé využité prostředky, jak je znázorněno na následujícím obrázku:

![Zobrazení fakturace a toku využití v Centrum účtů Azure](media/azure-stack-usage-reporting/pricing-details.png)

V případě ASDK se prostředky centra Azure Stack neúčtují, takže zobrazená cena je $0,00.

## <a name="which-azure-stack-hub-deployments-are-charged"></a>Která nasazení centra Azure Stack se účtují?

Využití prostředků je pro ASDK zdarma. Azure Stack systémy s více uzly, virtuální počítače úloh, služby úložiště a App Services se účtují.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Účtují se uživatelé za virtuální počítače infrastruktury?

No. Data o využití některých virtuálních počítačů poskytovatele prostředků centra Azure Stack se oznamují do Azure, ale pro tyto virtuální počítače se neúčtují žádné poplatky, ani pro virtuální počítače vytvořené během nasazení za účelem povolení infrastruktury centra Azure Stack.  

Uživatelům se účtují jenom virtuální počítače, které běží v předplatných tenanta. Všechny úlohy musí být nasazené v rámci předplatných tenantů, aby byly v souladu s licenčními podmínkami centra Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-hub-how-do-i-do-it"></a>Mám licenci na Windows Server, kterou chci použít v centru Azure Stack, jak to mám udělat?

Používání stávajících licencí zabraňuje vygenerování měřičů využití. Existující licence k Windows serveru se dají používat v Azure Stack hub. Tento postup je popsaný v části použití existujícího softwaru s centrem Azure Stack v [Průvodci licencováním centra Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536). Aby mohli zákazníci používat své stávající licence, musí nasadit své virtuální počítače s Windows serverem, jak je popsáno v tématu [hybridní zvýhodněná licence pro Windows Server](/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Které předplatné se účtuje za spotřebované prostředky?

Účtuje se předplatné poskytnuté při [registraci centra Azure Stack s Azure](azure-stack-registration.md) .

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Jaké typy předplatných se podporují pro vytváření sestav dat o využití?

Pro Azure Stack je podporováno více uzlů smlouva Enterprise (EA) a odběry CSP. Pro ASDK, smlouva Enterprise (EA), platby s průběžnými platbami a předplatnými MSDN podpora pro vytváření sestav dat využití.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Funguje vytváření sestav údajů o využití v cloudech z svrchovaného díla?

V ASDK se vytváření sestav dat o využití vyžaduje u předplatných, která jsou vytvořená v globálním systému Azure. Předplatná vytvořená v jednom z těchto cloudů z svrchovaného systému (cloudy Azure Government, Azure Německo a Azure Čína 21Vianet) nejde registrovat v Azure, takže nepodporují generování sestav dat o využití.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-hub-match-the-report-generated-from-azure-account-center"></a>Proč se použití nahlášené v centru Azure Stack neshoduje se sestavou vygenerovanou Centrum účtů Azure?

Vždy se jedná o zpoždění mezi daty o využití hlášených rozhraními API využití centra Azure Stack a daty o využití, která jsou uvedena v Centrum účtů Azure. Toto zpoždění je čas potřebný k odeslání údajů o využití z centra Azure Stack do Azure Commerce. Z důvodu této prodlevy se může v Azure během následujícího dne objevit použití, ke kterému dojde krátce před půlnocí. Pokud používáte [rozhraní API využití centra Azure Stack](azure-stack-provider-resource-api.md) a porovnáte výsledky s využitím uvedeným na fakturačním portálu Azure, uvidíte rozdíl.

## <a name="next-steps"></a>Další kroky

- [Rozhraní API využití zprostředkovatele](azure-stack-provider-resource-api.md)  
- [Rozhraní API využití tenanta](azure-stack-tenant-resource-usage-api.md)
- [Nejčastější dotazy k využití](azure-stack-usage-related-faq.md)
- [Správa využití a fakturace jako poskytovatel cloudových řešení](azure-stack-add-manage-billing-as-a-csp.md)
