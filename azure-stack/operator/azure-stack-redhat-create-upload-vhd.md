---
title: Nabídnout virtuální počítač se systémem Red Hat pro Azure Stack hub
titleSuffix: Azure Stack Hub
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém Red Hat Linux.
author: sethmanheim
ms.topic: article
ms.date: 2/18/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 2/18/2021
ms.openlocfilehash: 4dc1280b0120c5c64dff6a273ce13ae97dbe980c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840299"
---
# <a name="offer-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>Nabídnout virtuální počítač se systémem Red Hat pro Azure Stack hub

Tento článek popisuje, jak připravit Red Hat Enterprise Linux virtuální počítač pro použití v centru Azure Stack. 

## <a name="offer-a-red-hat-based-vm"></a>Nabídka virtuálního počítače se systémem Red Hat

Existují dva způsoby, jak můžete v centru Azure Stack nabízet virtuální počítače s Red Hat:

- Nabídku můžete přidat prostřednictvím tržiště centra Azure Stack.
    - Red Hat Enterprise Linux obrázky jsou privátní nabídka v centru Azure Stack. K zpřístupnění této nabídky na kartě **správy na webu Marketplace** budete muset [provést průzkum](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR_e32WQju3tMrgXNcUR94AVUNkJTWjdQRjc3TzFLREdGU0dIVFRUQ1JCSi4u).
    - Další informace najdete v tématu [Přehled služby Azure Stack hub Marketplace](azure-stack-marketplace.md).
- Můžete přidat vlastní možnost do centra Azure Stack a pak na webu Marketplace nabídnout image. 
    1. Budete muset mít k dispozici cloudový přístup Red Hat.
    2. Pokyny k přípravě image pro Azure a centra Azure Stack najdete v tématu [Příprava virtuálního počítače založeného na Red Hat pro Azure](/azure/virtual-machines/linux/redhat-create-upload-vhd).
    3. Pokyny k nabízení vlastní image v tržišti centra Azure Stack najdete v tématu [Vytvoření a publikování položky Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Další kroky

Další informace o hypervisorech, které jsou certifikované pro spouštění Red Hat Enterprise Linux, najdete [na webu Red Hat](https://access.redhat.com/certified-hypervisors).