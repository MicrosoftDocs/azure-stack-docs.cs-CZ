---
title: Využití sestav infrastruktury pro poskytovatele cloudových služeb pro Azure Stack | Dokumentace Microsoftu
description: Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití u klientů, které jsou obsluhovány pomocí Cloud Service Provider (CSP) nastane a předá ji do Azure.
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
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: ca474c9addeec3b3b6fcaad8db55bc40a148bb8f
ms.sourcegitcommit: c755c7eac0f871960f9290591421cf5990b9e734
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506103"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Využití sestav infrastruktury pro poskytovatele cloudových služeb

Azure Stack zahrnuje infrastrukturu potřebnou ke sledování využití, protože dojde k a předá ji do Azure. V Azure Azure Commerce zpracovává data o využití a poplatky za využití pro příslušné předplatné Azure. K tomu dojde za stejným způsobem jako sledování využití se sleduje v globální cloud Azure.

Některé koncepty jsou konzistentní mezi globální Azure a Azure Stack. Azure Stack je místní odběry, které splňují podobnou roli s předplatným Azure. Místní odběry jsou platné pouze místně. Místní odběry se mapují na předplatná Azure, když využití je předán do Azure.

Azure Stack je místní využití měřičů. Místní využití je namapována na měřiče použít v Azure commerce. ID měřiče se však liší. K dispozici další měřiče místně než ten, který společnost Microsoft používá pro účely fakturace.

Existuje několik rozdílů mezi jaké jsou ceny za služby ve službě Azure Stack a Azure. Například ve službě Azure Stack, poplatky za virtuální počítače je jenom na základě hodiny jader vcore /, se stejná sazba pro všechny řady virtuálních počítačů, na rozdíl od Azure. Důvodem je, že globální Azure různé ceny odrážejí jiný hardware. Ve službě Azure Stack poskytuje zákazník hardware, proto neexistuje žádný důvod platby prostřednictvím různých účtovat sazby platné pro různé třídy virtuálního počítače.

Můžete získat informace o měřiče služby Azure Stack, který je používán obchodování a jejich cenu v partnerském centru stejným způsobem jako u služby Azure:

1. V partnerském centru, přejděte **nabídce řídicího panelu** > **cenami a nabídkami**.
2. V části **založená na využití služby**vyberte **aktuální**.
3. Otevřít **Azure v ceníku CSP globální** tabulky.
4. Filtrovat podle **oblasti = Azure Stack**.

## <a name="usage-and-billing-error-codes"></a>Využití a fakturace kódy chyb

Následujících chybových zpráv může nastat při přidání klienty na registraci.

| Chyba                           | Podrobnosti                                                                                                                                                                                                                                                                                                                           | Komentáře                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **RegistrationNotFound**            | Zadaný registrační nebyl nalezen. Ujistěte se, že byla správně zadat následující informace:<br>1. Identifikátor předplatného (poskytnutá hodnota: _identifikátor předplatného_),<br>2. Skupina prostředků (poskytnutá hodnota: _skupiny prostředků_),<br>3. Registrace – název (poskytnutá hodnota: _registrace – název_).                             | K této chybě obvykle dochází, když informace o počáteční registraci není správný. Pokud je potřeba ověřit skupinu prostředků a název svou registraci, najdete ho na webu Azure Portal, uvedením všechny prostředky. Pokud zjistíte více než jeden prostředek registrace, podívejte se na **CloudDeploymentID** vlastnosti a vyberte registraci jehož **CloudDeploymentID** odpovídá vašeho cloudu. Najít **CloudDeploymentID**, můžete použít tento příkaz prostředí PowerShell ve službě Azure Stack:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| **BadCustomerSubscriptionId**       | Poskytnuté _identifikátor předplatného zákazníka_ a _registrace – název_ identifikátor předplatného nejsou vlastněny stejné poskytovatele cloudové služby Microsoftu. Zkontrolujte, zda je správný identifikátor předplatného zákazníka. Pokud se problém nevyřeší, obraťte se na podporu. | Tato chyba nastane, pokud zákaznické předplatné je předplatném CSP, ale jeho shrnuje CSP partner odlišné od těch, ke kterému shrnuje předplatného použité v počáteční registraci. Tato kontrola se provádí, aby se zabránilo situaci, která by za následek účtování partnerem CSP, který není zodpovědná za Azure Stack, používá.                                                                                                                                                                                                                                                                          |
| **InvalidCustomerSubscriptionId**   | "_Identifikátor předplatného zákazníka_' není platný. Ujistěte se, že je k dispozici platné předplatné Azure.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **CustomerSubscriptionNotFound**    | _Identifikátor předplatného zákazníka_ nebyl nalezen v rámci _registrace – název_. Ujistěte se, že se používá platné předplatné Azure a že ID předplatného byla přidána do registrace pomocí operace PUT.                                                   | K této chybě dochází při pokusu o závažnost neshody tenanta se přidala do předplatného, který nebyl nalezen předplatné zákazníka má být přidružena k registraci. Zákazník nebyla přidána k registraci nebo ID předplatného je napsán správně.                                                                                                                                                                                                                                                                                                                                |
| **UnauthorizedCspRegistration**     | Poskytnuté _registrace – název_ není schválený pro použití více tenantů. Odeslání e-mailu azstCSP@microsoft.com a zahrnují vaší registrace – název, skupinu prostředků a předplatném identifikátor použitý v registraci.                                                                                    | Registrace musí být schválen pro více tenantů společností Microsoft před zahájením přidání tenantů do něj.                                                                                                                                                                                                                                                                                                                                                                                             |
| **CustomerSubscriptionsNotAllowed** | Odpojené zákazníkům se nepodporují operace předplatného zákazníka. Aby bylo možné tuto funkci použít, znovu zaregistrovat platit jako můžete použít licencí.                                                                                                                                                                    | Registrace, ke kterému se pokoušíte přidat klienty je kapacita registrace; To znamená, když byla vytvořena registrace, parametr `BillingModel Capacity` byl použit. Přidání tenantů jsou povoleny pouze platit jako využití registrace. Je nutné znovu zaregistrovat pomocí parametru `BillingModel PayAsYouUse`.                                                                                                                                                                                                                                                                                          |
| **InvalidCSPSubscription**          | Poskytnuté _identifikátor předplatného zákazníka_ není platné předplatné poskytovatele CSP. Ujistěte se, že je k dispozici platné předplatné Azure.                                                                                                                                                        | Příčinou je pravděpodobně z důvodu zákaznického předplatného se chybně zadaná.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **MetadataResolverBadGatewayError** | Některý nadřazený server vrátil neočekávanou chybu. Zkuste to znovu později. Pokud se problém nevyřeší, obraťte se na podporu.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Termíny používané pro fakturaci a využití

Následující terminologie a Koncepty jsou použity pro využití a fakturace ve službě Azure Stack:

| Termín | Definice |
| --- | --- |
| Přímé partner CSP | Přímé partner Cloud Service Provider (CSP) obdrží fakturu přímo od společnosti Microsoft pro Azure a Azure Stackem využití a faktur zákazníkům přímo. |
| Nepřímé CSP | Nepřímí prodejci pracovat nepřímý poskytovatel (označované také jako distributor). Získávání koncovým zákazníkům; je prodejcům v systému nepřímý poskytovatel má fakturační vztah se společností Microsoft, spravuje fakturace zákazníkům a poskytuje další služby, třeba odborné pomoci. |
| Koncového zákazníka | Koncovým zákazníkům se podniky a instituce státní správy, které vlastní aplikace a další úlohy, které běží ve službě Azure Stack. |

## <a name="next-steps"></a>Další postup

- Další informace o programu CSP najdete v tématu [Cloudová řešení](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](azure-stack-billing-and-chargeback.md).
