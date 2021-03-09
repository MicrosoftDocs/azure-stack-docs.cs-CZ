---
title: Správa registrace clusteru HCI Azure Stack v Azure
description: Jak spravovat registraci Azure pro Azure Stack clustery HCI, pochopit stav registrace a zrušit registraci clusteru, až budete připraveni vyřadit z provozu.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 101d1cbcab097803e9c8a9fd9cd651dcf6fc80d1
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515630"
---
# <a name="manage-cluster-registration-with-azure"></a>Správa registrace clusteru v Azure

> Platí pro Azure Stack HCI verze 20H2

Po vytvoření clusteru Azure Stack HCI musíte [v Azure zaregistrovat centrum pro správu Windows](register-windows-admin-center.md) a pak [cluster zaregistrovat v Azure](../deploy/register-with-azure.md). Po registraci clusteru pravidelně synchronizuje informace mezi místním clusterem a cloudem. 

Tento článek vysvětluje, jak zobrazit stav registrace, udělit oprávnění Azure Active Directory (Azure AD) a zrušit registraci clusteru, až budete připraveni k vyřazení z provozu.

## <a name="view-registration-status-in-windows-admin-center"></a>Zobrazit stav registrace v centru pro správu Windows

Když se ke clusteru připojíte pomocí centra pro správu Windows, zobrazí se řídicí panel, který zobrazuje stav připojení Azure. **Připojeno** znamená, že cluster už je zaregistrovaný v Azure a během posledního dne se úspěšně synchronizuje do cloudu.

:::image type="content" source="media/manage-azure-registration/registration-status.png" alt-text="Snímek obrazovky zobrazující stav připojení clusteru na řídicím panelu centra pro správu systému Windows." lightbox="media/manage-azure-registration/registration-status.png":::

Další informace můžete získat tak, že v dolní části nabídky **nástroje** na levé straně vyberete **Nastavení** a pak vyberete **Azure Stack registraci HCI**.

:::image type="content" source="media/manage-azure-registration/azure-stack-hci-registration.png" alt-text="Snímek obrazovky zobrazující výběry pro získání informací o registraci Azure Stack H C" lightbox="media/manage-azure-registration/azure-stack-hci-registration.png":::

## <a name="view-registration-status-in-powershell"></a>Zobrazit stav registrace v PowerShellu

Chcete-li zobrazit stav registrace pomocí prostředí Windows PowerShell, použijte `Get-AzureStackHCI` rutinu prostředí PowerShell a `ClusterStatus` `RegistrationStatus` vlastnosti, a `ConnectionStatus` . 

Například po instalaci operačního systému Azure Stack HCI, ale ještě před vytvořením nebo připojením ke clusteru, `ClusterStatus` vlastnost zobrazuje `NotYet` stav:

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="Snímek obrazovky, který zobrazuje stav registrace Azure před vytvořením clusteru.":::

Po vytvoření clusteru se `RegistrationStatus` zobrazí pouze `NotYet` stav:

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="Snímek obrazovky, který zobrazuje stav registrace Azure po vytvoření clusteru.":::

V průběhu 30 dnů od instalace musíte zaregistrovat cluster Azure Stack HCI, jak je definováno v části Azure Online Services terms. Pokud jste cluster nevytvořili nebo jste do 30 dnů nepřipojili, zobrazí `ClusterStatus` se `OutOfPolicy` . Pokud jste cluster nezaregistrovali po 30 dnech, zobrazí `RegistrationStatus` se `OutOfPolicy` .

Po zaregistrování clusteru můžete zobrazit `ConnectionStatus` a `LastConnected` čas. `LastConnected`Čas obvykle spadá do posledního dne, pokud není cluster dočasně odpojený od Internetu. Cluster Azure Stack HCI může plně fungovat po dobu až 30 po sobě jdoucích dnů.

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="Snímek obrazovky, který zobrazuje stav registrace Azure po registraci.":::

Pokud překročíte maximální dobu offline operace, zobrazí `ConnectionStatus` se `OutOfPolicy` .

## <a name="assign-azure-ad-app-permissions"></a>Přiřazení oprávnění aplikace Azure AD

Kromě vytvoření prostředku Azure v rámci vašeho předplatného, registrace Azure Stack HCI vytvoří identitu aplikace ve vašem tenantovi Azure AD. Tato identita je koncepčně podobná uživateli. Tato identita aplikace zdědí název clusteru. Tato identita podle potřeby jedná jménem cloudové služby Azure Stack HCI v rámci vašeho předplatného.

Pokud je uživatel, který registruje cluster, správcem služby Azure AD nebo má dostatečná oprávnění, dojde k tomu automaticky. Není vyžadována žádná další akce. V opačném případě může být nutné, abyste od správce Azure AD od svého schválení dokončili registraci. Správce může buď výslovně udělit souhlas s aplikací, nebo může delegovat oprávnění, abyste mohli udělit souhlas aplikaci:

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Diagram, který zobrazuje oprávnění a identitu Azure Active Directory." border="false":::

Pokud chcete udělit souhlas, otevřete [Portal.Azure.com](https://portal.azure.com) a přihlaste se pomocí účtu Azure, který má dostatečná oprávnění ve službě Azure AD. Přejít na **Azure Active Directory**  >  **Registrace aplikací**. Vyberte identitu aplikace pojmenovanou po vašem clusteru a pak přejít na **oprávnění rozhraní API**.

Pro vydanou verzi Azure Stack HCL pro obecné dostupnosti vyžaduje aplikace následující oprávnění. Liší se od oprávnění aplikace, které byly požadovány ve verzi Public Preview.

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

Pro veřejnou verzi Preview byly oprávnění aplikace (nyní zastaralá):

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

Hledání od správce Azure AD může nějakou dobu trvat, takže `Register-AzStackHCI` rutina ukončí a ponechá registraci ve `pending admin consent` stavu (částečně dokončeno). Po udělení souhlasu znovu spusťte `Register-AzStackHCI` registraci.

## <a name="assign-azure-ad-user-permissions"></a>Přiřazení uživatelských oprávnění služby Azure AD

Uživatel, který se spustí, `Register-AzStackHCI` potřebuje oprávnění služby Azure AD:

- Vytvořit ( `New-Remove-AzureADApplication` ), získat ( `Get-Remove-AzureADApplication` ), nastavit () `Set-Remove-AzureADApplication` nebo odebrat ( `Remove-AzureADApplication` ) aplikace Azure AD.
- Create ( `New-Get-AzureADServicePrincipal` ) nebo Get ( `Get-AzureADServicePrincipal` ) instančního objektu služby Azure AD.
- Správa tajných klíčů aplikace Active Directory ( `New-Remove-AzureADApplicationKeyCredential` , `Get-Remove-AzureADApplicationKeyCredential` , nebo `Remove-AzureADApplicationKeyCredential` ).
- Udělit souhlas s použitím konkrétních oprávnění aplikace ( `New-AzureADApplicationKeyCredential` , `Get-AzureADApplicationKeyCredential` nebo `Remove-AzureADServiceAppRoleAssignments` ).

Existují tři způsoby, jak přiřadit tato oprávnění.

### <a name="option-1-allow-any-user-to-register-applications"></a>Možnost 1: Povolení registrace aplikací všem uživatelům

V Azure Active Directory přejít na **uživatelské nastavení**  >  **Registrace aplikací**. V části **Uživatelé můžou registrovat aplikace** vyberte **Ano**.

Tato možnost umožňuje každému uživateli registrovat aplikace. Uživatel ale pořád potřebuje správce Azure AD, aby mohl udělit souhlas při registraci clusteru. 

> [!NOTE]
> Tato možnost je nastavení na úrovni tenanta, takže nemusí být vhodná pro velké podnikové zákazníky.

### <a name="option-2-assign-the-cloud-application-administration-role"></a>Možnost 2: přiřazení role správce cloudové aplikace

Přiřaďte uživateli integrovanou roli Azure AD *pro správu cloudové aplikace* . Toto přiřazení umožní uživateli registrovat a rušit registraci clusterů bez nutnosti dalšího souhlasu správce služby Active Directory.

### <a name="option-3-create-a-custom-active-directory-role-and-consent-policy"></a>Možnost 3: Vytvoření vlastní role služby Active Directory a zásad pro vyjádření souhlasu

Nejvíce omezující možnost je vytvořit vlastní roli služby Active Directory s vlastními zásadami souhlasu, které deleguje souhlas správce v rámci tenanta pro požadovaná oprávnění pro službu Azure Stack HCI. Když přiřadíte tuto vlastní roli uživatelům, můžou si zaregistrovat i udělit souhlas bez nutnosti dalšího souhlasu správce služby Active Directory.

> [!NOTE]
> Tato možnost vyžaduje licenci Azure AD Premium. Používá vlastní role služby Active Directory a vlastní funkce zásad souhlasu, které jsou teď ve verzi Public Preview.

1. Připojte se k Azure AD:
   
   ```powershell
   Connect-AzureAD
   ```

2. Vytvořte vlastní zásadu souhlasu:

   ```powershell
   New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
   ```

3. Přidejte podmínku, která zahrnuje požadovaná oprávnění aplikace pro Azure Stack službu HCI, která má ID aplikace v 1322e676-dee7-41ee-A874-ac923822781c. 
   
   > [!NOTE]
   > Následující oprávnění jsou dostupná pro vydání GA Azure Stack HCL. Nebudou fungovat s verzí Public Preview, pokud jste nepoužívali [23. listopadu 2020, Preview Update (KB4586852)](https://support.microsoft.com/help/4595086/azure-stack-hci-release-notes-overview) na všechny servery v clusteru a stáhli AZ. StackHCI Module verze 0.4.1 nebo novější.
   
   ```powershell
   New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
   ```

4. Udělte oprávnění Povolit registraci Azure Stack HCI a zaznamená si vlastní zásadu souhlasu, kterou jste vytvořili v kroku 2:
   
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

5. Vytvořte novou vlastní roli služby Active Directory:

   ```powershell
   $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
   ```

6. Podle [těchto pokynů](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context) přiřaďte nové vlastní role služby Active Directory uživateli, který bude registrovat Azure Stack clusteru HCI s Azure.

## <a name="unregister-azure-stack-hci-by-using-windows-admin-center"></a>Zrušení registrace Azure Stack HCL pomocí centra pro správu Windows

Až budete připraveni vyřadit z provozu cluster HCI Azure Stack HCI, připojte se ke clusteru pomocí centra pro správu systému Windows. V dolní části nabídky **nástroje** na levé straně vyberte **Nastavení** . Pak vyberte **Azure Stack registrace rozhraní HCI** a vyberte tlačítko zrušit **registraci** . 

Proces zrušení registrace automaticky vyčistí prostředek Azure, který představuje cluster, skupinu prostředků Azure (Pokud se skupina vytvořila během registrace a neobsahuje žádné další prostředky) a identitu aplikace Azure AD. Toto vyčištění zastaví všechny funkce monitorování, podpory a fakturace prostřednictvím ARC Azure.

> [!NOTE]
> Zrušení registrace Azure Stack clusteru HCI vyžaduje správce Azure AD nebo jiného uživatele, který má [dostatečná oprávnění](#assign-azure-ad-user-permissions). 
>
> Pokud je vaše brána centra pro správu Windows registrovaná v jiném Azure Active Directory (tenant), než se použila k prvotnímu registraci clusteru, může dojít k potížím při pokusu o zrušení registrace clusteru pomocí centra pro správu systému Windows. Pokud k tomu dojde, použijte následující pokyny prostředí PowerShell.

## <a name="unregister-azure-stack-hci-by-using-powershell"></a>Zrušení registrace Azure Stack HCI pomocí prostředí PowerShell

Pomocí rutiny můžete také `Unregister-AzStackHCI` zrušit registraci clusteru Azure Stack HCI. Rutinu můžete spustit buď na uzlu clusteru, nebo v počítači pro správu.

Možná budete muset nainstalovat nejnovější verzi `Az.StackHCI` modulu. Pokud se zobrazí výzva `Are you sure you want to install the modules from 'PSGallery'?` , odpovězte Ano ( `Y` ).

```PowerShell
Install-Module -Name Az.StackHCI
```

### <a name="unregister-from-a-cluster-node"></a>Zrušení registrace uzlu clusteru

Pokud spouštíte `Unregister-AzStackHCI` rutinu na serveru v clusteru, použijte následující syntaxi. Zadejte ID předplatného Azure a název prostředku pro Azure Stack clusteru HCI, který chcete zrušit.

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Zobrazí se výzva k návštěvě microsoft.com/devicelogin na jiném zařízení (například na vašem počítači nebo telefonu). Zadejte kód a přihlaste se k ověřování pomocí Azure.

### <a name="unregister-from-a-management-pc"></a>Zrušení registrace na počítači pro správu

Pokud spouštíte rutinu z počítače pro správu, musíte také zadat název serveru v clusteru:

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

Zobrazí se interaktivní okno přihlášení do Azure. Přesné výzvy, které vidíte, se budou lišit v závislosti na nastavení zabezpečení (například dvojúrovňové ověřování). Podle pokynů se přihlaste.

## <a name="clean-up-after-a-cluster-that-was-not-properly-unregistered"></a>Vyčištění po neregistraci clusteru, který nebyl správně zaregistrován

Pokud uživatel zničí Azure Stack clusteru HCI bez zrušení jeho registrace, jako je třeba obnovení imagí hostitelských serverů nebo odstranění uzlů virtuálních clusterů, pak budou artefakty v Azure zůstat. Tyto artefakty jsou neškodné a neúčtují se ani nepoužívají prostředky, ale můžou Azure Portal. Pokud je chcete vyčistit, můžete je ručně odstranit.

Pokud chcete odstranit prostředek Azure Stack HCL, v Azure Portal vyberte jeho stránku a na panelu akcí v horní části vyberte **Odstranit** . Zadejte název prostředku pro potvrzení odstranění a pak vyberte **Odstranit**. 

Pokud chcete odstranit identitu aplikace Azure AD, přečtěte si v části aplikace **Azure AD**  >  **registrace**  >  **všech aplikací**. Vyberte možnost **Odstranit** a potvrdit.

Pomocí prostředí PowerShell můžete také odstranit prostředek Azure Stack HCI:

```PowerShell
Remove-AzResource -ResourceId "HCI001"
```

Je možné, že budete muset nainstalovat `Az.Resources` modul:

```PowerShell
Install-Module -Name Az.Resources
```

Pokud se skupina prostředků vytvořila během registrace a neobsahuje žádné další prostředky, můžete ji odstranit:

```PowerShell
Remove-AzResourceGroup -Name "HCI001-rg"
```

## <a name="next-steps"></a>Další kroky

Související informace najdete v těchto tématech:

- [Registrace centra pro správu Windows pomocí Azure](register-windows-admin-center.md)
- [Připojení Azure Stack HCl k Azure](../deploy/register-with-azure.md)