---
title: Proaktivní shromažďování protokolů diagnostiky v centru Azure Stack
description: Přečtěte si, jak nakonfigurovat shromažďování proaktivní diagnostického protokolu v centru Azure Stack Help a podpora.
author: justinha
ms.topic: article
ms.date: 04/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 04/17/2020
ms.openlocfilehash: e77252cf89d52291d7d4071d83981eb36bb062ef
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836168"
---
# <a name="proactive-diagnostic-log-collection-in-azure-stack-hub"></a>Proaktivní shromažďování protokolů diagnostiky v centru Azure Stack

::: moniker range=">= azs-2002"

Šetřete čas s zákaznickou podporou tím, že aktivně shromažďují protokoly diagnostiky, když se výstraha aktivuje na Azure Stackovém centru.

Pokud je potřeba prozkoumat stav systému, protokoly je možné nahrát automaticky pro analýzu před otevřením případu podpory se službou zákaznické podpory Microsoftu (CSS).

## <a name="steps-to-configure-proactive-log-collection"></a>Postup konfigurace proaktivní kolekce protokolů

Pomocí těchto kroků můžete nakonfigurovat proaktivní shromažďování protokolů. Proaktivní shromažďování protokolů lze kdykoli zakázat a znovu povolit.  

1. Přihlaste se k portálu pro správu centra Azure Stack.
1. Otevřete okno **help + podpora – přehled**.
1. Pokud se zobrazí nápis, vyberte **Povolit proaktivní shromažďování protokolů**. =

   ![Snímek obrazovky s informacemi o tom, kde Povolit shromažďování protokolů v nápovědě a podpoře](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

   Nebo můžete vybrat **Nastavení** a nastavit **proaktivní shromažďování protokolů** tak, aby se **povolilo**, a pak vybrat **Uložit**.

   ![Snímek obrazovky s informacemi o tom, kde Povolit shromažďování protokolů v nápovědě a podpoře](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

Pro zjednodušení shromažďování protokolů a možnosti zákaznické podpory doporučujeme nakonfigurovat funkci automatického shromažďování protokolů diagnostiky.

Pokud je potřeba prozkoumat stav systému, protokoly je možné automaticky nahrát k analýze prostřednictvím služeb zákaznické podpory Microsoftu (CSS).

## <a name="create-an-azure-blob-container-sas-url"></a>Vytvoření adresy URL SAS kontejneru objektů BLOB v Azure

Než budete moct nakonfigurovat automatické shromažďování protokolů, musíte pro kontejner objektů BLOB získat sdílený přístupový podpis (SAS). SAS umožňuje udělit přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu.

Soubory protokolu centra Azure Stack můžete ukládat do kontejneru objektů BLOB v Azure a pak zadat adresu URL SAS, kde může CSS shromažďovat protokoly.

### <a name="prerequisites"></a>Požadavky

V Azure můžete použít nový nebo existující kontejner objektů BLOB. Pokud chcete vytvořit kontejner objektů BLOB v Azure, potřebujete aspoň [roli Přispěvatel objektů BLOB úložiště](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) nebo [konkrétní oprávnění](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). Globální správci mají také potřebná oprávnění.

Osvědčené postupy při výběru parametrů pro účet úložiště automatického shromažďování protokolů najdete v tématu [osvědčené postupy pro automatické shromažďování protokolů Azure Stack hub](azure-stack-best-practices-automatic-diagnostic-log-collection.md). Další informace o typech účtů úložiště najdete v tématu [Přehled účtu Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

### <a name="create-a-blob-storage-account"></a>Vytvoření účtu úložiště BLOB

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **účty** > úložiště **Přidat**.
1. Vytvořte kontejner objektů BLOB s těmito nastaveními:

   - **Předplatné**: vyberte předplatné Azure.
   - **Skupina prostředků**: zadejte skupinu prostředků.
   - **Název účtu úložiště**: Zadejte jedinečný název účtu úložiště.
   - **Umístění**: Vyberte datové centrum v souladu se zásadami vaší společnosti.
   - **Výkon**: Standard.
   - **Druh účtu** StorageV2 (obecné účely v2).
   - **Replikace**: místně redundantní úložiště (LRS).
   - **Úroveň přístupu**: studená.

   ![Vlastnosti kontejneru objektů BLOB](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. Vyberte **Zkontrolovat a vytvořit** a potom **Vytvořit**.  

### <a name="create-a-blob-container"></a>Vytvoření kontejneru objektů blob

1. Po úspěšném nasazení vyberte **Přejít k prostředku**. Můžete také připnout účet úložiště na řídicí panel, abyste mohli snadno přistupovat.
1. Vyberte **Průzkumník služby Storage (Preview)**, klikněte pravým tlačítkem na **kontejnery objektů BLOB**a vyberte **vytvořit kontejner objektů BLOB**.
1. Zadejte název nového kontejneru a vyberte **OK**.

## <a name="create-a-sas-url"></a>Vytvoření adresy URL SAS

1. Klikněte pravým tlačítkem na kontejner a pak vyberte **získat sdílený přístupový podpis**.
   
   ![Jak získat sdílený přístupový podpis kontejneru objektů BLOB](media/azure-stack-automatic-log-collection/get-sas.png)

1. Vyberte tyto vlastnosti:

   - Čas spuštění: Volitelně můžete přesunout čas zahájení zpět.
   - Čas vypršení platnosti: dva roky
   - Časové pásmo: UTC
   - Oprávnění: čtení, zápis a výpis

   ![Vlastnosti sdíleného přístupového podpisu](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. Vyberte **Vytvořit**.  

Zkopírujte adresu URL a zadejte ji při [konfiguraci automatického shromažďování protokolů](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Další informace o adresách URL SAS najdete v tématu [použití sdílených přístupových podpisů (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

## <a name="steps-to-configure-automatic-log-collection"></a>Postup konfigurace automatického shromažďování protokolů

Pomocí těchto kroků přidejte adresu URL SAS do uživatelského rozhraní shromažďování protokolů:

1. Přihlaste se k portálu pro správu centra Azure Stack.
1. Otevřete okno **help + podpora – přehled**.
1. Klikněte na **Nastavení automatické kolekce**.

   ![Kde Povolit shromažďování protokolů v nápovědě a podpoře](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. Nastavte automatické shromažďování protokolů na **povoleno**.
1. Zadejte adresu URL sdíleného přístupového podpisu (SAS) pro kontejner objektů BLOB účtu úložiště.

   ![Adresa URL SAS objektu BLOB](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>Automatické shromažďování protokolů lze kdykoli zakázat a znovu povolit. Konfigurace adresy URL SAS se nemění. Pokud je automatické shromažďování protokolů znovu povolené, dříve zadaná adresa URL SAS bude podléhat stejným kontrolám ověření a adresa URL SAS, jejíž platnost vypršela, bude odmítnuta.

::: moniker-end

## <a name="view-log-collection"></a>Zobrazit kolekci protokolů

Historie protokolů shromážděných z centra Azure Stack se zobrazí na stránce **shromažďování protokolů** v **nápovědě a podpoře**s následujícími daty a časy:

- **Čas shromažďování**: při zahájení operace shromažďování protokolů.
- **Stav**: buď probíhá, nebo dokončeno.
- **Zahajte zápisy**: začátek časového období, které chcete shromáždit.
- **Konec protokolů**: konec časového období.
- **Typ**: Pokud se jedná o manuální nebo proaktivní shromažďování protokolů.

![Kolekce protokolů v nápovědě a podpoře](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="proactive-diagnostic-log-collection-alerts"></a>Výstrahy shromažďování proaktivní diagnostického protokolu

Je-li povoleno, služba proaktivní shromažďování protokolů odesílá protokoly pouze v případě, že je vyvolána jedna z následujících událostí.

Například **Chyba aktualizace** je výstraha, která spouští proaktivní shromažďování protokolů diagnostiky. Pokud je povoleno, diagnostické protokoly budou aktivně zachyceny během chyby aktualizace, což usnadňuje řešení potíží se šablonou stylů CSS. Diagnostické protokoly jsou shromažďovány pouze v případě, že se vyvolá výstraha pro **aktualizaci** .

| Název výstrahy | FaultIdType |
|---|---|
|Nejde se připojit ke vzdálené službě | UsageBridge.NetworkError|
|Aktualizace se nezdařila | Urp.UpdateFailure |
|Infrastruktura nebo závislosti poskytovatele prostředků úložiště nejsou k dispozici. |    StorageResourceProviderDependencyUnavailable |
|Uzel není připojený k řadiči.| ServerHostNotConnectedToController |  
|Selhání publikování trasy | SlbMuxRoutePublicationFailure |
|Interní úložiště dat poskytovatele prostředků úložiště není dostupné. |    StorageResourceProvider. DataStoreConnectionFail |
|Selhání úložného zařízení | Microsoft. Health. typ FaultType. VirtualDisks. odpojilo se |
|Kontroler stavu nemůže získat přístup k účtu úložiště. | Microsoft. Health. typ FaultType. StorageError |
|Připojení k fyzickému disku bylo ztraceno. | Microsoft. Health. typ FaultType. fyzický disk. LostCommunication |
|Služba BLOB Service není spuštěná na uzlu. | StorageService. blob. Service. is. not. Running. on. Node-Critical |
|Role infrastruktury není v pořádku. | Microsoft. Health. typ FaultType. GenericExceptionFault |
|Chyby služby Table service | StorageService. Table. Service. Errors – kritický |
|Sdílená složka je větší než 80% využití. | Microsoft. Health. typ FaultType. sdílené složky. Capacity. Warning. |
|Uzel jednotky škálování je offline | FRP. Prezenční signál. PhysicalNode |
|Instance role infrastruktury není dostupná. | FRP. Prezenční signál. InfraVM |
|Instance role infrastruktury není dostupná.  | FRP. Prezenční signál. NonHaVm |
|Role infrastruktury, Správa adresářů, ohlásila chyby synchronizace času. | DirectoryServiceTimeSynchronizationError |
|Blížící se vypršení platnosti externího certifikátu | CertificateExpiration. ExternalCert. Warning |
|Blížící se vypršení platnosti externího certifikátu | CertificateExpiration. ExternalCert. Critical |
|Pro konkrétní třídu a velikost nejde zřídit virtuální počítače kvůli nedostatečné kapacitě paměti | AzureStack. ComputeController. VmCreationFailure. LowMemory |
|Nedostupný uzel pro umístění virtuálního počítače | AzureStack. ComputeController. HostUnresponsive |
|Zálohování nebylo úspěšné.  | AzureStack. BackupController. BackupFailedGeneralFault |
|Naplánované zálohování bylo přeskočeno z důvodu konfliktu s neúspěšnými operacemi.    | AzureStack. BackupController. BackupSkippedWithFailedOperationFault |

## <a name="see-also"></a>Viz také

[Azure Stack zpracování dat protokolů a zákazníků centra](azure-stack-data-collection.md)
