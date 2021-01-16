---
title: Najít ID cloudu
description: Jak najít ID cloudu v centru pro podporu centra Azure Stack a podpoře.
author: PatAltimore
ms.topic: article
ms.date: 10/08/2019
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 6da70761e8a37967d3debdff28b67447596c632a
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255958"
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

* [Proaktivní odesílání protokolů](./diagnostic-log-collection.md#send-logs-proactively)
* [Poslat protokoly hned](./diagnostic-log-collection.md#send-logs-now)