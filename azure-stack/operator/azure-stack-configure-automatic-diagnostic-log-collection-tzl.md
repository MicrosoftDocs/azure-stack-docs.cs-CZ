---
title: Proaktivně shromažďovat diagnostické protokoly, když se na Azure Stack centru vygeneruje výstraha
description: Jak nakonfigurovat proaktivní shromažďování protokolů v centru Azure Stack Help a podpora.
author: justinha
ms.topic: article
ms.date: 02/12/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/12/2020
ms.openlocfilehash: 0c6bfe4dba2b3795e1069419d90624c8e3b55cda
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520262"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>Proaktivně odesílat diagnostické protokoly centra Azure Stack

Můžete ušetřit čas u zákaznické podpory tím, že aktivně shromažďujete diagnostické protokoly, když se na Azure Stack centru vygeneruje výstraha.
Pokud je potřeba prozkoumat stav systému, protokoly je možné nahrát automaticky pro analýzu před otevřením případu podpory se službou zákaznické podpory Microsoftu (CSS). 

## <a name="steps-to-configure-proactive-log-collection"></a>Postup konfigurace proaktivní kolekce protokolů

Pomocí těchto kroků můžete nakonfigurovat proaktivní shromažďování protokolů. Automatické shromažďování protokolů lze kdykoli zakázat a znovu povolit.  

1. Přihlaste se k portálu pro správu centra Azure Stack.
1. Otevřete okno **pomoc a podpora – přehled**.
1. Pokud se zobrazí nápis, klikněte na **Povolit proaktivní shromažďování protokolů**. 

   ![Snímek obrazovky s informacemi o tom, kde Povolit shromažďování protokolů v nápovědě a podpoře](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


   Případně můžete kliknout na **Nastavení** a nastavit **aktivní kolekci protokolů** na **Povolit** a kliknout na **Uložit**.

   ![Snímek obrazovky s informacemi o tom, kde Povolit shromažďování protokolů v nápovědě a podpoře](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)


## <a name="view-log-collection"></a>Zobrazit kolekci protokolů

Historie protokolů shromážděných z centra Azure Stack se zobrazí na stránce **shromažďování protokolů** v nápovědě a podpoře s následujícími daty a časy:

- **Čas shromažďování**: při zahájení operace shromažďování protokolů
- **Stav**: buď probíhá, nebo dokončeno
- **Zahajte zápisy**: začátek časového období, které chcete shromáždit.
- **Konec protokolů**: konec časového období
- **Typ**: Pokud se jedná o manuální nebo proaktivní kolekci protokolů 


![Snímek obrazovky se zobrazením kolekcí protokolů](media/azure-stack-help-and-support/azure-stack-log-collection.png)


## <a name="proactive-diagnostic-log-collection-alerts"></a>Výstrahy shromažďování proaktivní diagnostického protokolu 

Je-li povoleno, služba proaktivní shromažďování protokolů odesílá protokoly pouze v případě, že je vyvolána jedna z následujících událostí. 

Například **Chyba aktualizace** je výstraha, která spouští proaktivní shromažďování protokolů diagnostiky. Pokud je tato možnost povolena, diagnostické protokoly budou během chyby aktualizace aktivně zachyceny, aby bylo možné potíže vyřešit pomocí šablony stylů CSS. Diagnostické protokoly jsou shromažďovány pouze v případě, že se vyvolá výstraha pro **aktualizaci** . 

|Název výstrahy  | FaultIdType|    
|-------------|------------|
|Nejde se připojit ke vzdálené službě |  UsageBridge.NetworkError|
|Aktualizace se nezdařila |    Urp.UpdateFailure   |          
|Infrastruktura nebo závislosti poskytovatele prostředků úložiště nejsou k dispozici. |  StorageResourceProviderDependencyUnavailable     |     
|Uzel není připojený k řadiči.|  ServerHostNotConnectedToController   |     
|Selhání publikování trasy |    SlbMuxRoutePublicationFailure | 
|Interní úložiště dat poskytovatele prostředků úložiště není dostupné. |    StorageResourceProvider. DataStoreConnectionFail     |       
|Selhání úložného zařízení | Microsoft. Health. typ FaultType. VirtualDisks. odpojilo se   |      
|Kontroler stavu nemá přístup k účtu úložiště | Microsoft. Health. typ FaultType. StorageError |    
|Připojení k fyzickému disku bylo ztraceno. |    Microsoft. Health. typ FaultType. fyzický disk. LostCommunication    |    
|Služba BLOB Service není spuštěná na uzlu. | StorageService. blob. Service. is. not. Running. on. Node-Critical | 
|Role infrastruktury není v pořádku. |    Microsoft. Health. typ FaultType. GenericExceptionFault |        
|Chyby služby Table service | StorageService. Table. Service. Errors – kritický |              
|Sdílená složka je větší než 80% využití. |    Microsoft. Health. typ FaultType. sdílené složky. Capacity. Warning. |       
|Uzel jednotky škálování je offline | FRP. Prezenční signál. PhysicalNode |  
|Instance role infrastruktury není dostupná. | FRP. Prezenční signál. InfraVM   |    
|Instance role infrastruktury není dostupná.  |    FRP. Prezenční signál. NonHaVm     |        
|Role infrastruktury, Správa adresářů, ohlásila chyby synchronizace času. |  DirectoryServiceTimeSynchronizationError |     
|Blížící se vypršení platnosti externího certifikátu |  CertificateExpiration. ExternalCert. Warning |
|Blížící se vypršení platnosti externího certifikátu |  CertificateExpiration. ExternalCert. Critical |
|Pro konkrétní třídu a velikost nejde zřídit virtuální počítače kvůli nedostatečné kapacitě paměti |  AzureStack. ComputeController. VmCreationFailure. LowMemory |
|Nedostupný uzel pro umístění virtuálního počítače |  AzureStack. ComputeController. HostUnresponsive | 
|Zálohování nebylo úspěšné.  | AzureStack. BackupController. BackupFailedGeneralFault |    
|Naplánované zálohování bylo přeskočeno z důvodu konfliktu s neúspěšnými operacemi.  | AzureStack. BackupController. BackupSkippedWithFailedOperationFault |   


## <a name="see-also"></a>Viz také

[Azure Stack zpracování dat protokolů a zákazníků centra](azure-stack-data-collection.md)





