---
title: Nasaďte aplikaci, která používá místní data, a škálujte mezi cloudy pomocí Azure a centra Azure Stack.
description: Naučte se, jak nasadit aplikaci, která používá místní data, a škálujte mezi cloudy pomocí Azure a centra Azure Stack.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 1b21766561c39cf795665e8229eb1763acb89465
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881786"
---
# <a name="deploy-an-app-that-uses-on-premises-data-and-scales-cross-cloud-using-azure-and-azure-stack-hub"></a>Nasaďte aplikaci, která používá místní data, a škálujte mezi cloudy pomocí Azure a centra Azure Stack.

V tomto průvodci se dozvíte, jak nasadit hybridní aplikaci, která zahrnuje Azure i Azure Stack hub, a používá jeden místní zdroj dat.

Pomocí hybridního cloudového řešení můžete kombinovat výhody dodržování předpisů privátního cloudu s škálovatelností veřejného cloudu. Kromě toho můžou vývojáři využít výhody Microsoft Developer ekosystému a využívat jejich dovednosti v cloudových i místních prostředích.

## <a name="overview-and-assumptions"></a>Přehled a předpoklady

Postupujte podle tohoto kurzu a nastavte pracovní postup, který vývojářům umožní nasadit identickou webovou aplikaci do veřejného cloudu a privátního cloudu. Tato aplikace má přístup k síti směrovatelné přes Internet, která je hostovaná v privátním cloudu. Tyto webové aplikace jsou monitorovány a když je špička v provozu, program upraví záznamy DNS pro přesměrování provozu do veřejného cloudu. Když provoz na úrovni před špičkou klesne, provoz se směruje zpátky do privátního cloudu.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> - Nasaďte SQL Server databázový server s hybridním připojením.
> - Připojte webovou aplikaci v globálním Azure k hybridní síti.
> - Nakonfigurujte DNS pro škálování mezi cloudy.
> - Nakonfigurujte certifikáty SSL pro škálování mezi cloudy.
> - Nakonfigurujte a nasaďte webovou aplikaci.
> - Vytvořte profil Traffic Manager a nakonfigurujte ho pro škálování mezi cloudy.
> - Nastavte Application Insights monitorování a upozorňování na zvýšení provozu.
> - Nakonfigurujte automatické přepínání provozu mezi globálním centrem Azure a Azure Stack.

> [!Tip]  
> ![hybridní pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Centrum Microsoft Azure Stack je rozšířením Azure. Centrum Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí. tím se umožní jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](overview-app-design-considerations.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.

### <a name="assumptions"></a>Předpoklady

V tomto kurzu se předpokládá, že máte základní znalosti globálního centra Azure a centra Azure Stack. Pokud se chcete dozvědět víc, než začnete s kurzem, přečtěte si tyto články:

 - [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Klíčové koncepty centra Azure Stack](../operator/azure-stack-overview.md)

V tomto kurzu se taky předpokládá, že máte předplatné Azure. Pokud předplatné nemáte, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto řešením, ujistěte se, že splňujete následující požadavky:

- Azure Stack ASDK (hub Development Kit) nebo předplatné v integrovaném systému Azure Stack hub. Pokud chcete nasadit Azure Stack hub Development Kit, postupujte podle pokynů v tématu [nasazení ASDK pomocí instalačního programu](../asdk/asdk-install.md).
- Vaše instalace centra Azure Stack by měla mít nainstalované následující:
  - Azure App Service. K nasazení a konfiguraci Azure App Service ve vašem prostředí můžete použít operátor centra Azure Stack. Tento kurz vyžaduje, aby v App Service bylo aspoň jedna (1) dostupná vyhrazená role pracovního procesu.
  - Bitová kopie systému Windows Server 2016.
  - Windows Server 2016 s imagí Microsoft SQL Server.
  - Příslušné plány a nabídky.
  - Název domény pro vaši webovou aplikaci. Pokud název domény nemáte, můžete si ho koupit od poskytovatele domény, jako je GoDaddy, Bluehost a InMotion.
- Certifikát SSL pro vaši doménu od důvěryhodné certifikační autority, jako je LetsEncrypt.
- Webová aplikace, která komunikuje s databází SQL Server a podporuje Application Insights. Ukázkovou aplikaci [dotnetcore-SQLDB-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) si můžete stáhnout z GitHubu.
- Hybridní síť mezi virtuální sítí Azure a virtuální sítí centra Azure Stack. Podrobné pokyny najdete v tématu [Konfigurace připojení hybridního cloudu pomocí Azure a centra Azure Stack](solution-deployment-guide-connectivity.md).

- Hybridní kanál průběžné integrace nebo průběžného nasazování (CI/CD) s privátním agentem sestavení v centru Azure Stack. Podrobné pokyny najdete v tématu [Konfigurace hybridní cloudové identity s aplikacemi Azure a Azure Stack hub](solution-deployment-guide-identity.md).

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Nasazení SQL Server databázového serveru s hybridním připojením

1. Přihlašovat se k portálu Azure Stack User Portal.

2. Na **řídicím panelu**vyberte **Marketplace**.

    ![Tržiště centra Azure Stack](media/solution-deployment-guide-hybrid/image1.png)

3. V **tržišti**vyberte **COMPUTE**a pak klikněte na **Další**. V části **Další**vyberte **bezplatnou licenci SQL Server: SQL Server 2017 vývojář v imagi Windows serveru** .

    ![Vyberte image virtuálního počítače.](media/solution-deployment-guide-hybrid/image2.png)

4. **Licence na bezplatnou SQL Server: SQL Server 2017 vývojář na Windows serveru**vyberte **vytvořit**.

5. **Základy > nakonfigurovat základní nastavení**, zadat **název** virtuálního počítače (VM), **uživatelské jméno** pro SQL Server SA a **heslo** pro SA.  V rozevíracím seznamu **předplatné** vyberte předplatné, na které nasazujete. V poli **Skupina prostředků** **Vyberte vybrat existující** a vložte virtuální počítač do stejné skupiny prostředků, jako je vaše webová aplikace Azure Stack hub.

    ![Konfigurace základního nastavení pro virtuální počítač](media/solution-deployment-guide-hybrid/image3.png)

6. V části **Velikost**vyberte velikost pro svůj virtuální počítač. Pro tento kurz doporučujeme A2_Standard nebo DS2_V2_Standard.

7. V části **nastavení > nakonfigurovat volitelné funkce**nakonfigurujte následující nastavení:

   - **Účet úložiště**: vytvořte nový účet, pokud ho potřebujete.
   - **Virtuální síť**:

     > [!Important]  
     > Ujistěte se, že je váš virtuální počítač SQL Server nasazený ve stejné virtuální síti jako brány VPN.

   - **Veřejná IP adresa**: použijte výchozí nastavení.
   - **Skupina zabezpečení sítě**: (NSG). Vytvořte nový NSG.
   - **Rozšíření a monitorování**: ponechte výchozí nastavení.
   - **Účet úložiště diagnostiky**: vytvořte nový účet, pokud ho potřebujete.
   - Kliknutím na **OK** uložte svou konfiguraci.

     ![Konfigurace volitelných funkcí](media/solution-deployment-guide-hybrid/image4.png)

8. V části **nastavení SQL Server**nakonfigurujte následující nastavení:
   - V případě **připojení SQL**vyberte možnost **veřejné (Internet)** .
   - V poli **port**ponechte výchozí hodnotu **1433**.
   - Pro **ověřování SQL**vyberte **Povolit**.

     > [!Note]  
     > Pokud povolíte ověřování SQL, mělo by se automaticky naplnit informace o "SQLAdmin", které jste nakonfigurovali v **základních**informacích.

   - U zbývajících nastavení ponechte výchozí nastavení. Vyberte **OK**.

     ![Konfigurace nastavení SQL Server](media/solution-deployment-guide-hybrid/image5.png)

9. V části **Souhrn**Zkontrolujte konfiguraci virtuálního počítače a pak kliknutím na **tlačítko OK** spusťte nasazení.

    ![Souhrn konfigurace](media/solution-deployment-guide-hybrid/image6.png)

10. Vytvoření nového virtuálního počítače nějakou dobu trvá. STAV virtuálních počítačů můžete zobrazit na **virtuálních počítačích**.

    ![Virtuální počítače](media/solution-deployment-guide-hybrid/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack-hub"></a>Vytváření webových aplikací v Azure a centra Azure Stack

Azure App Service zjednodušuje spouštění a správu webové aplikace. Vzhledem k tomu, že centrum Azure Stack je konzistentní s Azure, může App Service běžet v obou prostředích. K hostování vaší aplikace použijete App Service.

### <a name="create-web-apps"></a>Vytváření webových aplikací

1. Pomocí pokynů v tématu [Správa plánu App Service v Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan)vytvořte webovou aplikaci v Azure. Nezapomeňte umístit webovou aplikaci do stejného předplatného a skupiny prostředků, jako je vaše hybridní síť.

2. V Azure Stackovém centru opakujte předchozí krok (1).

### <a name="add-route-for-azure-stack-hub"></a>Přidat trasu pro Azure Stack hub

App Service v centru Azure Stack musí být směrovatelné z veřejného Internetu a umožnit tak uživatelům přístup k vaší aplikaci. Pokud je vaše centrum Azure Stack dostupné z Internetu, poznamenejte si veřejnou IP adresu nebo adresu URL pro webovou aplikaci Azure Stack hub.

Pokud používáte ASDK, můžete [nakonfigurovat mapování statického překladu adres (NAT)](../operator/azure-stack-create-vpn-connection-one-node.md#configure-the-nat-vm-on-each-asdk-for-gateway-traversal) , které vystavuje App Service mimo virtuální prostředí.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Připojení webové aplikace v Azure k hybridní síti

Aby bylo možné zajistit propojení mezi webovým front-end v Azure a databází SQL Server v centru Azure Stack, musí být webová aplikace připojená k hybridní síti mezi Azure a centrem Azure Stack. Pokud chcete povolit připojení, budete muset:

- Nakonfigurujte připojení Point-to-site.
- Nakonfigurujte webovou aplikaci.
- Upravte bránu místní sítě v centru Azure Stack.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Konfigurace virtuální sítě Azure pro připojení Point-to-site

Brána virtuální sítě na straně Azure hybridní sítě musí umožňovat připojení typu Point-to-site k integraci s Azure App Service.

1. V Azure přejděte na stránku brány virtuální sítě. V části **Nastavení**vyberte **Konfigurace Point-to-site**.

    ![Možnost Point-to-site](media/solution-deployment-guide-hybrid/image8.png)

2. Vyberte **Konfigurovat nyní** pro konfiguraci Point-to-site.

    ![Spustit konfiguraci Point-to-site](media/solution-deployment-guide-hybrid/image9.png)

3. Na stránce konfigurace **Point-to-site** zadejte rozsah privátních IP adres, který chcete použít ve **fondu adres**.

   > [!Note]  
   > Ujistěte se, že se rozsah, který zadáte, nepřekrývá s žádným z rozsahů adres, které už jsou využívané v podsítích v globálním komponentě Azure nebo Azure Stack hub hybridní sítě.

   V části **Typ tunelu**zrušte ověření **IKEv2 VPN**. Výběrem **Uložit** dokončete konfiguraci Point-to-site.

   ![Nastavení Point-to-site](media/solution-deployment-guide-hybrid/image10.png)

### <a name="integrate-the-azure-app-service-app-with-the-hybrid-network"></a>Integrace aplikace Azure App Service s hybridní sítí

1. Pokud chcete připojit aplikaci k virtuální síti Azure, postupujte podle pokynů v části [Brána požadovaná integrace virtuální](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#gateway-required-vnet-integration)sítě.

2. Přejděte do **Nastavení** pro App Service plán hostování webové aplikace. V **Nastavení**vyberte **sítě**.

    ![Konfigurace sítě](media/solution-deployment-guide-hybrid/image11.png)

3. V **Integrace virtuální sítě** **pro správu vyberte kliknutím sem**.

    ![Správa integrace virtuální sítě](media/solution-deployment-guide-hybrid/image12.png)

4. Vyberte virtuální síť, kterou chcete nakonfigurovat. V části **IP adresy SMĚROVANÉ do virtuální**sítě zadejte rozsah IP adres pro virtuální síť Azure, virtuální síť centra Azure Stack a adresní prostory Point-to-site. Vyberte **Uložit** a ověřte a uložte tato nastavení.

    ![Rozsahy IP adres, které se mají směrovat](media/solution-deployment-guide-hybrid/image13.png)

Další informace o tom, jak se App Service integruje s Azure virtuální sítě, najdete v tématu [integrace aplikace s využitím azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-hub-virtual-network"></a>Konfigurace virtuální sítě centra Azure Stack

Brána místní sítě ve virtuální síti centra Azure Stack musí být nakonfigurovaná tak, aby směrovala provoz z rozsahu adres App Service Point-to-site.

1. V centru Azure Stack přejděte na **Brána místní sítě**. V části **Nastavení** vyberte **Konfigurace**.

    ![Možnost konfigurace brány](media/solution-deployment-guide-hybrid/image14.png)

2. Do pole **adresní prostor**zadejte rozsah adres Point-to-site pro bránu virtuální sítě v Azure.

    ![Adresní prostor Point-to-site](media/solution-deployment-guide-hybrid/image15.png)

3. Vyberte **Uložit** a ověřte a uložte konfiguraci.

## <a name="configure-dns-for-cross-cloud-scaling"></a>Konfigurace DNS pro škálování mezi cloudy

Díky správné konfiguraci DNS pro různé cloudové aplikace můžou uživatelé přistupovat k globálním instancím Azure a Azure Stack hub vaší webové aplikace. Konfigurace DNS pro tento kurz také umožňuje službě Azure Traffic Manager směrovat provoz, když se zatížení zvýší nebo sníží.

Tento kurz používá Azure DNS ke správě DNS, protože App Service domény nebudou fungovat.

### <a name="create-subdomains"></a>Vytvořit subdomény

Vzhledem k tomu, že Traffic Manager spoléhá na záznamy CNAME DNS, je pro správné směrování provozu do koncových bodů nutná subdoména. Další informace o záznamech DNS a mapování domén najdete v tématu [mapování domén pomocí Traffic Manager](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name).

Pro koncový bod Azure vytvoříte subdoménu, kterou můžou uživatelé používat pro přístup k vaší webové aplikaci. Pro tento kurz můžete použít **App.Northwind.com**, ale tuto hodnotu byste měli přizpůsobit na základě vaší vlastní domény.

Také budete muset vytvořit subdoménu se záznamem A pro koncový bod centra Azure Stack. Můžete použít **azurestack.Northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Konfigurace vlastní domény v Azure

1. Přidejte název hostitele **App.Northwind.com** do webové aplikace Azure tak, že [namapujete CNAME na Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack-hub"></a>Konfigurace vlastních domén v centru Azure Stack

1. Přidejte název hostitele **azurestack.Northwind.com** do webové aplikace centra Azure Stack tak, že [namapujete záznam a na Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Pro aplikaci App Service použijte IP adresu, kterou lze směrovat na Internet.

2. Přidejte název hostitele **App.Northwind.com** do webové aplikace centra Azure Stack tak, že [namapujete CNAME na Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Jako cíl pro záznam CNAME použijte název hostitele, který jste nakonfigurovali v předchozím kroku (1).

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Konfigurace certifikátů SSL pro škálování mezi cloudy

Je důležité zajistit, aby citlivá data shromažďovaná vaší webovou aplikací byla zabezpečená při přenosu do a při ukládání do databáze SQL.

Webové aplikace Azure a centra Azure Stack nakonfigurujete tak, aby používaly certifikáty SSL pro všechny příchozí přenosy.

### <a name="add-ssl-to-azure-and-azure-stack-hub"></a>Přidání SSL do Azure a centra Azure Stack

Přidání protokolu SSL do Azure:

1. Ujistěte se, že certifikát SSL, který získáte, je platný pro subdoménu, kterou jste vytvořili. (Používání certifikátů se zástupnými znaky je v pořádku.)

2. V Azure postupujte podle pokynů v části **Příprava vaší webové aplikace** a **vytvoření vazby certifikátu SSL** ve [vazbě existujícího vlastního certifikátu SSL k Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) článku. Jako **typ SSL**vyberte **SSL založené na sni** .

3. Přesměrujte veškerý provoz na port HTTPS. Postupujte podle pokynů v části **vyhovět protokolu HTTPS** v tématu [vytvoření vazby EXISTUJÍCÍHO vlastního certifikátu SSL k Azure Web Apps](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) článku.

Postup přidání protokolu SSL do centra Azure Stack:

- Opakujte kroky 1-3, které jste použili pro Azure.

## <a name="configure-and-deploy-the-web-app"></a>Konfigurace a nasazení webové aplikace

Nakonfigurujete kód aplikace pro ohlášení telemetrie do správné instance Application Insights a nakonfigurujete webové aplikace pomocí správných připojovacích řetězců. Další informace o Application Insights najdete v tématu [co je Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Přidat Application Insights

1. Otevřete webovou aplikaci v Microsoft Visual Studio.

2. [Přidejte Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-client-side-telemetry-for-web-applications) do projektu pro přenos telemetrie, kterou Application Insights používá k vytvoření výstrah při zvyšování nebo snižování webového provozu.

### <a name="configure-dynamic-connection-strings"></a>Konfigurace dynamických připojovacích řetězců

Každá instance webové aplikace bude používat pro připojení k databázi SQL jinou metodu. Aplikace v Azure používá privátní IP adresu SQL Server virtuálního počítače a aplikace v centru Azure Stack používá veřejnou IP adresu SQL Server virtuálního počítače.

> [!Note]  
> V integrovaném systému Azure Stack hub by veřejná IP adresa neměla být směrovatelný z Internetu. V Azure Stack hub Development Kit (ASDK) není veřejná IP adresa směrovatelný, a to mimo ASDK.

Proměnné prostředí App Service můžete použít k předání jiného připojovacího řetězce do každé instance aplikace.

1. Otevřete aplikaci v aplikaci Visual Studio.

2. Otevřete Startup.cs a vyhledejte následující blok kódu:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Nahraďte předchozí blok kódu následujícím kódem, který používá připojovací řetězec definovaný v souboru *appSettings. JSON* :

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-app-settings"></a>Konfigurovat nastavení aplikace App Service

1. Vytvořte připojovací řetězce pro Azure a centrum Azure Stack. Řetězce by měly být stejné, s výjimkou používaných IP adres.

2. V Azure a centra Azure Stack přidejte příslušný připojovací řetězec [jako nastavení aplikace](https://docs.microsoft.com/azure/app-service/web-sites-configure) ve webové aplikaci, a to pomocí `SQLCONNSTR\_` jako předponu v názvu.

3. **Uložte** nastavení webové aplikace a restartujte aplikaci.

## <a name="enable-automatic-scaling-in-global-azure"></a>Povolit automatické škálování v globálním Azure

Při vytváření webové aplikace v prostředí App Service se spustí s jednou instancí. Automatické horizontální navýšení kapacity můžete provést tak, že přidáte instance pro zajištění dalších výpočetních prostředků pro vaši aplikaci. Podobně můžete automaticky škálovat a snížit počet instancí, které vaše aplikace potřebuje.

> [!Note]  
> Abyste mohli nakonfigurovat horizontální navýšení kapacity a škálování, musíte mít App Service plán. Pokud nemáte plán, vytvořte ho před spuštěním dalších kroků.

### <a name="enable-automatic-scale-out"></a>Povolit automatické horizontální navýšení kapacity

1. V Azure Najděte App Service plán pro lokality, pro které chcete škálovat kapacitu, a pak vyberte škálování na více instancí **(App Service plán)** .

    ![Horizontální navýšení kapacity](media/solution-deployment-guide-hybrid/image16.png)

2. Vyberte **Povolit automatické škálování**.

    ![Povolit automatické škálování](media/solution-deployment-guide-hybrid/image17.png)

3. Zadejte název pro **název nastavení automatického škálování**. Pro **výchozí** pravidlo automatického škálování vyberte škálovat na **základě metriky**. Nastavte **limity instancí** na **minimum: 1**, **maximum: 10**a **Výchozí hodnota: 1**.

    ![Konfigurace automatického škálování](media/solution-deployment-guide-hybrid/image18.png)

4. Vyberte **+ Přidat pravidlo**.

5. Ve **zdroji metriky**vyberte **aktuální prostředek**. Pro pravidlo použijte následující kritéria a akce.

**Kritéria**

1. V části **Časová agregace** vyberte **průměr**.

2. V části **název metriky**vyberte **Procento procesoru**.

3. V části **operátor**vyberte **větší než**.

   - Nastavte **prahovou hodnotu** na **50**.
   - Nastavte **dobu trvání** na **10**.

**Akce**

1. V části **operace**vyberte **zvýšit počet o**.

2. Nastavte **počet instancí** na **2**.

3. Nastavte **vychladnutí dolů** na **5**.

4. Vyberte **Přidat**.

5. Vyberte **+ Přidat pravidlo**.

6. Ve **zdroji metriky**vyberte **aktuální prostředek.**

   > [!Note]  
   > Aktuální prostředek bude obsahovat název nebo identifikátor GUID vašeho plánu App Service a rozevírací seznamy pro **typ prostředku** a **prostředek** nebudou k dispozici.

### <a name="enable-automatic-scale-in"></a>Povolit automatické škálování v

Při snížení provozu může webová aplikace Azure automaticky snížit počet aktivních instancí, aby se snížily náklady. Tato akce je méně agresivní než horizontální navýšení kapacity a minimalizuje dopad na uživatele aplikace.

1. Přejděte do **výchozí** podmínky horizontálního navýšení kapacity a vyberte **+ Přidat pravidlo**. Pro pravidlo použijte následující kritéria a akce.

**Kritéria**

1. V části **Časová agregace** vyberte **průměr**.

2. V části **název metriky**vyberte **Procento procesoru**.

3. V části **operátor**vyberte **menší než**.

   - Nastavte **prahovou hodnotu** na **30**.
   - Nastavte **dobu trvání** na **10**.

**Akce**

1. V části **operace**vyberte **snížit počet o**.

   - Nastavte **počet instancí** na **1**.
   - Nastavte **vychladnutí dolů** na **5**.

2. Vyberte **Přidat**.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Vytvoření profilu Traffic Manager a konfigurace škálování mezi cloudy

Vytvořte v Azure profil Traffic Manager a pak nakonfigurujte koncové body, aby se povolilo škálování mezi cloudy.

### <a name="create-traffic-manager-profile"></a>Vytvořit profil Traffic Manageru

1. Vyberte **vytvořit prostředek**.
2. Vyberte **Sítě**.
3. Vyberte **profil Traffic Manager** a nakonfigurujte následující nastavení:

   - Do **název**zadejte název profilu. Tento název **musí** být v zóně trafficmanager.NET jedinečný a používá se k vytvoření nového názvu DNS (například northwindstore.trafficmanager.NET).
   - U **metody směrování**vyberte **Vážená**.
   - V části **předplatné**vyberte předplatné, ve kterém chcete vytvořit tento profil.
   - V rámci **skupiny prostředků**vytvořte novou skupinu prostředků pro tento profil.
   - V poli **Umístění skupiny prostředků** vyberte umístění skupiny prostředků. Toto nastavení odkazuje na umístění skupiny prostředků a nemá žádný vliv na profil Traffic Manager, který je nasazen globálně.

4. Vyberte **Vytvořit**.

    ![Vytvořit profil Traffic Manageru](media/solution-deployment-guide-hybrid/image19.png)

   Po dokončení globálního nasazení profilu Traffic Manager se zobrazí v seznamu prostředků pro skupinu prostředků, ve které jste ji vytvořili.

### <a name="add-traffic-manager-endpoints"></a>Přidání koncových bodů služby Traffic Manager

1. Vyhledejte profil Traffic Manager, který jste vytvořili. Pokud jste přešli na skupinu prostředků pro daný profil, vyberte profil.

2. V části **profil Traffic Manager**v části **Nastavení**vyberte **koncové body**.

3. Vyberte **Přidat**.

4. V části **přidat koncový bod**použijte pro Azure Stack centrum následující nastavení:

   - Jako **typ**vyberte **externí koncový bod**.
   - Zadejte **název** koncového bodu.
   - Pro **plně kvalifikovaný název domény (FQDN) nebo IP**adresu zadejte externí adresu URL webové aplikace centra Azure Stack.
   - Pro **váhu**ponechte výchozí hodnotu **1**. Tato váha má za následek veškerý provoz směřující do tohoto koncového bodu, pokud je v pořádku.
   - Nechejte položku **Přidat jako zakázanou** nezaškrtnutou.

5. Výběrem **OK** uložte koncový bod centra Azure Stack.

Potom nakonfigurujete koncový bod Azure.

1. V **Traffic Manager profil**vyberte **koncové body**.
2. Vyberte **+ Přidat**.
3. Na stránce **přidat koncový bod**použijte následující nastavení pro Azure:

   - Jako **typ**vyberte **koncový bod Azure**.
   - Zadejte **název** koncového bodu.
   - Jako **typ cílového prostředku**vyberte **App Service**.
   - V části **cílový prostředek**vyberte **možnost zvolit službu App Service** , ve které se zobrazí seznam Web Apps ve stejném předplatném.
   - V části **Prostředek** vyberte službu App Service, kterou chcete přidat jako první koncový bod.
   - V případě **váhy**vyberte **2**. Toto nastavení způsobí, že veškerý provoz směřující do tohoto koncového bodu je v případě, že primární koncový bod není v pořádku, nebo pokud máte pravidlo nebo výstrahu, která přesměruje provoz, když se aktivuje.
   - Nechejte položku **Přidat jako zakázanou** nezaškrtnutou.

4. Výběrem **OK** uložte koncový bod Azure.

Po nakonfigurování obou koncových bodů jsou uvedeny v **Traffic Manager profilu** při výběru **koncových bodů**. Příklad na následujícím snímku obrazovky ukazuje dva koncové body s informacemi o stavu a konfiguraci pro každé z nich.

![Koncové body](media/solution-deployment-guide-hybrid/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Nastavení Application Insights monitorování a upozorňování

Azure Application Insights umožňuje monitorovat aplikaci a odesílat výstrahy na základě podmínek, které nakonfigurujete. Mezi příklady patří: aplikace není k dispozici, dochází k chybám nebo zobrazuje problémy s výkonem.

K vytváření výstrah použijete Application Insights metriky. Při aktivaci těchto výstrah se instance webové aplikace automaticky přepne z centra Azure Stack do Azure pro horizontální navýšení kapacity a potom zpátky na Azure Stack centra pro horizontální navýšení kapacity.

### <a name="create-an-alert-from-metrics"></a>Vytvoření výstrahy z metriky

Přejděte do skupiny prostředků tohoto kurzu a pak vyberte instanci Application Insights pro otevření **Application Insights**.

![Application Insights](media/solution-deployment-guide-hybrid/image21.png)

Toto zobrazení použijete k vytvoření upozornění na horizontální navýšení kapacity a měřítka v upozornění.

### <a name="create-the-scale-out-alert"></a>Vytvoření upozornění na horizontální navýšení kapacity

1. V části **Konfigurovat**vyberte **výstrahy (klasické)** .
2. Vyberte **Přidat upozornění na metriku (Classic)** .
3. V části **Přidat pravidlo**nakonfigurujte následující nastavení:

   - Jako **název**zadejte **nárůst do cloudu Azure**.
   - **Popis** je volitelný.
   - V části **zdrojová** > **Upozornění na**vyberte **metriky**.
   - V části **kritéria**vyberte své předplatné, skupinu prostředků pro profil Traffic Manager a název profilu Traffic Manager pro daný prostředek.

4. Jako **metrika**vyberte **rychlost požadavků**.
5. V případě **podmínky**vyberte **větší než**.
6. V případě **prahové hodnoty**zadejte **2**.
7. Jako **periodu**vyberte **za posledních 5 minut**.
8. V části **oznamovat prostřednictvím**:
   - Zaškrtněte políčko pro **vlastníky e-mailů, přispěvatele a čtenáře**.
   - Zadejte svou e- **mailovou adresu pro další e-maily správce**.

9. Na panelu nabídek vyberte **Uložit**.

### <a name="create-the-scale-in-alert"></a>Vytvoření měřítka v upozornění

1. V části **Konfigurovat**vyberte **výstrahy (klasické)** .
2. Vyberte **Přidat upozornění na metriku (Classic)** .
3. V části **Přidat pravidlo**nakonfigurujte následující nastavení:

   - Jako **název**zadejte horizontální navýšení **kapacity zpátky do centra Azure Stack**.
   - **Popis** je volitelný.
   - V části **zdrojová** > **Upozornění na**vyberte **metriky**.
   - V části **kritéria**vyberte své předplatné, skupinu prostředků pro profil Traffic Manager a název profilu Traffic Manager pro daný prostředek.

4. Jako **metrika**vyberte **rychlost požadavků**.
5. V případě **podmínky**vyberte **méně než**.
6. V případě **prahové hodnoty**zadejte **2**.
7. Jako **periodu**vyberte **za posledních 5 minut**.
8. V části **oznamovat prostřednictvím**:
   - Zaškrtněte políčko pro **vlastníky e-mailů, přispěvatele a čtenáře**.
   - Zadejte svou e- **mailovou adresu pro další e-maily správce**.

9. Na panelu nabídek vyberte **Uložit**.

Následující snímek obrazovky znázorňuje výstrahy pro horizontální navýšení kapacity a škálování.

   ![Upozornění (Classic)](media/solution-deployment-guide-hybrid/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack-hub"></a>Přesměrování provozu mezi Azure a centra Azure Stack

Můžete nakonfigurovat ruční nebo automatické přepínání provozu webové aplikace mezi Azure a Azure Stack hub.

### <a name="configure-manual-switching-between-azure-and-azure-stack-hub"></a>Konfigurace ručního přepínání mezi Azure a centra Azure Stack

Když váš web dosáhne prahových hodnot, které nakonfigurujete, zobrazí se upozornění. K ručnímu přesměrování provozu do Azure použijte následující postup.

1. V Azure Portal vyberte svůj profil Traffic Manager.

    ![Koncové body Traffic Manageru](media/solution-deployment-guide-hybrid/image20.png)

2. Vyberte **koncové body**.
3. Vyberte **koncový bod Azure**.
4. V části **stav**vyberte **povoleno**a pak vyberte **Uložit**.

    ![Povolit koncový bod Azure](media/solution-deployment-guide-hybrid/image23.png)

5. V části **koncové body** profilu Traffic Manager vyberte **externí koncový bod**.
6. V části **stav**vyberte **zakázáno**a pak vyberte **Uložit**.

    ![Zakázat koncový bod centra Azure Stack](media/solution-deployment-guide-hybrid/image24.png)

Po nakonfigurování koncových bodů přejde provoz aplikace do webové aplikace se škálováním na více instancí místo webové aplikace centra Azure Stack.

 ![Změněné koncové body](media/solution-deployment-guide-hybrid/image25.png)

Pokud chcete tok vrátit zpátky do centra Azure Stack, použijte k těmto akcím předchozí kroky:

- Povolte koncový bod centra Azure Stack.
- Zakažte koncový bod Azure.

### <a name="configure-automatic-switching-between-azure-and-azure-stack-hub"></a>Konfigurace automatického přepínání mezi Azure a centra Azure Stack

Application Insights monitorování můžete použít také v případě, že aplikace běží v prostředí bez [serveru](https://azure.microsoft.com/overview/serverless-computing/) , které poskytuje Azure Functions.

V tomto scénáři můžete nakonfigurovat Application Insights pro použití Webhooku, který volá aplikaci Function App. Tato aplikace automaticky povolí nebo zakáže koncový bod v reakci na výstrahu.

Pomocí následujících kroků můžete nakonfigurovat automatické přepínání provozu.

1. Vytvoření aplikace Azure Function App
2. Vytvoření funkce aktivované protokolem HTTP
3. Importujte sady Azure SDK pro Správce prostředků, Web Apps a Traffic Manager.
4. Vývoj kódu pro:

   - Ověřte si předplatné Azure.
   - Použijte parametr, který přepíná Traffic Manager koncové body pro směrování provozu do Azure nebo centra Azure Stack.

5. Uložte svůj kód a přidejte adresu URL aplikace Function App s příslušnými parametry do oddílu **Webhooku** nastavení pravidla výstrahy Application Insights.
6. Provoz se automaticky přesměruje, když se aktivuje výstraha Application Insights.

## <a name="next-steps"></a>Další kroky

- Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
