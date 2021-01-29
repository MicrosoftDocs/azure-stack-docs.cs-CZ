---
title: Připojení Azure Stack HCl k Azure
description: Jak registrovat Azure Stack clusterů HCI pomocí Azure.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/28/2020
ms.openlocfilehash: 17e8758dfea300f6bc3e02609877dfed8f780383
ms.sourcegitcommit: b461597917b768412036bf852c911aa9871264b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050021"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Připojení Azure Stack HCl k Azure

> Platí pro: Azure Stack HCI v20H2

Azure Stack HCI se doručuje jako služba Azure a musí se zaregistrovat do 30 dnů od instalace podle podmínek pro Azure Online Services. Toto téma vysvětluje, jak zaregistrovat Azure Stack cluster HCI pomocí [Azure ARC](https://azure.microsoft.com/services/azure-arc/) pro monitorování, podporu, fakturaci a hybridní služby. Po registraci se vytvoří prostředek Azure Resource Manager, který bude představovat každý místní cluster s Azure Stack HCI, a efektivně rozšiřuje rovinu správy Azure na Azure Stack HCI. Informace se pravidelně synchronizují mezi prostředkem Azure a místními clustery. Registrace ARC Azure je nativní funkcí operačního systému Azure Stack HCI, takže není potřeba registrovat žádného agenta.

   > [!IMPORTANT]
   > Vyžaduje se registrace v Azure a váš cluster není plně podporovaný, dokud nebude vaše registrace aktivní. Pokud jste cluster nezaregistrovali v Azure po nasazení nebo pokud je váš cluster registrovaný, ale nepřipojil se k Azure po dobu delší než 30 dnů, systém nedovolí vytvořit nebo přidat nové virtuální počítače. Pokud k tomu dojde, při pokusu o vytvoření virtuálních počítačů se zobrazí následující chybová zpráva:
   >
   > *Při konfiguraci role virtuálního počítače pro VMName došlo k chybě. Úloha se nezdařila. Při otevírání clusterových rolí VMName došlo k chybě. Přistupovaná služba je licencovaná pro určitý počet připojení. V tuto chvíli není možné ve službě provést žádná další připojení, protože už existuje tolik připojení, kolik jich služba může přijmout.*
   >
   > Řešením je umožnit odchozí připojení k Azure a ujistit se, že je váš cluster registrovaný, jak je popsáno v tomto tématu.

## <a name="prerequisites-for-registration"></a>Předpoklady pro registraci

Dokud nevytvoříte cluster Azure Stack HCI, nebudete moct s Azure registrovat. Aby byl cluster podporovaný, musí být uzly clusteru fyzickými servery. Virtuální počítače se dají použít k testování, ale musí podporovat rozhraní UEFI (Unified Extensible Firmware Interface) (UEFI), což znamená, že nemůžete použít virtuální počítače Hyper-V Generation 1.

Pro nejjednodušší možnosti registrace požádejte správce Azure AD, aby registraci dokončil buď pomocí centra pro správu Windows, nebo pomocí PowerShellu.

   > [!IMPORTANT]
   > Pokud plánujete zaregistrovat cluster pomocí centra pro správu Windows, musíte nejdřív [zaregistrovat bránu centra pro správu Windows](../manage/register-windows-admin-center.md) s Azure pomocí stejného ID předplatného Azure a ID tenanta, které plánujete použít pro registraci clusteru.

### <a name="internet-access"></a>Přístup k internetu

Azure Stack HCI se musí pravidelně připojovat k veřejnému cloudu Azure. Pokud je odchozí připojení omezené externí podnikovou bránou firewall nebo proxy server, musí být nakonfigurovaná tak, aby umožňovala odchozí přístup k portu 443 (HTTPS) na omezeném počtu dobře známých IP adres Azure. Informace o tom, jak připravit brány firewall, najdete v tématu [Konfigurace bran firewall pro Azure Stack HCI](../concepts/configure-firewalls.md).

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

## <a name="register-a-cluster-using-windows-admin-center"></a>Registrace clusteru pomocí centra pro správu Windows

Nejjednodušší způsob, jak zaregistrovat Azure Stack clusteru HCI, je použití centra pro správu systému Windows. Mějte na paměti, že uživatel musí mít [Azure Active Directory oprávnění](../manage/manage-azure-registration.md#azure-active-directory-app-permissions), jinak se proces registrace nedokončí. místo toho se ukončí a ponechá registraci čeká na schválení správcem.

1. Než začnete s registrací, musíte nejdřív [zaregistrovat bránu centra pro správu Windows](../manage/register-windows-admin-center.md) pomocí Azure, pokud jste to ještě neudělali.

   > [!IMPORTANT]
   > Při registraci centra pro správu Windows v Azure je důležité použít stejné ID předplatného Azure a ID tenanta, které plánujete použít pro vlastní registraci clusteru. ID tenanta Azure AD představuje konkrétní instanci služby Azure AD obsahující účty a skupiny, zatímco ID předplatného Azure představuje smlouvu k používání prostředků Azure, pro které se účtují poplatky. ID tenanta zjistíte tak, že přejdete na [Portal.Azure.com](https://portal.azure.com) a vyberete **Azure Active Directory**. Vaše ID tenanta se zobrazí v části **informace o tenantovi**. Pokud chcete získat ID předplatného Azure, přejděte na **odběry** a zkopírujte nebo vložte své ID ze seznamu.

2. Otevřete centrum pro správu systému Windows a v levém dolním rohu nabídky **nástroje** vyberte **Nastavení** . Pak vyberte **Azure Stack registraci HCI** z dolní části nabídky **Nastavení** . Pokud váš cluster ještě není zaregistrovaný v Azure, pak **stav registrace** nebude **zaregistrovaný**. Pokračujte kliknutím na tlačítko **registr** .

3. K dokončení procesu registrace se musíte ověřit (přihlásit) pomocí účtu Azure. Váš účet musí mít přístup k předplatnému Azure, které jste zadali při registraci brány centra pro správu Windows, aby bylo možné pokračovat v registraci. Zkopírujte kód, který jste zadali, přejděte na microsoft.com/devicelogin na jiném zařízení (třeba na počítač nebo na telefon), zadejte kód a přihlaste se tam. Pracovní postup registrace detekuje, že jste se přihlásili, a bude pokračovat k dokončení. Váš cluster by se pak měl zobrazit na webu Azure Portal.

## <a name="register-a-cluster-using-powershell"></a>Registrace clusteru pomocí prostředí PowerShell

Pomocí následujícího postupu můžete zaregistrovat Azure Stack clusteru HCI s Azure pomocí počítače pro správu.

1. Na počítač pro správu nainstalujte požadované rutiny. Pokud registrujete cluster nasazený z aktuální obecně dostupné image Azure Stack HCI, stačí spustit následující příkaz. Pokud byl váš cluster nasazený z Public Preview Image, před pokusem o registraci v Azure se ujistěte, že jste na každý server v clusteru nainstalovali aktualizaci KB4586852 (verze Preview 23. listopadu 2020).

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - Může se zobrazit výzva, například přejete si, **aby PowerShellGet instalovat a importovat zprostředkovatele NuGet?** na který byste měli odpovědět **Ano** (Y).
   > - Může se vám zobrazit výzva, **že chcete nainstalovat moduly z ' PSGallery '?** na kterou byste měli odpovědět **Ano** (Y).

2. Proveďte registraci pomocí názvu libovolného serveru v clusteru. Pokud chcete získat ID vašeho předplatného Azure, přejděte na [Portal.Azure.com](https://portal.azure.com), přejděte na **odběry** a ZKOPÍRUJTE nebo vložte své ID ze seznamu.

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1
   ```

   Tato syntaxe registruje cluster (z kterého je Server1 členem), jako aktuální uživatel s výchozí oblastí Azure a cloudovým prostředím, a s využitím inteligentních výchozích názvů pro prostředek Azure a skupinu prostředků. `-Region` `-ResourceName` K zadání těchto hodnot můžete také přidat volitelné parametry, a `-ResourceGroupName` pro tento příkaz.

   Mějte na paměti, že uživatel, který spouští `Register-AzStackHCI` rutinu, musí mít [oprávnění Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-app-permissions), jinak se proces registrace nedokončí. místo toho se ukončí registrace, která čeká na schválení správcem. Jakmile budou udělena oprávnění, stačí znovu spustit `Register-AzStackHCI` úplnou registraci.

3. Ověřování pomocí Azure

   K dokončení procesu registrace se musíte ověřit (přihlásit) pomocí účtu Azure. Aby bylo možné pokračovat v registraci, musí mít váš účet přístup k předplatnému Azure, které jste zadali v kroku 2 výše. Zkopírujte kód, který jste zadali, přejděte na microsoft.com/devicelogin na jiném zařízení (třeba na počítač nebo na telefon), zadejte kód a přihlaste se tam. Pracovní postup registrace detekuje, že jste se přihlásili, a bude pokračovat k dokončení. Váš cluster by se pak měl zobrazit na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni:

- [Ověřit cluster](validate.md)

