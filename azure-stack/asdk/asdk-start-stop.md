---
title: Spuštění a zastavení ASDK
description: Naučte se spouštět a zastavovat Azure Stack Development Kit (ASDK).
author: justinha
ms.topic: article
ms.date: 07/18/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 6798481e7645c4a1a49c6370b00b1851c32ff478
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "79511976"
---
# <a name="start-and-stop-the-asdk"></a>Spuštění a zastavení ASDK
Nedoporučuje se jednoduše restartovat hostitelský počítač ASDK. Místo toho postupujte podle pokynů v tomto článku, abyste správně vypnuli a restartovali ASDK služby.

## <a name="stop-azure-stack"></a>Zastavit Azure Stack 
Pro správné vypnutí služby Azure Stack Services a hostitelského počítače ASDK použijte následující příkazy PowerShellu:

1. Přihlaste se jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (ne PowerShell ISE).
3. Spuštěním následujících příkazů vytvořte relaci privilegovaného koncového bodu (PEP): 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. Potom v relaci PEP pomocí rutiny **stop-AzureStack** zastavíte Azure Stack služby a vypnete hostitelský počítač ASDK:

   ```powershell
   Stop-AzureStack
   ```
5. Zkontrolujte výstup prostředí PowerShell a ujistěte se, že jsou všechny Azure Stack služby úspěšně vypnuté, než se hostitelský počítač ASDK vypne. Proces vypnutí trvá několik minut.

## <a name="start-azure-stack"></a>Spustit Azure Stack 
Služby ASDK by se měly spustit automaticky při spuštění hostitelského počítače. Čas spuštění služby infrastruktury ASDK se ale liší v závislosti na výkonu hardwarové konfigurace hostitelského počítače ASDK. V některých případech může trvat několik hodin, než se všechny služby úspěšně restartují.

Bez ohledu na to, jak byl ASDK vypnutý, byste měli pomocí následujících kroků ověřit, jestli jsou všechny služby Azure Stack spuštěné a plně funkční po zapnutí hostitelského počítače: 

1. Zapněte počítač hostitele ASDK. 
2. Přihlaste se jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.
3. Otevřete PowerShell jako správce (ne PowerShell ISE).
4. Spuštěním následujících příkazů vytvořte relaci privilegovaného koncového bodu (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. V dalším kroku v relaci PEP spusťte následující příkazy, abyste zkontrolovali stav spouštění služby Azure Stack Services:

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. Zkontrolujte výstup a ujistěte se, že se služba Azure Stack Services úspěšně restartovala.

Další informace o doporučených postupech pro správné vypnutí a restartování služeb Azure Stack najdete v tématu [spuštění a zastavení Azure Stack](../operator/azure-stack-start-and-stop.md).

## <a name="troubleshoot-startup-and-shutdown"></a>Řešení potíží při spuštění a vypnutí 
Postupujte podle těchto kroků, pokud se Azure Stack služby nespustí do dvou hodin po zapnutí počítače s hostitelským počítačem s ASDK:

1. Přihlaste se jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (ne PowerShell ISE).
3. Spuštěním následujících příkazů vytvořte relaci privilegovaného koncového bodu (PEP):

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. V dalším kroku v relaci PEP spusťte následující příkazy, abyste zkontrolovali stav spouštění služby Azure Stack Services:

   ```powershell
   Test-AzureStack
   ```
5. Zkontrolujte výstup a vyřešte všechny chyby. Další informace najdete v tématu [spuštění ověřovacího testu Azure Stack](../operator/azure-stack-diagnostic-test.md).
6. Restartování služby Azure Stack v rámci relace PEP spuštěním rutiny **Start-AzureStack** :

   ```powershell
   Start-AzureStack
   ```

Pokud při spuštění **Start-AzureStack** dojde k chybě, navštivte [fórum podpory Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack) , kde můžete získat podporu pro řešení potíží s ASDK. 

## <a name="next-steps"></a>Další kroky 
Další informace o nástroji pro diagnostiku Azure Stack a o protokolování problémů najdete v tématu [Azure Stack diagnostické nástroje](../operator/azure-stack-get-azurestacklog.md).
