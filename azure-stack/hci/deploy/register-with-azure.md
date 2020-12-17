---
title: Připojení Azure Stack HCI k Azure
description: Jak registrovat Azure Stack clusterů HCI pomocí Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/16/2020
ms.openlocfilehash: 95e0ed6b87fb501b31c024c5d2d886b4e1bce8ac
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620632"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Připojení Azure Stack HCI k Azure

> Platí pro: Azure Stack HCI v20H2

Azure Stack HCI se doručuje jako služba Azure a musí se zaregistrovat do 30 dnů od instalace podle podmínek pro Azure Online Services. Toto téma vysvětluje, jak zaregistrovat Azure Stack cluster HCI pomocí [Azure ARC](https://azure.microsoft.com/services/azure-arc/) pro monitorování, podporu, fakturaci a hybridní služby. Po registraci se vytvoří prostředek Azure Resource Manager, který bude představovat každý místní cluster s Azure Stack HCI, a efektivně rozšiřuje rovinu správy Azure na Azure Stack HCI. Informace se pravidelně synchronizují mezi prostředkem Azure a místními clustery.

   > [!IMPORTANT]
   > Vyžaduje se registrace v Azure. Dokud nebude cluster zaregistrován v Azure, Azure Stack operační systém HCI není platný, není podporován a má omezené funkce (například nebudete moci vytvářet virtuální počítače).

## <a name="prerequisites-for-registration"></a>Předpoklady pro registraci

Dokud nevytvoříte cluster Azure Stack HCI, nebudete moct s Azure registrovat. Aby byl cluster podporovaný, musí být uzly clusteru fyzickými servery. Virtuální počítače se dají použít k testování, ale musí podporovat rozhraní UEFI (Unified Extensible Firmware Interface) (UEFI), což znamená, že nemůžete použít virtuální počítače Hyper-V Generation 1. Registrace ARC Azure je nativní funkcí operačního systému Azure Stack HCI, takže není potřeba registrovat žádného agenta.

### <a name="internet-access"></a>Přístup k internetu

Azure Stack HCI se musí pravidelně připojovat ke veřejnému cloudu Azure. Pokud je odchozí připojení omezené externí podnikovou bránou firewall nebo proxy server, musí být nakonfigurovaná tak, aby umožňovala odchozí přístup k portu 443 (HTTPS) na omezeném počtu dobře známých IP adres Azure. Informace o tom, jak připravit brány firewall, najdete v tématu [Konfigurace bran firewall pro Azure Stack HCI](../concepts/configure-firewalls.md).

   > [!NOTE]
   > Proces registrace se pokusí kontaktovat Galerie prostředí PowerShell a ověří, zda máte nejnovější verzi nezbytných modulů prostředí PowerShell, například AZ a AzureAD. I když je Galerie prostředí PowerShell hostována v Azure, aktuálně nemá značku služby. Pokud nemůžete spustit výše uvedenou rutinu z počítače pro správu, který má odchozí přístup k Internetu, doporučujeme stáhnout moduly a ručně je přenést do uzlu clusteru, kde budete `Register-AzStackHCI` příkaz spouštět. Případně můžete [moduly nainstalovat v odpojeném scénáři](/powershell/scripting/gallery/how-to/working-with-local-psrepositories?view=powershell-7.1#installing-powershellget-on-a-disconnected-system).

### <a name="azure-subscription-and-permissions"></a>Předplatné Azure a oprávnění

Pokud ještě nemáte účet Azure, [vytvořte ho](https://azure.microsoft.com/).

Můžete použít existující předplatné libovolného typu:
- Bezplatný účet s kredity Azure [pro studenty](https://azure.microsoft.com/free/students/) nebo [předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- Předplatné s průběžnými [platbami](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) pomocí platební karty
- Předplatné získané prostřednictvím smlouva Enterprise (EA)
- Předplatné získané prostřednictvím programu Cloud Solution Provider (CSP)

Uživatel, který registruje cluster, musí mít oprávnění k předplatnému Azure:

- Registrace poskytovatele prostředků
- Vytváření, získávání a odstraňování prostředků Azure a skupin prostředků

Pokud je vaše předplatné Azure prostřednictvím programu EA nebo CSP, nejjednodušší způsob je požádat správce předplatného Azure, aby vašemu předplatnému přiřadil předdefinovanou roli Azure Owner (Vlastník) nebo Přispěvatel. Někteří správci však mohou preferovat více omezující možnosti. V takovém případě je možné vytvořit vlastní roli Azure specifickou pro Azure Stack registraci HCI pomocí následujících kroků:

1. Vytvořte soubor JSON s názvem **customHCIRole.js** s následujícím obsahem. Ujistěte se, že jste změnili <subscriptionID> ID vašeho předplatného Azure. Pokud chcete získat ID předplatného, přejděte na [Portal.Azure.com](https://portal.azure.com), přejděte na odběry a zkopírujte nebo vložte své ID ze seznamu.

   ```json
   {
     "Name": "Azure Stack HCI registration role”,
     "Id": null,
     "IsCustom": true,
     "Description": "Custom Azure role to allow subscription-level access to register Azure Stack HCI",
     "Actions": [
       "Microsoft.Resources/subscriptions/resourceGroups/write",
       "Microsoft.Resources/subscriptions/resourceGroups/read",
       "Microsoft.Resources/subscriptions/resourceGroups/delete",
       "Microsoft.AzureStackHCI/register/action",
       "Microsoft.AzureStackHCI/Unregister/Action",
       "Microsoft.AzureStackHCI/clusters/*"
     ],
     "NotActions": [
     ],
   "AssignableScopes": [
       "/subscriptions/<subscriptionId>"
     ]
   }
   ```

2. Vytvořte vlastní roli:

   ```powershell
   New-AzRoleDefinition -InputFile <path to customHCIRole.json>
   ```

3. Přiřaďte uživateli vlastní roli:

   ```powershell
   $user = get-AzAdUser -DisplayName <userdisplayname>
   $role = Get-AzRoleDefinition -Name "Azure Stack HCI registration role"
   New-AzRoleAssignment -ObjectId $user.Id -RoleDefinitionId $role.Id -Scope /subscriptions/<subscriptionid>
   ```

### <a name="azure-active-directory-permissions"></a>Azure Active Directory oprávnění

K dokončení procesu registrace budete také potřebovat příslušná Azure Active Directory oprávnění. Pokud je ještě nemáte, požádejte správce Azure AD, aby vám udělil souhlas nebo vám oprávnění delegovat. Další informace najdete v tématu [Správa registrace Azure](../manage/manage-azure-registration.md#azure-active-directory-app-permissions) .

## <a name="register-using-powershell"></a>Registrace pomocí PowerShellu

Pomocí následujícího postupu můžete zaregistrovat Azure Stack clusteru HCI s Azure pomocí počítače pro správu.

1. Na počítač pro správu nainstalujte požadované rutiny. Pokud zaregistrujete cluster nasazený z aktuální obecné dostupnosti (GA) Azure Stack HCL, stačí spustit následující příkaz. Pokud byl váš cluster nasazený z Public Preview Image, před pokusem o registraci v Azure se ujistěte, že jste na každý server v clusteru nainstalovali aktualizaci KB4586852 (verze Preview 23. listopadu 2020).

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Může se zobrazit výzva například "Přejete si, aby PowerShellGet instalovat a importovat zprostředkovatele NuGet?" na kterou byste měli odpovědět ano (Y).
   > - Může se vám zobrazit výzva "určitě chcete nainstalovat moduly z" PSGallery "?" na které byste měli odpovědět ano (Y).

2. Proveďte registraci pomocí názvu libovolného serveru v clusteru. Pokud chcete získat ID předplatného Azure, přejděte na [Portal.Azure.com](https://portal.azure.com), přejděte na odběry a zkopírujte nebo vložte své ID ze seznamu.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1 [–Credential] [-ResourceName] [-ResourceGroupName] [-Region]
   ```

   Tato syntaxe registruje cluster (z kterého je Server1 členem), jako aktuální uživatel, s výchozí oblastí Azure a cloudovým prostředím, a používá inteligentní výchozí názvy pro prostředek Azure a skupinu prostředků, ale pokud chcete, můžete do tohoto příkazu přidat parametry a zadat tyto hodnoty.

   Mějte na paměti, že uživatel, který spouští `Register-AzStackHCI` rutinu, musí mít [oprávnění Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-app-permissions), nebo se proces registrace nedokončí. místo toho se ukončí a ponechá souhlas správce o registraci. Jakmile budou udělena oprávnění, stačí znovu spustit `Register-AzStackHCI` úplnou registraci.

3. Ověřování pomocí Azure

   K dokončení procesu registrace se musíte ověřit (přihlásit) pomocí účtu Azure. Aby bylo možné pokračovat v registraci, musí mít váš účet přístup k předplatnému Azure, které jste zadali v kroku 4 výše. Zkopírujte kód, který jste zadali, přejděte na microsoft.com/devicelogin na jiném zařízení (třeba na počítač nebo na telefon), zadejte kód a přihlaste se tam. Toto je stejné prostředí, které Microsoft používá pro jiná zařízení s omezenými vstupními postupy, jako je Xbox.

Pracovní postup registrace zjistí, kdy jste se přihlásili, a pokračujte v jeho dokončení. Cluster by pak měl být schopný zobrazit v Azure Portal.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni:

- [Ověřit cluster](validate.md)

