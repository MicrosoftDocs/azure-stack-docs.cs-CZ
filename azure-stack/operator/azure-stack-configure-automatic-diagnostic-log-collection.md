---
title: Konfigurovat automatickou kolekci protokolů Azure Stack | Microsoft Docs
description: Jak nakonfigurovat automatické shromažďování protokolů v Azure Stack nápovědě a podpoře.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 4d6bc431b292fc7a124aa2b8051d0a927d736eee
ms.sourcegitcommit: 4e48f1e5af74712a104eda97757dc5f50a591936
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224955"
---
# <a name="configure-automatic-azure-stack-diagnostic-log-collection"></a>Konfigurace automatického shromažďování protokolů Azure Stack diagnostiky

*Platí pro: Azure Stack integrovaných systémů*

Pro zjednodušení shromažďování protokolů a možnosti zákaznické podpory doporučujeme nakonfigurovat funkci automatického shromažďování protokolů diagnostiky. Pokud je potřeba prozkoumat stav systému, protokoly je možné automaticky nahrát k analýze prostřednictvím služeb zákaznické podpory Microsoftu (CSS). 

## <a name="create-an-azure-blob-container-sas-url"></a>Vytvoření adresy URL SAS kontejneru objektů BLOB v Azure 

Než budete moct nakonfigurovat automatické shromažďování protokolů, budete muset pro kontejner objektů BLOB získat sdílený přístupový podpis (SAS). SAS umožňuje udělit přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu. Soubory protokolu Azure Stack můžete uložit do kontejneru objektů BLOB v Azure a pak zadat adresu URL SAS, kde může CSS shromažďovat protokoly. 

### <a name="prerequisites"></a>Požadavky

V Azure můžete použít nový nebo existující kontejner objektů BLOB. Pokud chcete vytvořit kontejner objektů BLOB v Azure, potřebujete aspoň [roli Přispěvatel objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) nebo [konkrétní oprávnění](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). Globální správci mají také potřebná oprávnění. 

Osvědčené postupy při volbě parametrů pro účet úložiště automatického shromažďování protokolů najdete v tématu [osvědčené postupy pro automatické shromažďování protokolů Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md). Další informace o typech účtů úložiště najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview) .

### <a name="create-a-blob-storage-account"></a>Vytvoření účtu úložiště BLOB
 
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Klikněte na **účty** > úložiště**Přidat**. 
1. Vytvořte kontejner objektů BLOB s těmito nastaveními:
   - **Předplatné:** Zvolte svoje předplatné Azure.
   - **Skupina prostředků**: Zadejte skupinu prostředků.
   - **Název účtu úložiště**: Zadejte jedinečný název účtu úložiště.
   - **Umístění**: Volba datového centra v souladu se zásadami vaší společnosti
   - **Výkon:** Zvolit standard
   - **Druh účtu** Zvolit StorageV2 (pro obecné účely v2) 
   - **Replikace**: Výběr místně redundantního úložiště (LRS)
   - **Úroveň přístupu**: Zvolit studenou

   ![Snímek obrazovky zobrazující vlastnosti kontejneru objektů BLOB](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Klikněte na tlačítko **zkontrolovat + vytvořit** a potom klikněte na tlačítko **vytvořit**.  

### <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob 

1. Po úspěšném nasazení klikněte na **Přejít k prostředku**. Můžete také připnout účet úložiště na řídicí panel, abyste mohli snadno přistupovat. 
1. Klikněte na **Průzkumník služby Storage (Preview)** , klikněte pravým tlačítkem na **kontejnery objektů BLOB**a pak klikněte na **vytvořit kontejner objektů BLOB**. 
1. Zadejte název nového kontejneru a klikněte na tlačítko **OK**.

## <a name="create-a-sas-url"></a>Vytvoření adresy URL SAS

1. Klikněte pravým tlačítkem na kontejner, klikněte na **získat sdílený přístupový podpis**. 
   
   ![Snímek obrazovky ukazující, jak získat sdílený přístupový podpis kontejneru objektů BLOB](media/azure-stack-automatic-log-collection/get-sas.png)

1. Vyberte tyto vlastnosti:
   - Čas spuštění: Volitelně můžete přesunout čas zahájení zpět. 
   - Čas vypršení platnosti: Dva roky
   - Časové pásmo: UTC
   - Nastaven Čtení, zápis a výpis

   ![Snímek obrazovky se zobrazením vlastností sdíleného přístupového podpisu](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Klikněte na možnost **Vytvořit**.  

Zkopírujte adresu URL a zadejte ji při [konfiguraci automatického shromažďování protokolů](azure-stack-configure-automatic-diagnostic-log-collection.md). Další informace o adresách URL SAS najdete v tématu [použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 


## <a name="steps-to-configure-automatic-log-collection"></a>Postup konfigurace automatického shromažďování protokolů

Pomocí těchto kroků přidejte adresu URL SAS do uživatelského rozhraní shromažďování protokolů: 

1. Přihlaste se k portálu správce Azure Stack.
1. Otevřete okno **pomoc a podpora – přehled**.
1. Klikněte na **Nastavení automatické kolekce**.

   ![Snímek obrazovky s informacemi o tom, kde Povolit shromažďování protokolů v nápovědě a podpoře](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Nastavte automatické shromažďování protokolů na **povoleno**.
1. Zadejte adresu URL sdíleného přístupového podpisu (SAS) pro kontejner objektů BLOB účtu úložiště.

   ![Snímek obrazovky s adresou URL SAS objektu BLOB](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>Automatické shromažďování protokolů lze kdykoli zakázat a znovu povolit. Konfigurace adresy URL SAS se nemění. Pokud je automatické shromažďování protokolů znovu povolené, dříve zadaná adresa URL SAS bude podléhat stejným kontrolám ověření a adresa URL SAS, jejíž platnost vypršela, bude odmítnuta. 

## <a name="view-log-collection"></a>Zobrazit kolekci protokolů

Historie protokolů shromážděných z Azure Stack se zobrazí na stránce **shromažďování protokolů** v nápovědě a podpoře s následujícími daty a časy:

- **Čas shromažďování dat**: Při zahájení operace shromažďování protokolů 
- **Od data**: Začátek časového období, pro které chcete shromáždit
- **Datum do**: Konec časového období

![Snímek obrazovky se zobrazením kolekcí protokolů](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

Pokud shromažďování protokolů diagnostiky neproběhne úspěšně, ověřte, zda je adresa URL SAS platná. Pokud chyba přetrvává nebo se zobrazí více selhání, zavolejte pro nápovědu Microsoft CSS. 

Operátoři můžou také kontrolovat účet úložiště pro automaticky shromážděné protokoly. Tento snímek obrazovky například ukazuje kolekce protokolů pomocí Průzkumník služby Storage Preview z Azure Portal:

![Snímek obrazovky se zobrazením kolekcí protokolů](media/azure-stack-automatic-log-collection/check-storage-account.png)

## <a name="automatic-diagnostic-log-collection-alerts"></a>Automatické výstrahy shromažďování protokolů diagnostiky 

Pokud je povoleno, bude automatické shromažďování protokolů diagnostiky provedeno pouze v případě potřeby. Pouze následující kolekce triggerů výstrah. 

|Název výstrahy  | FaultIdType|    
|-------------|------------|
|Nejde se připojit ke vzdálené službě |  UsageBridge.NetworkError|
|Aktualizace se nezdařila. |    Urp.UpdateFailure   |          
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
|Zálohování neproběhlo úspěšně  | AzureStack. BackupController. BackupFailedGeneralFault |    
|Naplánované zálohování bylo přeskočeno z důvodu konfliktu s neúspěšnými operacemi.  | AzureStack. BackupController. BackupSkippedWithFailedOperationFault |   


## <a name="see-also"></a>Viz také:

[Azure Stack zpracování dat protokolů a zákazníků](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[Použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[Osvědčené postupy pro automatické shromažďování protokolů Azure Stack](azure-stack-best-practices-automatic-diagnostic-log-collection.md)





