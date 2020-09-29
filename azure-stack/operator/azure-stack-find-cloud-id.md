---
title: Najít ID cloudu
description: Jak najít ID cloudu v centru pro podporu centra Azure Stack a podpoře.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 647f731d1d82c41d26142ff88e56b8ec0717f5ef
ms.sourcegitcommit: dbc6739584aa407b26e4ad4921d967b7b608de38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2020
ms.locfileid: "90038807"
---
# <a name="find-your-cloud-id"></a>Najít ID cloudu

Toto téma popisuje, jak získat ID cloudu pomocí portálu pro správu nebo privilegovaného koncového bodu (PEP). ID cloudu je jedinečné ID pro sledování dat podpory nahraných z konkrétní jednotky škálování. Po nahrání diagnostických protokolů pro analýzu podpory je ID cloudu způsob, jakým jsou protokoly přidružené k této jednotce škálování.

## <a name="use-the-administrator-portal"></a>Použití portálu pro správu

1. Otevřete portál pro správu. 
1. Vyberte **Správa oblastí**.

   ![Snímek obrazovky s řídicím panelem](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Vyberte **vlastnosti** a zkopírujte **ID cloudu razítko**.

   ![Snímek obrazovky s vlastnostmi oblasti s ID cloudového razítka](media/azure-stack-automatic-log-collection/region-properties-blade-with-stamp-cloud-id.png)


## <a name="use-the-privileged-endpoint"></a>Použití privilegovaného koncového bodu

1. Otevřete relaci PowerShellu se zvýšenými oprávněními a spusťte následující skript. V případě potřeby nahraďte IP adresu virtuálního počítače PEP a přihlašovací údaje správce cloudového prostředí. 

   ```powershell
   $ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

   $password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
   $cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)
   $session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

   $stampInfo = Invoke-Command -Session $session { Get-AzureStackStampInformation }
   if ($session) {
       Remove-PSSession -Session $session
   }

   $stampInfo.CloudID
   ```

## <a name="next-steps"></a>Další kroky

* [Proaktivní odesílání protokolů](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)
* [Poslat protokoly hned](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002)
