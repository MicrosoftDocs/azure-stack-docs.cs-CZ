---
title: Stažení položek z webu Marketplace z Azure a publikování do Azure Stack | Microsoft Docs
description: Naučte se stahovat položky Marketplace z Azure a publikovat na Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 734c84c1226a9e1ed4a9f3e34b787f1677ab6902
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939523"
---
# <a name="download-marketplace-items-to-azure-stack-hub"></a>Stažení položek z Marketplace do centra Azure Stack 

Jako operátor cloudu můžete z Marketplace stahovat položky do centra Azure Stack a zpřístupnit je všem uživatelům pomocí prostředí Azure Stack hub. Položky, které můžete vybrat, jsou z uspořádaného seznamu Azure Marketplacech položek, které jsou předem testovány a podporovány pro práci s Azure Stack. Do tohoto seznamu se často přidají další položky, takže se budete moct vrátit k novému obsahu.

Existují dva scénáře stažení produktů z webu Marketplace:

- **Připojený scénář**: vyžaduje, aby bylo prostředí centra Azure Stack připojené k Internetu. K vyhledání a stažení položek slouží portál pro správu centra Azure Stack.
- **Odpojený nebo částečně připojený scénář**: vyžaduje, abyste měli přístup k Internetu pomocí syndikačního nástroje Marketplace ke stažení položek Marketplace. Pak přenesete soubory ke stažení na odpojenou Azure Stack instalaci. Tento scénář používá PowerShell.

Úplný seznam položek na webu Marketplace, které si můžete stáhnout, najdete v tématu [Azure Marketplace položky Azure Stack](../../operator/azure-stack-marketplace-azure-items.md) . Seznam nedávných přidání, odstranění a aktualizací pro Azure Stack Marketplace najdete v článku o [Azure Stack na webu Marketplace](../../operator/azure-stack-marketplace-changes.md) .

> [!NOTE]
> Katalog se bude lišit v závislosti na cloudu, ke kterému je připojen systém Azure Stack hub. Cloudové prostředí se určuje pomocí předplatného Azure, které používáte k registraci centra Azure Stack.

## <a name="connected-scenario"></a>Připojený scénář

Pokud se centrum Azure Stack připojuje k Internetu, můžete k stažení položek z Marketplace použít portál pro správu.

### <a name="prerequisites"></a>Předpoklady

Vaše nasazení centra Azure Stack musí mít připojení k Internetu a musí být [zaregistrované v Azure](registration-tzl.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Stažení položek z Marketplace pomocí portálu

1. Přihlaste se k portálu pro správu centra Azure Stack.

2. Před stažením položek z webu Marketplace zkontrolujte dostupný prostor úložiště. Později můžete po výběru položek ke stažení porovnat velikost stahovaných zařízení s dostupnou kapacitou úložiště. Pokud je kapacita omezená, zvažte možnosti [správy dostupného místa](../../operator/azure-stack-manage-storage-shares.md#manage-available-space).

   Chcete-li zkontrolovat dostupné místo: v **oblasti Správa oblastí** vyberte oblast, kterou chcete prozkoumat, a pak klikněte na úložiště **poskytovatelé prostředků**  >  **Storage**:

   ![Kontrola místa v úložišti Azure Stack portálu pro správu](media/azure-stack-download-azure-marketplace-item-tzl/storage.png)

3. Otevřete web Centrum pro Azure Stack a připojte se k Azure. Provedete to tak, že vyberete službu **pro správu Marketplace** , vyberete **položky Marketplace** a pak vyberete **Přidat z Azure**:

   ![Přidání položek z Marketplace z Azure](media/azure-stack-download-azure-marketplace-item-tzl/marketplace.png)

4. V každé položce řádku se zobrazí také aktuálně dostupná verze. Pokud je k dispozici více než jedna verze položky Marketplace, zobrazí se ve sloupci **verze** **více**. Kliknutím na každou položku můžete zobrazit její popis a další informace, včetně její velikosti ke stažení:

   ![Přidat z Azure](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure1.png)

5. Pokud je verze položky zobrazená jako **více**, můžete tuto položku vybrat a pak vybrat konkrétní verzi z rozevíracího seznamu výsledná verze – výběr:

   ![Přidat z Azure](media/azure-stack-download-azure-marketplace-item-tzl/add-from-azure3.png)

6. Vyberte položku, kterou chcete, a pak vyberte **Stáhnout**. Doba stahování se liší a závisí na připojení k síti. Po dokončení stahování můžete novou položku Marketplace nasadit buď jako operátor Azure Stack, nebo na uživatele.

7. Pokud chcete staženou položku nasadit, vyberte **+ vytvořit prostředek** a potom vyhledejte kategorie nové položky Marketplace. Pak vyberte položku, která zahájí proces nasazení. Tento proces se liší pro různé položky Marketplace.

## <a name="disconnected-or-a-partially-connected-scenario"></a>Odpojený nebo částečně připojený scénář

V odpojeném prostředí nemůžete stáhnout položky Marketplace z Azure. K stažení položek Marketplace na místní disk musíte použít nástroj pro syndikaci offline, a pak tyto položky nahrajte do centra Azure Stack.

[Online nástroje pro syndikaci si můžete stáhnout tady](https://aka.ms/azsSyndicationtool).

### <a name="download-marketplace-items-from-azure"></a>Stažení položek z Marketplace z Azure

#### <a name="prerequisites"></a>Předpoklady

- Azure PowerShell v počítači nainstalován.

- ID předplatného Azure, registrační skupina prostředků a název registrace, které jste použili k registraci centra Azure Stack. Tyto informace najdete na kartě **vlastnosti** v okně **Správa oblastí** na portálu operátora centra Azure Stack.

- Uživatelské jméno a heslo pro účet, který má oprávnění vlastníka nebo přispěvatele k ID předplatného Azure.

- Místní disk nebo síťovou cestu, do které lze zapisovat soubory v režimu offline.

#### <a name="download-items"></a>Stáhnout položky

1. Otevřete PowerShell a pokračujte na extrahovanou složku.

2. Spusťte skript **Invoke-AzSMarketplaceDownload.ps1** PowerShellu:

    ```powershell
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationSubscriptionId '<subscription ID>' ` 
       -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' `
       -TenantName mytenant.onmicrosoft.com -DownloadFolder 'F:\offlineSyndication'
    ```

    Pokud jste už přihlášení prostřednictvím Azure PowerShellu, můžete v kontextu Azure předat:

    ```powershell
    Add-AzureRmAccount -Environment AzureCloud -Tenant mytenant.onmicrosoft.com 
    .\Invoke-AzSMarketplaceDownload.ps1 -RegistrationResourceGroup 'azurestack' -RegistrationName '<registration name>' -DownloadFolder 'F:\offlineSyndication' -AzureContext $(Get-AzureRMContext)
    ```

    Pokud nepředáte kontext Azure, budete vyzváni k přihlášení.

3. Zobrazí se okno, ve kterém můžete vybrat produkt, který chcete stáhnout. Můžete vybrat více položek stisknutím Ctrl + kliknutí.

4. Vyberte **OK**. Tím se stáhne položka Marketplace a její závislosti, pokud existují.

### <a name="upload-marketplace-items-to-azure-stack-hub"></a>Nahrání položek Marketplace do centra Azure Stack

#### <a name="prerequisites"></a>Předpoklady

- Správce Azure Stack Správce prostředků koncového bodu a klienta adresáře.

- Přístup k položkám offline Marketplace.

#### <a name="upload-items"></a>Nahrání položek

1. Otevřete PowerShell a pokračujte na extrahovanou složku.

2. Spusťte skript **Invoke-AzSMarketplaceUpload.ps1** PowerShellu:

    ```powershell
    .\Invoke-AzsMarketplaceUpload.ps1 -AzureStackCloudName "AzureStack-Admin" -AzureStackAdminARMEndpoint https://adminmanagement.<region>.<fqdn> -TenantName mytenant.onmicrosoft.com -DownloadFolder F:\offlineSyndication
    ```

    Alternativně můžete nastavit Azure Stack prostředí sami v prostředí Azure PowerShell, ověřit ho pro správce Správce prostředků koncovým bodem a předat do skriptu kontext:

    ```powershell
    Add-AzureRmEnvironment -Name Redmond-Admin -ARMEndpoint https://adminmanagement.redmond.azurestack.corp.microsoft.com

    Add-AzureRmAccount -Environment Redmond-Admin

    .\Invoke-AzsMarketplaceUpload.ps1 -DownloadFolder F:\Downloads\offlining -AzureContext $(Get-AzureRmContext)
    ```

    Tento postup nahraje položky Marketplace do určeného centra Azure Stack.
