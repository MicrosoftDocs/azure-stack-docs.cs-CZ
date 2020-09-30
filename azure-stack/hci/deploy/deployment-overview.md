---
title: Přehled nasazení Azure Stack HCI
description: Přehled procesu nasazení Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/09/2020
ms.openlocfilehash: 34a93a65d45861c7c7ff1727347cc95465968151
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572513"
---
# <a name="what-is-the-deployment-process-for-azure-stack-hci"></a>Jaký je proces nasazení Azure Stack HCI?

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

## <a name="deploy"></a>Nasadit

### <a name="1-before-you-begin"></a>1. než začnete

Než začnete, [Určete, jestli hardware splňuje základní požadavky, a shromážděte informace potřebné](before-you-start.md) k nasazení Azure Stack HCI. Pak [nainstalujte centrum pro správu systému Windows](/windows-server/manage/windows-admin-center/deploy/install) , ve kterém můžete spravovat cluster Azure Stack HCI.

### <a name="2-deploy-azure-stack-hci"></a>2. nasazení Azure Stack HCL

[Stáhněte si Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) a nasaďte Azure Stack [operační systém](operating-system.md) HCI na každém serveru, který chcete clusterovat.

### <a name="3-create-the-cluster"></a>3. vytvoření clusteru

Vytvořte cluster s podporou převzetí služeb při selhání pomocí [centra pro správu Windows](create-cluster.md) nebo [PowerShellu](create-cluster-powershell.md). Pro nativní zotavení po havárii a kontinuitu podnikových aplikací můžete nasadit [roztažené clustery](../concepts/stretched-clusters.md) , které budou zahrnovat dvě geograficky oddělené lokality.

### <a name="4-set-up-a-cluster-witness"></a>4. nastavení určujícího clusteru

[Nastavení prostředku určujícího zdroje](witness.md) je pro všechny clustery povinné. Clustery se dvěma uzly potřebují určující určující, aby jeden server v režimu offline nezpůsobí, že druhý uzel nebude k dispozici také. Clustery se třemi a vyššími uzly potřebují určující, aby bylo možné vydržet dva servery, které jsou neúspěšné nebo offline. 

### <a name="5-register-with-azure"></a>5. Zaregistrujte se do Azure

Azure Stack HCI vyžaduje připojení k Azure. Pokud chcete připojit svůj cluster k Azure, přečtěte si téma [registrace Azure Stack HCL pomocí Azure](register-with-azure.md). Po registraci se cluster automaticky připojí na pozadí.

### <a name="6-validate-the-cluster"></a>6. ověření clusteru

Po vytvoření a registraci clusteru [Spusťte testy pro ověření clusteru](validate.md) , abyste zachytili problémy s hardwarem nebo konfigurací předtím, než cluster přejde do produkčního prostředí.

### <a name="7-deploy-storage"></a>7. nasazení úložiště

[Vytvořte svazky](../manage/create-volumes.md) v clusteru s jednou lokalitou nebo [vytvořte svazky a nastavte replikaci na roztaženém clusteru](../manage/create-stretched-volumes.md).

### <a name="8-deploy-workloads"></a>8. nasazení úloh

Nyní jste připraveni [vytvořit virtuální počítače](../manage/vm.md) a nasazovat úlohy do Azure Stack HCI pomocí centra pro správu Windows.

## <a name="next-steps"></a>Další kroky

Přečtěte si, co je potřeba udělat před nasazením Azure Stack HCL.

> [!div class="nextstepaction"]
> [Před zahájením](before-you-start.md)
