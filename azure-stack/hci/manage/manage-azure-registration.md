---
title: Správa registrace Azure pro Azure Stack HCI
description: Jak spravovat registraci Azure pro Azure Stack HCI, pochopit stav registrace a zrušit registraci clusteru, až budete připraveni vyřadit z provozu.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/27/2021
ms.openlocfilehash: c16216a52b0955277bc6d30725f88d0555908685
ms.sourcegitcommit: dc11aabd3b97c505c5b3cecd3bdb2d5c8e8496aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98981174"
---
# <a name="manage-azure-registration"></a>Správa registrace v Azure

> Platí pro Azure Stack HCI v20H2

Po vytvoření clusteru Azure Stack HCI musíte [cluster zaregistrovat pomocí ARC Azure](../deploy/register-with-azure.md). Po registraci clusteru pravidelně synchronizuje informace mezi místním clusterem a cloudem. V tomto tématu se dozvíte, jak pochopit stav registrace, udělit Azure Active Directory oprávnění a zrušit registraci clusteru, až budete připraveni vyřadit z provozu.

## <a name="understanding-registration-status-using-windows-admin-center"></a>Principy stavu registrace pomocí centra pro správu Windows

Když se připojíte ke clusteru pomocí centra pro správu Windows, zobrazí se řídicí panel, který zobrazuje stav připojení Azure. **Připojeno** znamená, že cluster už je zaregistrovaný v Azure a během posledního dne se úspěšně synchronizuje do cloudu.

   :::image type="content" source="media/manage-azure-registration/registration-status.png" alt-text="Na řídicím panelu centra pro správu systému Windows se vždy zobrazí stav připojení clusteru" lightbox="media/manage-azure-registration/registration-status.png":::

Další informace můžete získat tak, že v levém dolním rohu nabídky **nástroje** na levé straně **vyberete** Další informace a pak vyberete **Azure Stack registraci HCI**.

   :::image type="content" source="media/manage-azure-registration/azure-stack-hci-registration.png" alt-text="Pokud chcete získat další informace, vyberte nastavení > nástroje > Azure Stack registraci HCI." lightbox="media/manage-azure-registration/azure-stack-hci-registration.png":::

## <a name="understanding-registration-status-using-powershell"></a>Principy stavu registrace pomocí prostředí PowerShell

Chcete-li pochopit stav registrace pomocí prostředí Windows PowerShell, použijte `Get-AzureStackHCI` rutinu prostředí PowerShell a `ClusterStatus` `RegistrationStatus` vlastnosti, a `ConnectionStatus` . Například po instalaci operačního systému Azure Stack HCI před vytvořením nebo připojením ke clusteru se v této `ClusterStatus` Vlastnosti zobrazuje stav není dosud:

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Stav registrace Azure před vytvořením clusteru":::

Po vytvoření clusteru se `RegistrationStatus` zobrazí pouze stav "nedosud":

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Stav registrace Azure po vytvoření clusteru":::

Azure Stack HCI se musí zaregistrovat do 30 dnů od instalace podle podmínek Azure Online Services. Pokud není cluster po 30 dnech zaregistrovaný, zobrazí se v `ClusterStatus` `OutOfPolicy` seznamu `RegistrationStatus` `OutOfPolicy` .

Po zaregistrování clusteru můžete zobrazit `ConnectionStatus` `LastConnected` čas a, který je obvykle během posledního dne, pokud není cluster dočasně odpojený od Internetu. Cluster Azure Stack HCI může plně fungovat po dobu až 30 po sobě jdoucích dnů.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Stav registrace Azure po registraci":::

Pokud je toto maximální období překročené, zobrazí se `ConnectionStatus` `OutOfPolicy` .

## <a name="azure-active-directory-app-permissions"></a>Azure Active Directory oprávnění aplikace

Kromě vytvoření prostředku Azure v rámci vašeho předplatného se při registraci Azure Stack HCI v tenantovi Azure Active Directory vytvoří identita aplikace, která se v tomto tenantovi podobá uživateli. Tato identita aplikace zdědí název clusteru. Tato identita podle potřeby jedná jménem cloudové služby Azure Stack HCI v rámci vašeho předplatného.

Pokud uživatel, který registruje cluster, je správcem Azure Active Directory nebo má delegovaná dostatečná oprávnění, dojde k tomu automaticky a nevyžaduje se žádná další akce. V takovém případě může správce Azure Active Directory pro dokončení registrace potřebovat schválení. Správce může buď výslovně udělit souhlas s aplikací, nebo může delegovat oprávnění, abyste mohli udělit souhlas aplikaci:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Azure Active Directory oprávnění a diagramu identity" border="false":::

Pokud chcete udělit souhlas, otevřete [Portal.Azure.com](https://portal.azure.com) a přihlaste se pomocí účtu Azure, který má dostatečná oprávnění pro Azure Active Directory. Přejděte na **Azure Active Directory** a pak **Registrace aplikací**. Vyberte identitu aplikace pojmenovanou po vašem clusteru a přejděte na **oprávnění rozhraní API**.

Pro vydání služby Azure Stack HCL pro obecné dostupnosti (GA) vyžaduje aplikace následující oprávnění, která se liší od oprávnění aplikací požadovaných v Public Preview:

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

Pro Public Preview oprávnění aplikace byly (tyto jsou nyní zastaralé):

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Hledání od správce Azure Active Directory může nějakou dobu trvat, takže `Register-AzStackHCI` rutina ukončí a ponechá registraci ve stavu čeká na schválení správce, tj. částečně dokončená. Po udělení souhlasu jednoduše znovu spusťte `Register-AzStackHCI` registraci a dokončete ji.

## <a name="azure-active-directory-user-permissions"></a>Azure Active Directory uživatelských oprávnění

Uživatel, který používá Register-AzStackHCI potřebuje oprávnění služby Azure AD:

- Vytvoření, získání, nastavení nebo odebrání aplikací Azure AD (nové/získání/nastavení/odebrání – AzureADApplication)
- Vytvoření nebo získání instančního objektu služby Azure AD (New/Get-New-AzureADServicePrincipal)
- Správa tajných kódů aplikace AD (nový/získat/odebrat – AzureADApplicationKeyCredential)
- Udělit souhlas pro použití konkrétních oprávnění aplikace (nový/získat/odebrat AzureADServiceAppRoleAssignments)

To lze provést třemi způsoby.

### <a name="option-1-allow-any-user-to-register-applications"></a>Možnost 1: Povolení registrace aplikací všem uživatelům

V Azure Active Directory přejděte na **uživatelské nastavení > registrace aplikací**. V části **Uživatelé můžou registrovat aplikace** vyberte **Ano**.

To umožní každému uživateli registrovat aplikace. Uživatel ale bude i nadále vyžadovat, aby správce Azure AD udělil souhlas během registrace clusteru. Všimněte si, že se jedná o nastavení na úrovni tenanta, takže nemusí být vhodné pro velké podnikové zákazníky.

### <a name="option-2-assign-cloud-application-administration-role"></a>Možnost 2: přiřazení role správce cloudové aplikace

Přiřaďte uživateli integrovanou roli Azure AD správa cloudové aplikace. To umožní uživateli zaregistrovat a zrušit registraci clusterů bez nutnosti dalšího souhlasu správce služby AD.

### <a name="option-3-create-a-custom-ad-role-and-consent-policy"></a>Možnost 3: Vytvoření vlastní role AD a zásad pro vyjádření souhlasu

Nejvíce omezující možnost je vytvořit vlastní roli AD s vlastní zásadou souhlasu, která deleguje souhlas správce na úrovni tenanta pro požadovaná oprávnění pro Azure Stack službu HCI. Když je tato vlastní role přiřazená, můžou si uživatelé zaregistrovat i udělit souhlas bez nutnosti dalšího souhlasu správce služby AD.

   > [!NOTE]
   > Tato možnost vyžaduje licenci Azure AD Premium a používá vlastní role služby AD a vlastní funkce zásad souhlasu, které jsou aktuálně ve verzi Public Preview.

   1. Připojte se k Azure AD:
   
      ```powershell
      Connect-AzureAD
      ```

   2. Vytvořte vlastní zásadu souhlasu:

      ```powershell
      New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
      ```

   3. Přidejte podmínku, která zahrnuje požadovaná oprávnění aplikace pro Azure Stack službu HCI, která umožňuje, aby ID aplikace bylo 1322e676-dee7-41ee-A874-ac923822781c. Následující oprávnění jsou k dispozici pro vydání GA Azure Stack HCL a nebudou fungovat s Public Preview, pokud jste neKB4586852i na každý server v clusteru použili verzi [23. listopadu 2020 Preview ()](https://support.microsoft.com/help/4595086/azure-stack-hci-release-notes-overview) a stáhli jste StackHCI modulu AZ. verze 0.4.1 nebo novější.
   
      ```powershell
      New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
      ```

   4. Udělte oprávnění Povolit registraci Azure Stack HCI a zaznamenání vlastních zásad souhlasu vytvořených v kroku 2:
   
      ```powershell
      $displayName = "Azure Stack HCI Registration Administrator "
      $description = "Custom AD role to allow registering Azure Stack HCI "
      $templateId = (New-Guid).Guid
      $allowedResourceAction =
      @(
             "microsoft.directory/applications/createAsOwner",
             "microsoft.directory/applications/delete",
             "microsoft.directory/applications/standard/read",
             "microsoft.directory/applications/credentials/update",
             "microsoft.directory/applications/permissions/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/read",
             "microsoft.directory/servicePrincipals/appRoleAssignments/read",
             "microsoft.directory/servicePrincipals/createAsOwner",
             "microsoft.directory/servicePrincipals/credentials/update",
             "microsoft.directory/servicePrincipals/permissions/update",
             "microsoft.directory/servicePrincipals/standard/read",
             "microsoft.directory/servicePrincipals/managePermissionGrantsForAll.AzSHCI-registration-consent-policy"
      )
      $rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
      ```

   5. Vytvořte novou vlastní roli AD:

      ```powershell
      $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
      ```

   6. Pomocí [těchto pokynů](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context)přiřaďte novou vlastní roli AD uživateli, který bude registrovat Azure Stack cluster HCI pomocí Azure.

## <a name="unregister-azure-stack-hci-using-windows-admin-center"></a>Zrušení registrace Azure Stack HCL pomocí centra pro správu Windows

Až budete připraveni vyřadit z provozu cluster Azure Stack HCI, stačí se ke clusteru připojit pomocí centra pro správu Windows a vybrat **Nastavení** v dolní části nabídky **nástroje** na levé straně. Pak vyberte **Azure Stack registrace rozhraní HCI** a klikněte na tlačítko **zrušit registraci** . Proces zrušení registrace automaticky vyčistí prostředek Azure, který představuje cluster, skupinu prostředků Azure (Pokud se skupina vytvořila během registrace a neobsahuje žádné další prostředky) a identitu aplikace Azure AD. Tím se zastaví všechny funkce monitorování, podpory a fakturace prostřednictvím ARC Azure.

   > [!NOTE]
   > Zrušení registrace Azure Stack clusteru HCI vyžaduje správce Azure Active Directory nebo jiného uživatele, který má delegovaná dostatečná oprávnění. Viz [Azure Active Directory oprávnění uživatele](#azure-active-directory-user-permissions).

## <a name="unregister-azure-stack-hci-using-powershell"></a>Zrušení registrace Azure Stack HCL pomocí PowerShellu

Pomocí rutiny můžete také `Unregister-AzStackHCI` zrušit registraci clusteru Azure Stack HCI. Rutinu můžete spustit buď na uzlu clusteru, nebo v počítači pro správu.

Možná budete muset nainstalovat nejnovější verzi `Az.StackHCI` modulu. Může se zobrazit výzva "jste si jisti, že chcete nainstalovat moduly z" PSGallery "?" na které byste měli odpovědět ano (Y).

```PowerShell
Install-Module -Name Az.StackHCI
```

### <a name="unregister-from-a-cluster-node"></a>Zrušení registrace uzlu clusteru

Pokud spouštíte `Unregister-AzStackHCI` rutinu na serveru v clusteru, použijte tuto syntaxi a zadejte ID předplatného Azure a také název prostředku pro Azure Stack clusteru HCI, který chcete zrušit.

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Zobrazí se výzva k návštěvě microsoft.com/devicelogin na jiném zařízení (například na počítači nebo telefonu), zadejte kód a přihlaste se k ověřování pomocí Azure.

### <a name="unregister-from-a-management-pc"></a>Zrušení registrace na počítači pro správu

Pokud spouštíte rutinu z počítače pro správu, budete také muset zadat název serveru v clusteru:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Automaticky se otevře okno interaktivní přihlášení do Azure. Přesné výzvy, které vidíte, se budou lišit v závislosti na nastavení zabezpečení (například dvojúrovňové ověřování). Podle pokynů se přihlaste.

## <a name="cleaning-up-after-a-cluster-that-was-not-properly-unregistered"></a>Vyčištění po neregistraci clusteru, který nebyl správně zaregistrován

Pokud uživatel zničí Azure Stack clusteru HCI bez zrušení jeho registrace, například při opakovaném vytvoření bitové kopie hostitelských serverů nebo při odstraňování uzlů virtuálních clusterů, budou artefakty v Azure zůstat v Azure. Ty jsou neškodné a neúčtují se ani nepoužívají prostředky, ale můžou Azure Portal. Pokud je chcete vyčistit, můžete je ručně odstranit.

Pokud chcete odstranit prostředek Azure Stack HCL, přejděte na jeho stránku v Azure Portal a vyberte **Odstranit** z panelu akcí v horní části. Zadáním názvu prostředku potvrďte odstranění a pak vyberte **Odstranit**. Pokud chcete odstranit identitu aplikace Azure AD, přejděte do **Azure AD**, pak na **Registrace aplikací** a najdete ji v části **všechny aplikace**. Vyberte možnost **Odstranit** a potvrdit.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Připojení Azure Stack HCl k Azure](../deploy/register-with-azure.md)
