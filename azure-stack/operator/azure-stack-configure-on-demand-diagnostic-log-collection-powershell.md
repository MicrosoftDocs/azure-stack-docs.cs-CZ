---
title: Odesílání diagnostických protokolů centra Azure Stack do Azure pomocí privilegovaného koncového bodu (PEP)
description: Naučte se odesílat diagnostické protokoly centra Azure Stack do Azure pomocí privilegovaného koncového bodu (PEP).
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: ca6240cb4f1e54c5e5fbfda79c46697909612063
ms.sourcegitcommit: b2b0fe629d840ca8d5b6353a90f1fcb392a73bd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2020
ms.locfileid: "85377043"
---
# <a name="send-azure-stack-hub-diagnostic-logs-to-azure-using-the-privileged-endpoint-pep"></a>Odesílání diagnostických protokolů centra Azure Stack do Azure pomocí privilegovaného koncového bodu (PEP)

Při použití této metody není nutné zadávat žádné cílové umístění úložiště. Pokud **nejsou zadány parametry** **FromDate a ne** , použije se ve výchozím nastavení poslední 4 hodiny od doby spuštění. 

Volitelně můžete zadat parametr **FilterByRole** nebo **FilterByResourceProvider** , který bude zahrnovat pouze konkrétní role nebo hodnoty-přidat protokoly RP. 

Tady je ukázkový skript, který můžete spustit pomocí PEP a shromažďovat protokoly v integrovaném systému. 


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP address. 
 
$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force 
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password) 
 
$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred 
 
$fromDate = (Get-Date).AddHours(-8) # Optional. 
$toDate = (Get-Date).AddHours(-2) # Optional. Provide the time that includes the period for your issue 
 
Invoke-Command -Session $session {Send-AzureStackDiagnosticLog -FromDate $using:fromDate -ToDate $using:toDate} 
 
if ($session) { 
    Remove-PSSession -Session $session 
} 
```

## <a name="parameter-considerations"></a>Požadavky na parametry 

* Parametry **FromDate** a **ToDate** na více dní lze použít ke shromažďování protokolů pro konkrétní časové období. Nejsou-li tyto parametry zadány, budou ve výchozím nastavení shromažďovány protokoly za poslední čtyři hodiny.

* Pomocí parametru **FilterByNode** můžete filtrovat protokoly podle názvu počítače. Příklad:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* Pomocí parametru **FilterByLogType** můžete filtrovat protokoly podle typu. Můžete zvolit filtrování podle souboru, sdílení nebo WindowsEvent. Příklad:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* Pomocí parametru **FilterByResourceProvider** můžete odesílat diagnostické protokoly pro poskytovatele prostředků s hodnotou Add (RPS). Obecná syntaxe je:
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Postup odesílání diagnostických protokolů pro IoT Hub: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  Postup odesílání diagnostických protokolů pro Event Hubs:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  Postup odesílání diagnostických protokolů pro Azure Stack Edge:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* K odesílání diagnostických protokolů z rolí VirtualMachines a BareMetal použijte parametr **FilterByRole** :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* K odesílání diagnostických protokolů z rolí VirtualMachines a BareMetal s filtrováním data pro soubory protokolů za posledních 8 hodin:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Pro odesílání diagnostických protokolů z rolí VirtualMachines a BareMetal s filtrováním data pro soubory protokolů za časové období před 8 hodinami a před 2 hodinami:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```


## <a name="next-steps"></a>Další kroky

[Použití privilegovaného koncového bodu (PEP) k odesílání diagnostických protokolů centra Azure Stack](azure-stack-get-azurestacklog.md)
