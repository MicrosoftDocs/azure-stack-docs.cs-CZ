---
title: Spuštění N-vrstvé aplikace v několika oblastech centra Azure Stack pro zajištění vysoké dostupnosti
description: Naučte se spouštět N-vrstvou aplikaci v několika oblastech centra Azure Stack pro zajištění vysoké dostupnosti.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: df65f32abdc7c643c953f176ae8a4fd0b47309f5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873737"
---
# <a name="run-an-n-tier-application-in-multiple-azure-stack-hub-regions-for-high-availability"></a>Spuštění N-vrstvé aplikace v několika oblastech centra Azure Stack pro zajištění vysoké dostupnosti

Tato referenční architektura ukazuje sadu osvědčených postupů pro spuštění v N-vrstvé aplikaci s více Azure Stackmi oblastmi centra, aby bylo možné dosáhnout dostupnosti a robustní infrastruktury pro zotavení po havárii. V tomto dokumentu se Traffic Manager používá k dosažení vysoké dostupnosti, ale pokud Traffic Manager není upřednostňovanou volbou ve vašem prostředí, můžete v systému nahradit pár vysoce dostupných nástrojů pro vyrovnávání zatížení.

> [!NOTE]  
> Nezapomeňte prosím, že Traffic Manager používané v níže uvedené architektuře je potřeba nakonfigurovat v Azure a koncové body, které se používají ke konfiguraci Traffic Manager profilu, musí být veřejně směrovatelné IP adresy.

## <a name="architecture"></a>Architektura

Tato architektura sestaví na takovém obrázku, který je zobrazený v [N-vrstvé aplikaci s SQL Server](iaas-architecture-windows-sql-n-tier.md).

![Vysoce dostupná síťová architektura pro n-vrstvé aplikace Azure](./media/iaas-architecturesql-n-tier-multi-region/image1.png)

-   **Primární a sekundární oblasti**. Abyste dosáhli vysoké dostupnosti, použijte dvě oblasti. Jedna bude primární oblastí. Druhá oblast bude sloužit k převzetí služeb při selhání.

-   **Traffic Manager Azure**. [Traffic Manager](https://azure.microsoft.com/services/traffic-manager) směruje příchozí žádosti do jedné z oblastí. V normálním provozu směruje žádosti do primární oblasti. Pokud se tato oblast stane nedostupnou, Traffic Manager zajistí převzetí služby při selhání sekundární oblastí. Další informace najdete v části [konfigurace Traffic Manageru](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server#traffic-manager-configuration).

-   **Skupiny prostředků**. Vytvořte samostatné [skupiny prostředků](/azure/azure-resource-manager/resource-group-overview) pro primární oblast, Sekundární oblast. Díky tomu získáte flexibilitu spravovat každou oblast jako jedinou kolekci prostředků. Můžete například znovu nasadit jednu oblast, aniž byste museli zastavovat tu druhou. [Skupiny prostředků propojte](/azure/resource-group-link-resources), abyste mohli spustit dotaz, který vypíše všechny prostředky pro danou aplikaci.

-   **Virtuální sítě**. Vytvořte samostatnou virtuální síť pro každou oblast. Ujistěte se, že se adresní prostory nepřekrývají.

-   **Skupina dostupnosti Always On SQL Server**. Pokud používáte SQL Server, doporučujeme vám pro vysokou dostupnost použít [skupiny dostupnosti AlwaysOn pro SQL Server](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-ver15). Vytvořte jednu skupinu dostupnosti, která obsahuje instance SQL Serveru v obou oblastech.

-   **Připojení VPN k virtuální síti** VNET. Protože VNET Peering ještě není v centru Azure Stack k dispozici, použijte připojení VPN VNET to VNET, aby se tyto dva virtuální sítěy připojily. Další informace najdete [v tématu virtuální síť a virtuální síť v centru Azure Stack](./azure-stack-network-howto-vnet-to-vnet.md?view=azs-1908) .

## <a name="recommendations"></a>Doporučení

Architektura pro více oblastí může poskytnout vyšší dostupnost než nasazení do jedné oblasti. Pokud oblastní výpadek ovlivní primární oblast, můžete použít [Traffic Manager](https://azure.microsoft.com/services/traffic-manager) a převzít služby při selhání sekundární oblastí. Tato architektura může také pomoct, když jednotlivý subsystém aplikace selže.

Existuje několik obecných přístupů k dosažení vysoké dostupnosti napříč oblastmi:

-   **Aktivní/pasivní s aktivním pohotovostním režimem**. provoz směruje do jedné oblasti, zatímco ta druhá čeká v aktivním pohotovostním režimu. Aktivní pohotovostní režim znamená, že virtuální počítače v sekundární oblasti jsou přidělené a běží za všech okolností.

-   **Aktivní/pasivní s studeným pohotovostním režimem**. provoz směruje do jedné oblasti, zatímco ta druhá čeká v pasivním pohotovostním režimu. Pasivní pohotovostní režim znamená, že virtuální počítače v sekundární oblasti nejsou přidělené, dokud není potřeba převzetí služeb při selhání. Tento přístup je méně nákladný, ale obecně bude trvat déle, než v případě selhání přejde do režimu online.

-   **Aktivní/aktivní**. obě oblasti jsou aktivní, žádosti se mezi ně rozdělují a vyrovnává se tak zatížení. Pokud bude jedna oblast nedostupná, vyřadí se z oběhu.

Tato referenční architektura se zaměřuje na aktivní/pasivní vysokou dostupnost s aktivním pohotovostním režimem a pro převzetí služeb při selhání používá Traffic Manager. Můžete nasadit malý počet virtuálních počítačů pro aktivní pohotovostní režim a pak škálovat podle potřeby.

### <a name="traffic-manager-configuration"></a>Konfigurace Traffic Manageru

Při konfiguraci Traffic Manageru zvažte následující body:

-   **Směrování** Traffic Manager podporuje několik [algoritmů směrování](/azure/traffic-manager/traffic-manager-routing-methods). U scénáře popsaném v tomto článku použijte *prioritní* směrování (dříve nazývané směrování s *převzetím služeb při selhání*). S tímto nastavením odešle Traffic Manager všechny žádosti do primární oblasti (pokud se nestane nedostupnou). Od tohoto okamžiku služby při selhání automaticky převezme sekundární oblast. Viz [Konfigurace metody směrování s převzetím služeb při selhání](/azure/traffic-manager/traffic-manager-configure-failover-routing-method).

-   **Sonda stavu**. Traffic Manager používá ke sledování dostupnosti každé oblasti [sondu](/azure/traffic-manager/traffic-manager-monitoring) protokolu HTTP (nebo HTTPS). Tato sonda kontroluje odpověď HTTP 200 pro zadanou cestu adresy URL. Osvědčeným postupem je vytvořit koncový bod, který bude hlásit celkový stav aplikace, a použít tento koncový bod pro sondu stavu. Jinak by sonda mohla ohlásit funkční koncový bod, přestože by důležité části aplikace ve skutečnosti selhávaly. Další informace najdete v tématu [model monitorování stavu koncových bodů](/azure/architecture/patterns/health-endpoint-monitoring).

Když Traffic Manager převezme služby při selhání, nastane časový úsek, ve kterém se klienti nebudou moct k aplikaci připojit. Dobu trvání ovlivňují následující faktory:

-   Sonda stavu musí odhalit, že primární oblast už není dostupná.

-   Servery DNS musí aktualizovat záznamy DNS v mezipaměti pro danou IP adresu, která je závislá na DNS hodnotě TTL (Time to Live). Výchozí hodnotou TTL je 300 sekund (5 minut), ale tuto hodnotu můžete při vytváření profilu Traffic Manageru upravit.

Podrobnosti najdete v tématu [o monitorování Traffic Manageru](/azure/traffic-manager/traffic-manager-monitoring).

Pokud Traffic Manager převezme služby při selhání, raději než provést navrácení služeb po obnovení automaticky, ho doporučujeme udělat ručně. Jinak může nastat situace, kdy aplikace přebíhá mezi různými oblastmi. Před navrácením služeb po obnovení si ověřte, že všechny subsystémy aplikace jsou v pořádku.

Nezapomeňte, že Traffic Manager ve výchozím nastavení provádí navrácení služeb po obnovení automaticky. Pokud tomu chcete zabránit, snižte po převzetí služeb při selhání ručně prioritu primární oblasti. Předpokládejme například, že priorita primární oblasti je 1 a priorita sekundární oblasti je 2. Po převzetí služeb při selhání nastavte prioritu primární oblasti na 3, abyste automatickému navrácení služeb po obnovení předešli. Až budete připraveni na přepínání, aktualizujte prioritu na 1.

Následující příkaz [Azure CLI](/cli/azure/) tuto prioritu aktualizuje:

```cli  
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type externalEndpoints --priority 3
```

Další možností je dočasně zakázat koncový bod, dokud nebudete připravení navrátit služby po obnovení:

```cli
az network traffic-manager endpoint update --resource-group <resource-group> --profile-name <profile>
    --name <endpoint-name> --type externalEndpoints --endpoint-status Disabled
```

V závislosti na příčině převzetí služeb při selhání možná budete muset v rámci oblasti znovu nasadit prostředky. Před navrácením služby po obnovení proveďte test provozní připravenosti. Test by měl ověřit například:

-   správnou konfiguraci virtuálních počítačů (veškerý software je nainstalovaný, služba IIS je spuštěná atd.)

-   stav subsystémů aplikace

-   funkční testování (například že databázová vrstva je dostupná z webové vrstvy)

### <a name="configure-sql-server-always-on-availability-groups"></a>Konfigurace skupin dostupnosti AlwaysOn pro SQL Server

Skupiny dostupnosti AlwaysOn pro SQL Server vyžadují před Windows Serverem 2016 řadič domény a všechny uzly ve skupině dostupnosti musí být ve stejné doméně Active Directory (AD).

Postup konfigurace skupiny dostupnosti:

-   Umístěte do každé oblasti alespoň dva řadiče domény.

-   Přiřaďte každému řadiči domény statickou IP adresu.

-   Vytvořte [síť VPN](./azure-stack-vpn-gateway-about-vpn-gateways.md) pro povolení komunikace mezi dvěma virtuálními sítěmi.

-   Pro každou virtuální síť přidejte IP adresy řadičů domény (z obou oblastí) do seznamu serverů DNS. Můžete k tomu použít následující příkaz rozhraní příkazového řádku. Další informace najdete v tématu [Změna serverů DNS](/azure/virtual-network/manage-virtual-network#change-dns-servers).

    ```cli
    az network vnet update --resource-group <resource-group> --name <vnet-name> --dns-servers "10.0.0.4,10.0.0.6,172.16.0.4,172.16.0.6"
    ```

-   Vytvořte [clustering převzetí služeb při selhání ve Windows Serveru](/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-ver15), který obsahuje instance SQL Serveru v obou oblastech.

-   Vytvořte skupinu dostupnosti AlwaysOn pro SQL Server, která obsahuje instance SQL Serveru jak v primárních, tak i v sekundárních oblastech. Postup najdete v tématu o [rozšíření skupiny dostupnosti AlwaysOn na vzdálené datacentrum Azure (PowerShell)](https://techcommunity.microsoft.com/t5/DataCAT/Extending-AlwaysOn-Availability-Group-to-Remote-Azure-Datacenter/ba-p/305217).

    -   Primární repliku dejte do primární oblasti.

    -   Do primární oblasti dejte také jednu nebo více sekundárních replik. Nakonfigurujte je, aby s automatickým převzetím služeb při selhání používaly synchronní potvrzování.

    -   Jednu nebo více sekundárních replik dejte do sekundární oblasti. Nakonfigurujte je, aby z důvodu zachování výkonu používaly *asynchronní* potvrzování. (Jinak všechny transakce T-SQL musí čekat na dobu odezvy ze sítě do sekundární oblasti.)

> [!NOTE]  
> Repliky asynchronního potvrzování nepodporují automatické převzetí služeb při selhání.

## <a name="availability-considerations"></a>Aspekty dostupnosti

U komplexní n-vrstvé aplikace nemusíte v sekundární oblasti replikovat celou aplikaci. Místo toho můžete replikovat pouze hlavní subsystém, který je potřeba k podpoře kontinuity podnikových procesů.

Traffic Manager je možným bodem selhání v systému. Pokud služba Traffic Manager selže, klienti nebudou mít během výpadku k vaší aplikaci přístup. Zkontrolujte [smlouvu SLA pro Traffic Manager](https://azure.microsoft.com/support/legal/sla/traffic-manager) a rozhodněte se, zda použití samotného Traffic Manageru splňuje vaše podnikové požadavky na vysokou dostupnost. Pokud ne, zvažte přidání jiného řešení správy provozu jako navrácení služeb po obnovení. Pokud služba Azure Traffic Manager selže, změňte záznamy CNAME v DNS tak, aby odkazovaly na jinou službu pro správu provozu. (Tento krok je nutné provést ručně a vaše aplikace nebude dostupná, dokud se změny DNS nerozšíří.)

U clusteru SQL Serveru musíte zvážit dva scénáře převzetí služeb při selhání:

-   Všechny repliky databáze SQL Serveru v primární oblasti selžou. K tomu může dojít například během oblastního výpadku. V takovém případě musíte služby při selhání skupiny dostupnosti převzít ručně, přestože Traffic Manager služby při selhání převezme na front-endu automaticky. Postupujte podle pokynů v tématu o [provedení vynuceného ručního převzetí služeb při selhání skupiny dostupnosti SQL Serveru](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-ver15), které popisuje, jak provést vynucené převzetí služeb při selhání použitím aplikací SQL Server Management Studio, Transact-SQL nebo prostředí PowerShell v SQL Serveru 2016.

    > [!Warning]  
    > Při použití vynuceného převzetí služeb při selhání existuje riziko ztráty dat. Jakmile se primární oblast vrátí do režimu online, pořiďte snímek databáze a použijte nástroj [tablediff](/sql/tools/tablediff-utility?view=sql-server-ver15), abyste našli rozdíly.

-   Traffic Manager převezme služby při selhání sekundární oblastí, ale primární replika databáze SQL Serveru bude stále k dispozici. Front-endová vrstva může například selhat, aniž by ovlivnila virtuální počítače SQL Serveru. V takovém případě je internetový provoz směrován do sekundární oblasti a ta se stále může připojit k primární replice. Zvýší se však latence, protože připojení SQL Serveru cestují napříč oblastmi. V takovém případě byste měli následujícím způsobem provést ruční převzetí služeb při selhání:

    1.  Repliku databáze SQL Serveru v sekundární oblasti dočasně přepněte na *synchronní* potvrzování. Tím se zajistí, že během převzetí služeb při selhání nedojde ke ztrátě dat.

    2.  Převezměte služby při selhání touto replikou.

    3.  Když převezmete služby zpět primární oblastí, obnovte nastavení asynchronního potvrzování.

## <a name="manageability-considerations"></a>Aspekty správy

Při aktualizaci nasazení aktualizujte současně pouze jednu oblast, abyste snížili riziko globálního selhání z nesprávné konfigurace nebo chyby v aplikaci.

Otestujte odolnost systému vůči chybám. Zde jsou některé běžné scénáře selhání, které byste měli testovat:

-   Vypnutí instancí virtuálních počítačů

-   Přetížení prostředků – například procesorů nebo pamětí

-   Odpojení nebo zpoždění sítě

-   Násilné ukončení procesů

-   Vypršení certifikátů

-   Simulace hardwarových chyb

-   Vypnutí služby DNS na řadičích domény

Změřte dobu zotavení a ověřte, zda splňuje vaše obchodní požadavky. Otestujte také kombinace různých typů selhání.

## <a name="next-steps"></a>Další kroky

- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](/azure/architecture/patterns).
