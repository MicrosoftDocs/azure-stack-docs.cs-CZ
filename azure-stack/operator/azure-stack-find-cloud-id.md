---
title: Najít ID cloudu
description: Jak najít ID cloudu v centru pro podporu centra Azure Stack a podpoře.
author: myoungerman
ms.topic: article
ms.date: 10/08/2019
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: e14a2a000b52f09f664112467ce8694134243681
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778234"
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

* [Proaktivní odesílání protokolů](./azure-stack-diagnostic-log-collection-overview.md#send-logs-proactively)
* [Poslat protokoly hned](./azure-stack-diagnostic-log-collection-overview.md#send-logs-now)
