---
title: Vymažte data uživatelů portálu na vyžádání z centra Azure Stack. | Dokumentace Microsoftu
description: Jako operátor centra Azure Stack se naučíte, jak vymazat data uživatelů portálu, když to požadují uživatelé centra Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/10/2019
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
monikerRange: azs-1802
ms.openlocfilehash: ac28a67f7b1409ebc5a786a88e8b9702df94c2ff
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022773"
---
# <a name="clear-portal-user-data-from-azure-stack-hub"></a>Vymaže data uživatelů portálu z centra Azure Stack.

Operátory centra Azure Stack můžou na vyžádání vymazat data uživatelů portálu, když je Azure Stack uživatelé centra požadují. Jako uživatel centra Azure Stack můžete portál přizpůsobit pomocí připnutí dlaždic a změnou rozložení řídicího panelu. Uživatelé také mohou změnit motiv a nastavit výchozí jazyk tak, aby odpovídal osobním potřebám. 

Uživatelská data portálu zahrnují oblíbené a nedávno použité prostředky na portálu Azure Stack User Portal. Tento článek popisuje, jak vymazat data uživatelů portálu.

Odebrání uživatelských nastavení portálu by se mělo provádět jenom po odstranění předplatného uživatele.

> [!NOTE]
> Některá uživatelská data mohou v protokolech událostí stále existovat po provedení pokynů v tomto článku v části systém. Tato data můžou trvat několik dní, než se protokoly automaticky převádějí.

## <a name="requirements"></a>Požadavky

- [Nainstalujte PowerShell pro centrum Azure Stack](azure-stack-powershell-install.md).
- [Stáhněte si nejnovější nástroje Azure Stack hub](azure-stack-powershell-download.md) z GitHubu.
- Uživatelský účet musí stále existovat v adresáři.
- Azure Stack přihlašovací údaje správce centra pro přístup ke koncovému bodu Správce prostředků správce.

> [!NOTE]
> Pokud se pokusíte odstranit informace o uživatelích portálu z uživatele, který byl pozván z adresáře hosta (víceklientské prostředí), musíte mít v tomto adresáři oprávnění ke čtení. Další informace najdete v části [scénář CSP dále v tomto článku](#clear-portal-user-data-in-guest-directory).

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>Vymazání uživatelských dat portálu pomocí hlavního názvu uživatele

V tomto scénáři se předpokládá, že buď je výchozí předplatné poskytovatele a uživatel součástí stejného adresáře, nebo že máte oprávnění ke čtení adresáře, ve kterém se uživatel nachází.

Než budete pokračovat, nezapomeňte [si stáhnout nejnovější verzi nástrojů Azure Stack hub](azure-stack-powershell-download.md) z GitHubu.

Pro účely tohoto postupu použijte počítač, který může komunikovat s Správce prostředků koncovým bodem Azure Stack centra pro správu.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce), přejděte do kořenové složky v adresáři **AzureStack-Tools-Master** a importujte požadovaný modul prostředí PowerShell:

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Spusťte následující příkazy. Nezapomeňte nahradit zástupné symboly hodnotami, které odpovídají vašemu prostředí.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $azureStackDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"

   ## Replace the following value with the user directory tenant ID.
   $userDirectoryTenantId = " 7ddf3648-9671-47fd-b63d-eecd82ed040e"

   ## Replace the following value with name of the user principal whose portal user data is to be cleared.
   $userPrincipalName = "myaccount@contoso.onmicrosoft.com"

   Clear-AzsUserDataWithUserPrincipalName -AzsAdminArmEndpoint $adminARMEndpoint `
    -AzsAdminDirectoryTenantId $azureStackDirectoryTenantId `
    -UserPrincipalName $userPrincipalName `
    -DirectoryTenantId $userDirectoryTenantId
   ```

   > [!NOTE]
   > Parametr `azureStackDirectoryTenantId` je volitelný. Pokud tuto hodnotu nezadáte, skript vyhledá hlavní název uživatele ve všech adresářích tenanta zaregistrovaných v Azure Stackovém centru a pak vymaže data portálu pro všechny odpovídající uživatele.

## <a name="clear-portal-user-data-in-guest-directory"></a>Vymazání uživatelských dat portálu v adresáři hosta

V tomto scénáři nemá operátor centra Azure Stack žádný přístup k adresáři hosta, ve kterém se nachází uživatel. Toto je běžný scénář, pokud jste poskytovatelem Cloud Solution Provider (CSP).

Pro operátor centra Azure Stack pro odebrání uživatelských dat portálu je nutné zadat alespoň ID uživatelského objektu.

Uživatel musí zadat dotaz na ID objektu a poskytnout mu operátor centra Azure Stack. Operátor nemá přístup k adresáři, ve kterém se nachází uživatel.

### <a name="user-retrieves-the-user-object-id"></a>Uživatel načte ID objektu uživatele.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce), přejděte do kořenové složky v adresáři **AzureStack-Tools-Master** a pak importujte potřebný modul PowerShellu.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Spusťte následující příkazy. Nezapomeňte nahradit zástupné symboly hodnotami, které odpovídají vašemu prostředí.

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $userARMEndpoint = "https://management.local.azurestack.external"

   ## Replace the following value with the directory tenant ID, which contains the user account.
   $userDirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userPrincipleName = "myaccount@contoso.onmicrosoft.com"

   Get-UserObjectId -DirectoryTenantId $userDirectoryTenantId `
    -AzsArmEndpoint $userARMEndpoint `
    -UserPricinpalName $userPrincipleName
   ```

   > [!NOTE]
   > Jako uživatel musíte zadat ID objektu uživatele, což je výstup předchozího skriptu, do operátoru centra Azure Stack.

## <a name="azure-stack-hub-operator-removes-the-portal-user-data"></a>Operátor centra Azure Stack odebere data uživatelů portálu.

Po přijetí ID objektu uživatele jako operátoru centra Azure Stack spusťte následující příkazy pro odebrání uživatelských dat portálu:

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce), přejděte do kořenové složky v adresáři **AzureStack-Tools-Master** a pak importujte potřebný modul PowerShellu.

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. Spusťte následující příkazy a ujistěte se, že upravíte parametr tak, aby odpovídal vašemu prostředí:

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $AzsAdminDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"
   
   ## Replace the following value with the directory tenant ID of the user to clear.
   $DirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userObjectID = "s-1-*******"
   Clear-AzsUserDataWithUserObject -AzsAdminArmEndpoint $AzsAdminARMEndpoint `
    -AzsAdminDirectoryTenantId $AzsAdminDirectoryTenantId `
    -DirectoryTenantID $DirectoryTenantId `
    -UserObjectID $userObjectID `
   ```

## <a name="next-steps"></a>Další kroky

- [Zaregistrujte Azure Stack hub pomocí Azure](azure-stack-registration.md) a naplňte na [web centra Azure Stack](azure-stack-marketplace.md) a položky, které nabídnou vašim uživatelům.
