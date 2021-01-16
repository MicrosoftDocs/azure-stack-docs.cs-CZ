---
title: Přehled nasazení Azure Stack HCI
description: Přehled procesu nasazení Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/11/2020
ms.openlocfilehash: 641615aafd8531cf31e326a2f829b0bf5f5c7a12
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254751"
---
# <a name="what-is-the-deployment-process-for-azure-stack-hci"></a>Jaký proces nasazení se používá pro Azure Stack HCI?

> Platí pro: Azure Stack HCI, verze 20H2

Toto téma obsahuje podrobný podrobný přehled Azure Stack procesu nasazení HCI s odkazy na podrobnější informace.

## <a name="plan"></a>Plánování

Před nasazením pečlivě Naplánujte úložiště, sítě a požadavky pro clustering s více lokalitami (pokud existuje).

### <a name="plan-storage"></a>Plánování úložiště

Azure Stack HCI používá standardní servery s připojenými jednotkami k vytvoření vysoce dostupného a vysoce škálovatelného úložiště definovaného softwarem. Abyste splnili požadavky na výkon a kapacitu, pečlivě [vybírejte jednotky](../concepts/choose-drives.md) a [Naplánujte svazky](../concepts/plan-volumes.md).

### <a name="plan-networking"></a>Plánování sítí

Poznamenejte si názvy serverů, názvy domén, protokoly RDMA a verze a ID sítě VLAN pro vaše nasazení.

### <a name="plan-stretched-clusters"></a>Plánování roztaženého clusteru

Pokud je nasazení Azure Stack HCI roztaženo mezi více lokalit, určete, kolik serverů budete potřebovat v každé lokalitě a zda bude konfigurace clusteru aktivní/pasivní nebo aktivní/aktivní. Další informace najdete v tématu [o roztaženém clusteru](../concepts/stretched-clusters.md).

## <a name="deploy"></a>Nasazení

Před nasazením operačního systému Zjistěte, jestli hardware splňuje požadavky na [systém](../concepts/system-requirements.md) pro Azure Stack HCI. Pak [nainstalujte centrum pro správu systému Windows](/windows-server/manage/windows-admin-center/deploy/install) , ve kterém můžete spravovat cluster Azure Stack HCI.

### <a name="1-deploy-azure-stack-hci"></a>1. nasazení Azure Stack HCL

[Stáhněte si Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) a nasaďte Azure Stack [operační systém](operating-system.md) HCI na každém serveru, který chcete clusterovat. Pokud jste si koupili Azure Stack hardware integrovaného systémového řešení z [katalogu Azure Stack HCI](https://hcicatalog.azurewebsites.net) přes preferovaný hardwarový partner společnosti Microsoft, měl by být operační systém Azure Stack HCI předem nainstalován. V takovém případě můžete tento krok přeskočit a přejít na #2.

### <a name="2-create-the-cluster"></a>2. vytvoření clusteru

Vytvořte cluster s podporou převzetí služeb při selhání pomocí [centra pro správu Windows](create-cluster.md) nebo [PowerShellu](create-cluster-powershell.md). Pro nativní zotavení po havárii a kontinuitu podnikových aplikací můžete nasadit [roztažené clustery](../concepts/stretched-clusters.md) , které budou zahrnovat dvě geograficky oddělené lokality.

### <a name="3-set-up-a-cluster-witness"></a>3. nastavení určujícího clusteru

[Nastavení prostředku určujícího zdroje](witness.md) je pro všechny clustery povinné. Clustery se dvěma uzly potřebují určující určující, aby jeden server v režimu offline nezpůsobí, že druhý uzel nebude k dispozici také. Clustery se třemi a vyššími uzly potřebují určující, aby bylo možné vydržet dva servery, které jsou neúspěšné nebo offline. 

### <a name="4-register-with-azure"></a>4. Zaregistrujte se do Azure

Azure Stack HCI vyžaduje připojení k Azure. Pokud chcete připojit svůj cluster k Azure, přečtěte si téma [registrace Azure Stack HCL pomocí Azure](register-with-azure.md). Po registraci se cluster automaticky připojí na pozadí.

### <a name="5-validate-the-cluster"></a>5. ověření clusteru

Po vytvoření a registraci clusteru [Spusťte testy pro ověření clusteru](validate.md) , abyste zachytili problémy s hardwarem nebo konfigurací předtím, než cluster přejde do produkčního prostředí.

### <a name="6-deploy-storage"></a>6. nasazení úložiště

[Vytvořte svazky](../manage/create-volumes.md) v clusteru s jednou lokalitou nebo [vytvořte svazky a nastavte replikaci na roztaženém clusteru](../manage/create-stretched-volumes.md).

### <a name="7-deploy-workloads"></a>7. nasazení úloh

Nyní jste připraveni nasadit úlohy do Azure Stack HCI pomocí centra pro správu systému Windows.

Podrobnosti o tom, jak začít s nasazením úlohy služby Azure Kubernetes na Azure Stack HCI, najdete v tomto přehledu: [co je Azure Kubernetes Service on Azure Stack HCI?](../../aks-hci/overview.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nasadit operační systém Azure Stack HCI.

> [!div class="nextstepaction"]
> [Nasazení operačního systému Azure Stack HCI](operating-system.md)