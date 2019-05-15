---
title: Nasazení C# webovou aplikaci ASP.NET do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení C# webové aplikace ASP.NET do virtuálního počítače ve službě Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b41c64d64a2c2abe6d1f145f11c2d4d84686b207
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617699"
---
# <a name="deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>Nasazení C# webové aplikace ASP.NET do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač (VM) na hostiteli vaše C# webové aplikace ASP.NET ve službě Azure Stack. Tento článek popisuje pokyny k nastavování vašeho serveru, nakonfigurujte jej pro hostování vašeho C# webová aplikace ASP.NET a pak nasadíte aplikaci přímo z Visual Studia.

C#je pro obecné účely, více paradigmaty programovací jazyk včetně silných typů, lexikálně s vymezeným oborem, imperativní, deklarativní, funkční, Obecné, objektově orientované a součást objektově orientovaného programování oborů. Další informace C# programovací jazyk a najít další zdroje informací o C#, najdete v článku [ C# průvodce](https://docs.microsoft.com/dotnet/csharp/).

Tento článek používá C# 6.0 aplikaci, která používá 2.2 technologie ASP.NET Core běží na serveru Windows 2016.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Vytvoření [virtuálního počítače s Windows serverem](azure-stack-quick-windows-portal.md).

1. Chcete-li nainstalovat službu IIS (s konzolou pro správu) a ASP.NET 4.6 součásti na svém virtuálním počítači, spusťte následující skript:

    ```PowerShell  
    # Install IIS (with Management Console)
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Install ASP.NET 4.6
    Install-WindowsFeature Web-Asp-Net45
    
    # Install Web Management Service
    Install-WindowsFeature -Name Web-Mgmt-Service
    ```

1. Stáhněte si [Webdeploy v3.6](https://www.microsoft.com/download/details.aspx?id=43717). Instalace ze souboru MSI a pak povolte všechny funkce.

1. Instalaci sady hostování aplikace .NET Core 2.2 na vašem serveru. Pokyny najdete v tématu [instalačního programu .NET Core](https://dotnet.microsoft.com/download/dotnet-core/2.2). Ujistěte se, že používáte stejnou verzi nástroje .NET Core na počítači pro vývoj a cílový server.

1. Na portálu Azure Stack otevřete porty, které jsou uvedeny v nastavení sítě pro virtuální počítač.

    a. Otevřete na portálu Azure Stack pro vašeho tenanta.

    b. Vyhledávání pro virtuální počítač. Virtuální počítač může mít připnout na řídicí panel nebo vám ho můžete vyhledat v **vyhledat prostředky** pole.

    c. Vyberte **sítě**.

    d. Vyberte **přidat pravidlo portu pro příchozí spojení** v rámci virtuálního počítače.

    e. Přidáte příchozí pravidlo zabezpečení pro následující porty:

    | Port | Protocol (Protokol) | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol aplikací pro distribuované, spolupráci, hypermédia informačních systémů. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 443 | HTTPS | Přenos protokolu HTTPS (Hypertext Secure) je rozšířením sady protokol HTTP (Hypertext Transfer). Používá se pro zabezpečenou komunikaci v počítačové síti. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 22 | SSH | Secure Shell (SSH) je kryptografický síťový protokol pro bezpečné provozování síťové služby přes nezabezpečenou síť. Pomocí tohoto připojení klienta SSH pro konfiguraci virtuálního počítače a nasaďte aplikaci. |
    | 3389 | Protokol RDP | Volitelné. Protokol RDP (Remote Desktop) umožňuje připojení ke vzdálené ploše na váš počítač použít grafické uživatelské rozhraní.   |

    Pro každý z portů:

    a. Pro **zdroj**vyberte **jakékoli**.

    b. Pro **zdrojový rozsah portů**, zadejte hvězdičku (**\***).

    c. Pro **cílové**vyberte **jakékoli**.

    d. Pro **rozsah cílových portů**, přidejte port, který chcete spustit.

    e. Pro **protokol**vyberte **jakékoli**.

    f. V části **Akce** vyberte **Povolit**.

    g. Pro **Priority**, ponechte výchozí výběr.

    h. Zadejte **název** a **popis** který usnadní zapamatování, proč je otevřený port.

    i. Vyberte **Přidat**.

1.  V **sítě** nastavení pro váš virtuální počítač ve službě Azure Stack, vytvoření názvu DNS pro váš server. Uživatelé můžou připojit k webu pomocí adresy URL.

    a. Otevřete na portálu Azure Stack pro vašeho tenanta.

    b. Vyhledávání pro virtuální počítač. Virtuální počítač může mít připnout na řídicí panel nebo vám ho můžete vyhledat v **vyhledat prostředky** pole.

    c. Vyberte **Přehled**.

    d. V části **VM**vyberte **konfigurovat**.

    e. Pro **přiřazení**vyberte **dynamické**.

    f. Zadejte popisek názvu DNS, jako například **mywebapp**, aby se stal úplnou adresu URL *mywebapp.local.cloudapp.azurestack.external*.

## <a name="create-an-app"></a>Vytvoření nové aplikace 

Můžete použít vlastní webové aplikace nebo v příkladu v [publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
). Tento článek popisuje, jak vytvářet a publikovat webovou aplikaci ASP.NET na virtuálním počítači Azure s použitím publikování funkce Azure Virtual Machines v sadě Visual Studio 2017. Po instalaci a zkontrolujte, že vaše aplikace běží místně, budete aktualizovat váš cíl publikování do virtuálního počítače Windows ve vaší instanci služby Azure Stack.

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

Vytvořte cíl publikování do virtuálního počítače ve službě Azure Stack.

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na projekt a pak vyberte **publikovat**.

    ![Nasazení webové aplikace ASP.NET do služby Azure Stack publikování](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

1. V **publikovat** okně **nový profil**.
1. Vyberte **IIS**, **FTP**, a tak dále.
1. Vyberte **Publikovat**.
1. Pro **metodu publikování**vyberte **Webdeploy**.
1. Pro **Server** zadejte název DNS, který jste definovali dříve, jako například *w21902.local.cloudapp.azurestack.external*.
1. Pro **název lokality**, zadejte **výchozí webový server**.
1. Pro **uživatelské jméno**, zadejte uživatelské jméno pro počítač.
1. Pro **heslo**, zadejte heslo pro tento počítač.
1. Pro **cílovou adresu URL**, zadejte adresu URL pro web, jako například *mywebapp.local.cloudapp.azurestack.external*.

    ![Nasazení webové aplikace ASP.NET – nakonfigurujete nástroj nasazení webu](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

1. K ověření vaší konfigurace nasazení webu, vyberte **ověřit připojení**a pak vyberte **Další**.
1. Nastavte **konfigurace** jako **vydání**.
1. Nastavte **cílové rozhraní Framework** jako **netcoreapp2.2**.
1. Nastavte **cílit na modul Runtime** jako **přenosné**.
1. Vyberte **Uložit**.
1. Vyberte **Publikovat**.
1. Přejdete na nový server. Měli byste vidět spuštěné webové aplikace.

    ```HTTP  
        mywebapp.local.cloudapp.azurestack.external
    ```

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nastavení vývojového prostředí ve službě Azure Stack](azure-stack-dev-start.md).
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
