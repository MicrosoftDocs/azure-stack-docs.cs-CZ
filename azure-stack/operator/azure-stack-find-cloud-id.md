---
title: Najít ID cloudu
description: Jak najít ID cloudu v centru pro podporu centra Azure Stack a podpoře.
author: justinha
ms.topic: article
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e0045ae6bf76b6b4e5973f65c6c0a5f758e0d46b
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520405"
---
# <a name="find-your-cloud-id"></a>Najít ID cloudu

Toto téma popisuje, jak získat ID cloudu pomocí portálu pro správu nebo privilegovaného koncového bodu (PEP). 

## <a name="use-the-administrator-portal"></a>Použití portálu pro správu

1. Otevřete portál pro správu. 
1. Klikněte na **Správa oblastí**.

   ![Snímek obrazovky s řídicím panelem](./media/azure-stack-automatic-log-collection/dashboard.png)

1. Klikněte na **vlastnosti** a zkopírujte **ID cloudového razítka**.

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

* [Proaktivní odesílání protokolů](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)
* [Poslat protokoly hned](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)






