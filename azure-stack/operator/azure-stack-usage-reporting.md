---
title: Nahlásit data o využití Azure Stack do Azure | Microsoft Docs
description: Naučte se, jak nastavit vytváření sestav dat o využití v Azure Stack.
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
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 5fecdbe7bcc85ea01e1502afcfc1c67d3b830c3e
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019385"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Sestava údajů o využití Azure Stack do Azure

Data o využití, označovaná také jako data o spotřebě, představují množství využitých prostředků.

Azure Stack systémy s více uzly, které používají model fakturace založené na spotřebě, by měly pro účely fakturace nahlásit data o využití do Azure. Operátoři Azure Stack by měli nakonfigurovat svoji Azure Stack instanci, aby nahlásila data o využití do Azure.

> [!IMPORTANT]
> Všechny úlohy [musí být nasazené v rámci předplatných tenantů](#are-users-charged-for-the-infrastructure-vms) , aby byly v souladu s licenčními podmínkami Azure Stack.

Vytváření sestav dat o využití je vyžadováno pro Azure Stack uživatelů s více uzly, kteří používají licenci v rámci modelu průběžných plateb. Je volitelný pro zákazníky, kteří mají licenci v rámci kapacity modelu (viz stránka [Jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/) ). Pro uživatele Azure Stack Development Kit (ASDK) mohou Azure Stack operátoři nahlásit data o využití a otestovat funkci. Uživatelům se ale nebude účtovat žádné využití, které tyto náklady účtují.

![tok fakturace](media/azure-stack-usage-reporting/billing-flow.png)

Data o využití se odesílají z Azure Stack do Azure prostřednictvím Azure Bridge. V Azure systém obchodu zpracovává data o využití a vygeneruje vyúčtování. Po vygenerování faktury ho vlastník předplatného Azure může zobrazit a stáhnout z [centrum účtů Azure](https://account.windowsazure.com/subscriptions). Další informace o tom, jak je Azure Stack licencovaná, najdete v [dokumentu Azure Stack balení a cen](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Nastavení generování sestav dat využití

Pokud chcete nastavit vytváření sestav dat o využití, musíte [zaregistrovat instanci Azure Stack v Azure](azure-stack-registration.md). V rámci procesu registrace je Azure Bridge komponentou Azure Stack, která připojuje Azure Stack k Azure a odesílá data o využití, je nakonfigurovaná. Následující data o využití se odesílají z Azure Stack do Azure:

- **ID měřiče** – jedinečné ID spotřebovaného prostředku.
- **Množství** využití prostředků.
- **Umístění** – umístění, kde je nasazen aktuální Azure Stack prostředek.
- **Identifikátor URI prostředku** – plně kvalifikovaný identifikátor URI prostředku, pro který se vykazuje využití.
- **ID předplatného** – ID předplatného uživatele Azure Stack, které je místní (Azure Stack) předplatné.
- **Čas – počáteční** a koncový čas dat o využití. Mezi časem, kdy se tyto prostředky spotřebují v Azure Stack a když se data o využití hlásí do obchodu, dojde k prodlevě. Azure Stack agreguje data o využití každých 24 hodin a vykazování dat o využití do kanálu pro obchod v Azure trvá několik minut. Proto se může v Azure během následujícího dne objevit využití, ke kterému dojde krátce před půlnocí.

## <a name="generate-usage-data-reporting"></a>Generování sestav dat využití

- Pokud chcete testovat vytváření sestav dat o využití, vytvořte v Azure Stack několik prostředků. Můžete například vytvořit [účet úložiště](azure-stack-provision-storage-account.md), [virtuální počítač s Windows serverem](../user/azure-stack-create-vm-template.md)a virtuální počítač Linux se základními a standardními SKU, abyste viděli, jak se nahlásí základní využití. Data o využití pro různé typy prostředků se nahlásí v různých měřičích.

- Ponechte své prostředky běžet po dobu několik hodin. Informace o použití jsou shromažďovány přibližně jednou za hodinu. Po shromáždění se tato data přenáší do Azure a zpracují do systému Azure Commerce. Tento proces může trvat až několik hodin.

## <a name="view-usage---csp-subscriptions"></a>Zobrazení využití – předplatná CSP

Pokud jste Azure Stack zaregistrovali pomocí předplatného CSP, můžete si prohlédnout své využití a poplatky stejným způsobem jako při zobrazení využití Azure. Ve vaší faktuře a v souboru pro odsouhlasení, který je k dispozici prostřednictvím [partnerského centra](https://partnercenter.microsoft.com/partner/home), je použití Azure Stack. Soubor pro odsouhlasení se aktualizuje měsíčně. Pokud potřebujete získat přístup k nejnovějším informacím o použití Azure Stack, můžete použít rozhraní API partnerského centra.

![partnerské centrum](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>Zobrazení předplatných smlouva Enterprise využití

Pokud jste Azure Stack zaregistrovali pomocí smlouva Enterprise předplatného, můžete si na [portálu EA](https://ea.azure.com/)zobrazit své využití a poplatky. Použití Azure Stack je součástí rozšířených souborů ke stažení společně s využitím Azure v části sestavy na tomto portálu.

## <a name="view-usage---other-subscriptions"></a>Zobrazit využití – ostatní předplatná

Pokud jste Azure Stack zaregistrovali pomocí jiného typu předplatného; například předplatné s průběžnými platbami vám umožní zobrazit využití a poplatky v Centrum účtů Azure. Přihlaste se k [centrum účtů Azure](https://account.windowsazure.com/subscriptions) jako správce účtu Azure a vyberte předplatné Azure, které jste použili k registraci Azure Stack. Můžete zobrazit data o využití Azure Stack, což je množství účtované za jednotlivé využité prostředky, jak je znázorněno na následujícím obrázku:

![tok fakturace](media/azure-stack-usage-reporting/pricing-details.png)

Pro ASDK se neúčtují Azure Stack prostředky, takže uvedená cena je $0,00.

## <a name="which-azure-stack-deployments-are-charged"></a>Které Azure Stack nasazení se účtují?

Využití prostředků je pro ASDK zdarma. Azure Stack systémy s více uzly, virtuální počítače s úlohami, služby úložiště a App Services se účtují.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Účtují se uživatelé za virtuální počítače infrastruktury?

Ne. Data o využití některých Azure Stackch virtuálních počítačů poskytovatele prostředků se nahlásí do Azure, ale pro tyto virtuální počítače se neúčtují žádné poplatky, ani pro virtuální počítače vytvořené během nasazení za účelem povolení Azure Stack infrastruktury.  

Uživatelům se účtují jenom virtuální počítače, které běží v předplatných tenanta. Všechny úlohy musí být nasazené v rámci předplatných tenantů, aby byly v souladu s licenčními podmínkami Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Mám licenci na Windows Server, kterou chci použít na Azure Stack, jak to mám udělat?

Používání stávajících licencí zabraňuje vygenerování měřičů využití. Existující licence na Windows Server se dají použít v Azure Stack, jak je popsáno v části použití existujícího softwaru s Azure Stack v [průvodci Azure Stack licencování](https://go.microsoft.com/fwlink/?LinkId=851536). Aby mohli zákazníci používat své stávající licence, musí nasadit své virtuální počítače s Windows serverem, jak je popsáno v tématu [hybridní zvýhodněná licence pro Windows Server](/azure/virtual-machines/windows/hybrid-use-benefit-licensing).

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Které předplatné se účtuje za spotřebované prostředky?

Účtuje se předplatné, které je k dispozici při [registraci Azure Stack s Azure](azure-stack-registration.md) .

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Jaké typy předplatných se podporují pro vytváření sestav dat o využití?

Pro Azure Stack podporují více uzlů smlouva Enterprise (EA) a CSP. Pro Azure Stack Development Kit, smlouva Enterprise (EA), platby s průběžnými platbami, CSP a předplatnými MSDN podpora pro generování sestav dat využití.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Funguje vytváření sestav údajů o využití v cloudech z svrchovaného díla?

V Azure Stack Development Kit vytváření sestav dat o využití vyžaduje předplatná, která jsou vytvořená v globálním systému Azure. Předplatná vytvořená v jednom z těchto cloudů z svrchovaného cloudu (Azure Government, Azure Německo a Azure Čína 21Vianet cloudy) nejde registrovat v Azure, takže nepodporují generování sestav dat o využití.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Proč se použití hlášené v Azure Stack neshoduje se sestavou vygenerovanou Centrum účtů Azure?

Data o využití uvedená v rozhraních API pro Azure Stack využití a data o využití zaznamenaná v Centrum účtů Azure vždy platí zpoždění. Toto zpoždění je čas potřebný k odeslání údajů o využití z Azure Stack do služby Azure Commerce. V rámci této prodlevy se může v Azure zobrazit následující den použití, ke kterému dojde krátce před půlnocí. Pokud používáte [rozhraní API využití Azure Stack](azure-stack-provider-resource-api.md)a porovnáte výsledky s využitím uvedeným na fakturačním portálu Azure, uvidíte rozdíl.

## <a name="next-steps"></a>Další kroky

- [Rozhraní API využití zprostředkovatele](azure-stack-provider-resource-api.md)  
- [Rozhraní API využití tenanta](azure-stack-tenant-resource-usage-api.md)
- [Nejčastější dotazy k využití](azure-stack-usage-related-faq.md)
- [Správa využití a fakturace jako poskytovatel cloudových řešení](azure-stack-add-manage-billing-as-a-csp.md)
