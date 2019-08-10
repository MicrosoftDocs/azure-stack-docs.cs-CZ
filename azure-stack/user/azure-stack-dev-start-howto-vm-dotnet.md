---
title: Nasazení webové C# aplikace v ASP.NET do virtuálního počítače v Azure Stack | Microsoft Docs
description: Nasazení webové C# aplikace v ASP.NET do virtuálního počítače v Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 08/09/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 08/09/2019
ms.openlocfilehash: beddafb351af39f0a21a1cd0d7a7baa4ccfee28e
ms.sourcegitcommit: 94669fe8a55fadd3103e80be307e9e8c823bf746
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68940280"
---
# <a name="deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>Nasazení webové C# aplikace v ASP.NET do virtuálního počítače v Azure Stack

Můžete vytvořit virtuální počítač pro hostování webové aplikace v C# ASP.NET v Azure Stack. Tento článek popisuje pokyny, které je třeba provést při nastavování serveru, jeho konfiguraci pro hostování C# webové aplikace v ASP.NET a následné nasazení aplikace přímo ze sady Visual Studio.

V tomto článku se C# používá aplikace 6,0, která používá ASP.NET Core 2,2 spuštěnou na serveru Windows 2016.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Vytvořte [virtuální počítač s Windows serverem](azure-stack-quick-windows-portal.md).

1. Pokud chcete na svém VIRTUÁLNÍm počítači nainstalovat komponenty IIS (s konzolou pro správu) a součásti ASP.NET 4,6, spusťte následující skript:

    ```PowerShell  
    # Install IIS (with Management Console)
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Install ASP.NET 4.6
    Install-WindowsFeature Web-Asp-Net45
    
    # Install Web Management Service
    Install-WindowsFeature -Name Web-Mgmt-Service
    ```

1. Stáhněte [nasazení webu v 3.6](https://www.microsoft.com/download/details.aspx?id=43717). Nainstalujte ji ze souboru MSI a pak povolte všechny funkce.

1. Nainstalujte na server hostující sadu .NET Core 2,2. Pokyny najdete v tématu [instalační program .NET Core](https://dotnet.microsoft.com/download/dotnet-core/2.2). Ujistěte se, že používáte stejnou verzi .NET Core jak na vývojovém počítači, tak i na cílovém serveru.

1. Na portálu Azure Stack otevřete porty, které jsou uvedené v nastavení sítě pro váš virtuální počítač.

    a. Otevřete portál Azure Stack pro vašeho tenanta.

    b. Vyhledejte svůj virtuální počítač. Je možné, že jste virtuální počítač připnuli k řídicímu panelu, nebo ho můžete vyhledat v poli **Hledat prostředky** .

    c. Vyberte **sítě**.

    d. V části virtuální počítač vyberte **Přidat pravidlo portu pro příchozí spojení** .

    e. Přidejte příchozí pravidlo zabezpečení pro následující porty:

    | Port | Protocol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) je protokol používaný k doručování webových stránek ze serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresou. |
    | 443 | HTTPS | Protokol HTTPS (Hypertext Transfer Protocol Secure) je zabezpečená verze protokolu HTTP, která vyžaduje certifikát zabezpečení a umožňuje šifrovaný přenos informací.  |
    | 22 | SSH | Secure Shell (SSH) je zašifrovaný síťový protokol pro zabezpečenou komunikaci. K nakonfigurování virtuálního počítače a nasazení aplikace použijete toto připojení k klientovi SSH. |
    | 3389 | PROTOKOL RDP | Volitelné. Protokol RDP (Remote Desktop Protocol) umožňuje připojení ke vzdálené ploše pro použití grafického uživatelského rozhraní vašeho počítače.   |
    | 8172 | Vlastní | Port používaný nástrojem WebDeploy. |

    Pro každý port:

    a. Jako **zdroj**vyberte **libovolný**.

    b. Jako **rozsah zdrojového portu**zadejte hvězdičku ( **\*** ).

    c. Jako **cíl**vyberte **libovolný**.

    d. Pro **Rozsah cílových portů**přidejte port, který chcete otevřít.

    e. V případě **protokolu**vyberte možnost **libovolný**.

    f. V části **Akce** vyberte **Povolit**.

    g. V poli **Priorita**ponechte výchozí výběr.

    h. Zadejte **název** a **Popis** , který vám pomůžete mít na paměti, proč je port otevřený.

    i. Vyberte **Přidat**.

1.  V nastavení **sítě** pro virtuální počítač v Azure Stack vytvořte název DNS pro váš server. Uživatelé se můžou k webu připojit pomocí adresy URL.

    a. Otevřete portál Azure Stack pro vašeho tenanta.

    b. Vyhledejte svůj virtuální počítač. Je možné, že jste virtuální počítač připnuli k řídicímu panelu, nebo ho můžete vyhledat v poli **Hledat prostředky** .

    c. Vyberte **Přehled**.

    d. V části **virtuální počítač**vyberte **Konfigurovat**.

    e. Jako **přiřazení**vyberte **Dynamická**.

    f. Zadejte popisek názvu DNS, například **MyWebApp**, aby se vaše úplná adresa URL stala *MyWebApp. Local. cloudapp. azurestack. external*.

## <a name="create-an-app"></a>Vytvoření nové aplikace 

V [aplikaci Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
)můžete použít vlastní webovou aplikaci nebo příklad publikování aplikace ASP.NET Core do Azure. Tento článek popisuje, jak vytvořit a publikovat webovou aplikaci v ASP.NET na virtuálním počítači Azure pomocí funkce publikování v Azure Virtual Machines v aplikaci Visual Studio 2017. Po instalaci a zajistěte, aby vaše aplikace běžela místně, aktualizujte svůj cíl publikování na virtuální počítač s Windows ve vaší instanci Azure Stack.

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

Vytvořte cíl publikování na svém VIRTUÁLNÍm počítači v Azure Stack.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a pak vyberte **publikovat**.

    ![Nasazení webové aplikace v ASP.NET pro publikování Azure Stack](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

1. V okně **publikovat** vyberte **Nový profil**.
1. Vyberte **IIS**, **FTP**a tak dále.
1. Vyberte **Publikovat**.
1. V případě **metody publikování**vyberte **nasazení webu**.
1. Jako **Server** zadejte název DNS, který jste definovali dříve, například *w21902. Local. cloudapp. azurestack. external*.
1. Jako **název webového serveru**zadejte **výchozí web**.
1. Do pole **uživatelské jméno**zadejte uživatelské jméno počítače.
1. Jako **heslo**zadejte heslo pro tento počítač.
1. V poli **cílová adresa URL**zadejte adresu URL pro web, například *MyWebApp. Local. cloudapp. azurestack. external*.

    ![Nasazení webové aplikace v ASP.NET – konfigurace Nasazení webu](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

1. Pokud chcete ověřit konfiguraci webového nasazení, vyberte **ověřit připojení**a pak vyberte **Další**.
1. Nastavte **konfiguraci** jako **verzi**.
1. Nastavte **cílové rozhraní .NET Framework** jako **netcoreapp 2.2**.
1. Nastavte **cílový modul runtime** jako **přenosný**.
1. Vyberte **Uložit**.
1. Vyberte **Publikovat**.
1. Přejít na nový server. Měla by se zobrazit vaše spuštěná webová aplikace.

    ```http  
        mywebapp.local.cloudapp.azurestack.external
    ```

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [nastavit vývojové prostředí v Azure Stack](azure-stack-dev-start.md).
- Přečtěte si o [běžných nasazeních Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Informace o C# programovacím jazyce a hledání dalších prostředků pro C#najdete v [ C# průvodci](https://docs.microsoft.com/dotnet/csharp/) .
