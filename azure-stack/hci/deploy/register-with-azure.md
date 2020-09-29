---
title: Připojení Azure Stack HCl k Azure
description: Jak zaregistrovat Azure Stack HCL pomocí Azure
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: 621662110b2e842a432183dfb7415f3c8365907b
ms.sourcegitcommit: 034e61836038ca75199a0180337257189601cd12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91230491"
---
# <a name="connect-azure-stack-hci-to-azure"></a>Připojení Azure Stack HCl k Azure

> Platí pro: Azure Stack HCI v20H2

Azure Stack HCI se doručuje jako služba Azure a musí se zaregistrovat do 30 dnů od instalace podle podmínek pro Azure Online Services. Toto téma vysvětluje, jak zaregistrovat Azure Stack cluster HCI pomocí [Azure ARC](https://azure.microsoft.com/services/azure-arc/) pro monitorování, podporu, fakturaci a hybridní služby. Po registraci se vytvoří prostředek Azure Resource Manager, který bude představovat každý místní cluster s Azure Stack HCI, a efektivně rozšiřuje rovinu správy Azure na Azure Stack HCI. Informace se pravidelně synchronizují mezi prostředkem Azure a místním clusterem. 

## <a name="prerequisites-for-registration"></a>Předpoklady pro registraci

Dokud nevytvoříte cluster Azure Stack HCI, nebudete moct s Azure registrovat. Uzly můžou být fyzické nebo virtuální počítače, ale musí mít rozhraní UEFI (Unified Extensible Firmware Interface) (UEFI), což znamená, že nemůžete použít virtuální počítače Hyper-V Generation 1. Registrace ARC Azure je nativní funkcí Azure Stack HCL, takže není nutný žádný agent.

### <a name="internet-access"></a>Přístup k internetu

Uzly Azure Stack HCI potřebují připojení ke cloudu, aby se mohli připojit k Azure. Například odchozí příkazy příkazového testu by měly být úspěšné:

```PowerShell
C:\> ping bing.com
```

### <a name="azure-subscription"></a>Předplatné Azure

Pokud ještě nemáte účet Azure, [vytvořte ho](https://azure.microsoft.com/). 

Můžete použít existující předplatné libovolného typu:
- Bezplatný účet s kredity Azure [pro studenty](https://azure.microsoft.com/free/students/) nebo [předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)
- Předplatné s průběžnými [platbami](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) pomocí platební karty
- Předplatné získané prostřednictvím smlouva Enterprise (EA)
- Předplatné získané prostřednictvím programu Cloud Solution Provider (CSP)

### <a name="azure-active-directory-permissions"></a>Azure Active Directory oprávnění

K dokončení procesu registrace budete potřebovat Azure Active Directory oprávnění. Pokud je ještě nemáte, požádejte správce Azure AD, aby vám udělil oprávnění, nebo je delegovat. Další informace najdete v tématu [Správa registrace Azure](../manage/manage-azure-registration.md#azure-active-directory-permissions) .

## <a name="register-using-powershell"></a>Registrace pomocí PowerShellu

Pomocí následujícího postupu zaregistrujete Azure Stack clusteru HCI do Azure:

1. Připojte se k jednomu z uzlů clusteru tak, že otevřete relaci PowerShellu a zadáte následující příkaz:

   ```PowerShell
   Enter-PSSession <server-name>
   ```

2. Nainstalujte modul PowerShell pro Azure Stack HCL:

   ```PowerShell
   Install-WindowsFeature RSAT-Azure-Stack-HCI
   ```

3. Nainstalujte požadované rutiny:

   ```PowerShell
   Install-Module Az.StackHCI
   ```

   > [!NOTE]
   > 1. Může se zobrazit výzva například "Přejete si, aby PowerShellGet instalovat a importovat zprostředkovatele NuGet?" na kterou byste měli odpovědět ano (Y).
   > 2. Může se vám zobrazit výzva "určitě chcete nainstalovat moduly z" PSGallery "?" na které byste měli odpovědět ano (Y).
   > 3. Nakonec můžete předpokládat, že instalace celého modulu **AZ** Module by zahrnovala dílčí modul **StackHCI** a ta bude správně dlouhodobá. Pro každou standardní Azure PowerShell konvenci ale dílčí moduly ve verzi Preview nejsou zahrnuté automaticky. místo toho je třeba je explicitně zadat. Proto je třeba explicitně požádat o **AZ. StackHCI** , jak je uvedeno výše.

4. Proveďte vlastní registraci:

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" [-ResourceName] [-ResourceGroupName]
   ```

   Tato syntaxe registruje místní cluster (z nějž je místní server členem), jako aktuální uživatel, s výchozí oblastí Azure a cloudovým prostředím, a používá inteligentní výchozí názvy pro prostředek Azure a skupinu prostředků, ale pokud chcete, můžete zadat parametry pro zadání těchto hodnot.

   Mějte na paměti, že uživatel, který spouští `Register-AzStackHCI` rutinu, musí mít [oprávnění Azure Active Directory](../manage/manage-azure-registration.md#azure-active-directory-permissions), nebo se proces registrace nedokončí. místo toho se ukončí a ponechá souhlas správce o registraci. Jakmile budou udělena oprávnění, stačí znovu spustit `Register-AzStackHCI` úplnou registraci.

5. Ověřování pomocí Azure

   K dokončení procesu registrace se musíte ověřit (přihlásit) pomocí účtu Azure. Aby bylo možné pokračovat v registraci, musí mít váš účet přístup k předplatnému Azure, které jste zadali v kroku 4 výše. Zkopírujte kód, který jste zadali, přejděte na microsoft.com/devicelogin na jiném zařízení (třeba na počítač nebo na telefon), zadejte kód a přihlaste se tam. Toto je stejné prostředí, které Microsoft používá pro jiná zařízení s omezenými vstupními postupy, jako je Xbox.

Pracovní postup registrace zjistí, kdy jste se přihlásili, a pokračujte v jeho dokončení. Cluster by pak měl být schopný zobrazit v Azure Portal.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni:

- [Ověřit cluster](validate.md)
- [Vytváření svazků](../manage/create-volumes.md)
