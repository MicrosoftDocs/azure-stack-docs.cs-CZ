---
title: Odstranění svazků v Azure Stack HCI
description: Postup odstranění svazků v Azure Stack HCI pomocí centra pro správu systému Windows.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/05/2020
ms.openlocfilehash: ab99ed7a49fe2bf003245f139451895a85c4edbf
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025444"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Odstraňování svazků v Azure Stack HCI

> Platí pro: Windows Server 2019

Toto téma poskytuje pokyny pro odstranění svazků v [prostory úložiště s přímým přístupemm](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) clusteru pomocí centra pro správu systému Windows.

Podívejte se na rychlé video o tom, jak svazek odstranit.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Použití centra pro správu systému Windows k odstranění svazku

1. V centru pro správu Windows se připojte ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** a potom vyberte svazek, který chcete odstranit.
3. V horní části stránky s podrobnostmi o svazcích vyberte možnost **Odstranit**.
4. V dialogovém okně potvrzení zaškrtněte políčko pro potvrzení, že chcete svazek odstranit, a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Podrobné pokyny pro další důležité úlohy správy úložiště najdete tady:

- [Plánování svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/plan-volumes)
- [Vytváření svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/create-volumes)
- [Rozšíření svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/resize-volumes)