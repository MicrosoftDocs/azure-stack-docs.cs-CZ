---
title: Nasazení C# webovou aplikaci ASP .net do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení C# webové aplikace ASP.net do virtuálního počítače ve službě Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 3f925d7c6a7f08257dbcb054044403e1488d38cb
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482124"
---
# <a name="how-to-deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>Jak nasadit C# webové aplikace ASP.net do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač pro hostování vašeho C# (ASP.NET) webové aplikace ve službě Azure Stack. Tento článek ukazuje kroky budou pokračovat podle nastavení serveru, konfigurace serveru pro hostování vaší C# webové aplikace (ASP.NET) a pak nasazení vaší aplikace přímo ze sady Visual Studio.

C#je pro obecné účely, více paradigmaty programovací jazyk včetně silných typů, lexikálně s vymezeným oborem, imperativní, deklarativní, funkční, Obecné, objektově orientované a součást objektově orientovaného programování oborů. Další informace C# programovací jazyk a najít další zdroje informací o C#, najdete v článku [ C# průvodce](https://docs.microsoft.com/dotnet/csharp/).

Tento článek bude používat C# 6.0 aplikace pomocí ASP.NET Core 2.2 běží na serveru Windows 2016.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Vytvoření [virtuálního počítače s Windows serverem](azure-stack-quick-windows-portal.md).

2. Spusťte následující skript pro instalaci součásti na svém virtuálním počítači. Tento skript:
      - Instalace služby IIS (s konzolou pro správu).
      - Instalace technologie ASP.NET 4.6.

        ```PowerShell  
        # Install IIS (with Management Console)
        Install-WindowsFeature -name Web-Server -IncludeManagementTools
        
        # Install ASP.NET 4.6
        Install-WindowsFeature Web-Asp-Net45
        
        # Install Web Management Service
        Install-WindowsFeature -Name Web-Mgmt-Service
        ```

3. Stáhněte si [MSI pro nasazení webu 3.6](https://www.microsoft.com/download/details.aspx?id=43717). Nainstalujte z MSI a pak povolte na všechny funkce.

4. Sada .NET Core hostování nástroje 2.2 nainstalujte na server. Pokyny najdete v tématu [instalačního programu .NET Core](https://dotnet.microsoft.com/download/dotnet-core/2.2). Ujistěte se, že používáte stejnou verzi nástroje .NET Core na počítači pro vývoj a cílový server.

5. Vraťte se k portálu Azure Stack a otevřít porty v nastavení sítě pro virtuální počítač.

    1. Otevřete na portálu Azure Stack pro vašeho tenanta.
    2. Najdete váš virtuální počítač. Virtuální počítač může mít připnout na řídicí panel, nebo můžete vyhledávání pro virtuální počítač v **vyhledat prostředky** pole.
    3. Vyberte **sítě**.
    4. Vyberte **přidat pravidlo portu pro příchozí spojení** v rámci virtuálního počítače.
    1. Přidáte příchozí pravidlo zabezpečení pro následující porty:

    | Port | Protocol (Protokol) | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol aplikací pro distribuované, spolupráci, hypermédia informačních systémů. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 443 | HTTPS | Přenos protokolu HTTPS (Hypertext Secure) je rozšířením sady protokol HTTP (Hypertext Transfer). Používá se pro zabezpečenou komunikaci v počítačové síti. Klienti se připojí k vaší webové aplikaci buď veřejné IP adresy nebo DNS název vašeho virtuálního počítače. |
    | 22 | SSH | Secure Shell (SSH) je kryptografický síťový protokol pro bezpečné provozování síťové služby přes nezabezpečenou síť. Toto připojení použijete s klientem SSH ke konfiguraci virtuálního počítače a nasazení aplikace. |
    | 3389 | Protokol RDP | Volitelné. Remote Desktop Protocol umožňuje připojení ke vzdálené ploše použít grafické uživatelské rozhraní vašeho počítače.   |

    Pro každý z portů:

    1. Vyberte **jakékoli** zdroje.
    1. Typ `*` pro rozsah zdrojových portů.
    1. Vyberte **jakékoli** pro **cílové**.
    1. Přidejte port, který chcete otevřít pro **rozsah cílových portů**.
    1. Vyberte **jakékoli** pro **protokol**.
    1. Vyberte **povolit** pro **akce**.
    1. Ponechte výchozí nastavení pro **Priority**.
    1. **Název** a zadejte **popis** tak, aby vám může poznamenejte si, proč jste otevřeli port.
    1. Výběr možnosti Přidat.

5.  V **sítě** nastavení pro váš virtuální počítač ve službě Azure Stack, vytvoření názvu DNS pro váš server. Uživatelé můžou připojit k webu pomocí adresy URL.

    1. Otevřete na portálu Azure Stack pro vašeho tenanta.
    1. Najdete váš virtuální počítač. Virtuální počítač může mít připnout na řídicí panel, nebo můžete vyhledávání pro virtuální počítač v **vyhledat prostředky** pole.
    1. Vyberte **Přehled**.
    1. Vyberte **konfigurace** v rámci virtuálního počítače.
    1. Vyberte **dynamické** pro **přiřazení**.
    1. Zadejte popisek názvu DNS, třeba `mywebapp` tak, aby úplná adresa URL bude vypadat: `mywebapp.local.cloudapp.azurestack.external`.

## <a name="create-an-app"></a>Vytvoření aplikace 

Můžete použít vlastní webové aplikace nebo použijte tento příklad v [publikování aplikace ASP.NET Core do Azure pomocí sady Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
).

Tento článek popisuje, jak vytvářet a publikovat webovou aplikaci ASP.NET do Azure virtuálního počítače (VM) pomocí funkce publikování Microsoft Azure Virtual Machines v sadě Visual Studio 2017. Po instalaci a ověření, zda vaše aplikace běží místně, budete aktualizovat váš cíl publikování na virtuální počítač Windows ve výběrové potvrzování vašeho Azure.

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

Vytvořte cíl publikování do virtuálního počítače ve službě Azure Stack.

1. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a vyberte **publikovat**.

    ![Nasazení webové aplikace v ASP.NET do služby Azure Stack publikování](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

2.  V okně publikování vyberte nový profil.
3. Vyberte službu IIS, FTP atd.
4. Vyberte publikovat.

5.  Vyberte **Webdeploy** pro **metodu publikování**.
6.  Pro **Server** zadejte název DNS, který jste definovali dříve, jako například `w21902.local.cloudapp.azurestack.external`
7.  Pro **název lokality** typ `Default Web Site`.
8.  Pro **uživatelské jméno** zadejte uživatelské jméno pro tento počítač.
9.  Pro **heslo**, zadejte heslo pro tento počítač.
10. Pro **cílovou adresu URL** zadejte adresu URL webu, například `mywebapp.local.cloudapp.azurestack.external`.

    ![Nasazení webové aplikace ASP.NET – nakonfigurujete nástroj nasazení webu](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

9. Vyberte **ověřit připojení** k ověření konfigurace nasazení vašeho webu. a pak klikněte na tlačítko **Další**.
10. Nastavte vaše **konfigurace** jako **vydání**.
11. Nastavte **cílové rozhraní Framework** jako **netcoreapp2.2**.
12. Nastavte **cílit na modul Runtime** jako **přenosné**.
13. Vyberte **Uložit**.
14. Vyberte **Publikovat**.
15. Přejděte na nový server. Měli byste vidět spuštěné webové aplikace.

```HTTP  
    mywebapp.local.cloudapp.azurestack.external
```

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md)
- Další informace o [běžné nasazení pro službu Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).