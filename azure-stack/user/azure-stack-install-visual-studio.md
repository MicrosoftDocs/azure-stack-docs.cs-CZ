---
title: Instalace sady Visual Studio a připojení k centru Azure Stack
description: Naučte se instalovat Visual Studio a připojit se k rozbočovači Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2020
ms.openlocfilehash: 9fb0cf281fb97bc5cf255fb39507869b106d0a1b
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702954"
---
# <a name="install-visual-studio-and-connect-to-azure-stack-hub"></a>Instalace sady Visual Studio a připojení k centru Azure Stack

Pomocí sady Visual Studio můžete zapisovat a nasazovat [šablony](azure-stack-arm-templates.md) Azure Resource Manager do centra Azure Stack. Postup v tomto článku popisuje, jak nainstalovat Visual Studio do [centra Azure Stack](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) nebo na externí počítač, pokud plánujete používat centrum Azure Stack prostřednictvím [sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

## <a name="install-visual-studio"></a>Install Visual Studio

1. Stáhněte a spusťte [instalační program webové platformy](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Otevřete **Instalace webové platformy Microsoft**.

3. Vyhledejte **sadu Visual Studio Community 2015 s Microsoft Azure SDK – 2.9.6**. Klikněte na **Přidat**a potom na **nainstalovat**.

4. Odinstalujte **Microsoft Azure PowerShell** , která je nainstalovaná jako součást sady Azure SDK.

    ![Snímek obrazovky s postupem instalace WebPI](./media/azure-stack-install-visual-studio/image1.png)

5. [Nainstalujte PowerShell pro centrum Azure Stack](../operator/azure-stack-powershell-install.md).

6. Po dokončení instalace restartujte operační systém.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Připojení k centru Azure Stack pomocí Azure AD

1. Spusťte sadu Visual Studio.

2. V nabídce **zobrazení** vyberte **Průzkumník cloudu**.

3. V podokně Nový vyberte **Přidat účet** a přihlaste se pomocí přihlašovacích údajů pro Azure Active Directory (Azure AD).  

    ![Snímek obrazovky aplikace Cloud Explorer po přihlášení a připojení k rozbočovači Azure Stack](./media/azure-stack-install-visual-studio/image2.png)

Po přihlášení můžete [nasadit šablony](azure-stack-deploy-template-visual-studio.md) nebo procházet dostupné typy prostředků a skupiny prostředků a vytvořit vlastní šablony.  

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Připojení k centru Azure Stack pomocí AD FS

1. Spusťte sadu Visual Studio.

2. V **nabídce nástroje**vyberte **možnost možnosti**.

3. V **navigačním podokně** rozbalte **prostředí** a vyberte **účty**.

4. Vyberte **Přidat**a zadejte koncový bod uživatele Azure Resource Manageru. Pro Azure Stack Development Kit (ASDK) je adresa URL: `https://management.local.azurestack/external`.  V případě integrovaných systémů Azure Stack hub je adresa URL: `https://management.[Region}.[External FQDN]`.

    ![Přidat nový koncový bod Azure Cloud Discovery](./media/azure-stack-install-visual-studio/image5.png)

5. Vyberte **Přidat**.  

    Visual Studio volá správce prostředků Azure a zjišťuje koncové body, včetně koncového bodu ověřování pro Azure Directory federovaným Services (AD FS).

    ![Snímek obrazovky aplikace Cloud Explorer po přihlášení a připojení k rozbočovači Azure Stack](./media/azure-stack-install-visual-studio/image6.png)

6. V nabídce **zobrazení** vyberte **Průzkumník cloudu** .

7. Vyberte **Přidat účet** a přihlaste se pomocí přihlašovacích údajů AD FS.  

    ![Přihlášení k aplikaci Visual Studio v Průzkumníkovi cloudu](./media/azure-stack-install-visual-studio/image7.png)

    Průzkumník cloudu se dotazuje na dostupná předplatná. Můžete vybrat dostupné předplatné, které chcete spravovat.

    ![Výběr předplatných pro správu v Průzkumníkovi cloudu](./media/azure-stack-install-visual-studio/image8.png)

8. Procházejte stávající prostředky, skupiny prostředků nebo nasaďte šablony.

## <a name="next-steps"></a>Další kroky

- Další informace o Visual Studiu najdete [vedle](/visualstudio/install/install-visual-studio-versions-side-by-side) ostatních verzí sady Visual Studio.
- [Vytvořte šablony pro centrum Azure Stack](azure-stack-develop-templates.md).
