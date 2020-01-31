---
title: Nejčastější dotazy týkající se využití rozhraní API
description: Seznam měřičů Azure Stack centra, porovnání s rozhraním API využití Azure, čas využití a hlášený čas, kódy chyb.
author: sethmanheim
ms.topic: article
ms.date: 01/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: 18539c666683c6e35c8736616405f961b63adf40
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880648"
---
# <a name="frequently-asked-questions-about-azure-stack-hub-usage"></a>Nejčastější dotazy týkající se využití centra Azure Stack

Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se Azure Stack využití centra a rozhraní API pro využití centra Azure Stack.

## <a name="what-meter-ids-can-i-see"></a>Jaká ID měřičů můžu zobrazit?

Použití je hlášeno pro následující poskytovatele prostředků:

### <a name="network"></a>Network (Síť)
  
**ID měřiče**: F271A8A388C44D93956A063E1D2FA80B  
**Název měřiče**: využití statických IP adres  
**Jednotka**: IP adresy  
**Poznámky**: počet využitých IP adres. Pokud voláte rozhraní API s využitím každodenní členitosti, měřič vrátí IP adresu vynásobenou počtem hodin.  
  
**ID měřiče**: 9E2739BA86744796B465F64674B822BA  
**Název měřiče**: využití dynamické IP adresy  
**Jednotka**: IP adresy  
**Poznámky**: počet využitých IP adres. Pokud voláte rozhraní API s využitím každodenní členitosti, měřič vrátí IP adresu vynásobenou počtem hodin.  
  
### <a name="storage"></a>Storage
  
**ID měřiče**: B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**Název měřiče**: TableCapacity  
**Jednotka**: GB\*hodin  
**Poznámky**: Celková kapacita spotřebovaná tabulkami.  
  
**ID měřiče**: B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**Název měřiče**: PageBlobCapacity  
**Jednotka**: GB\*hodin  
**Poznámky**: Celková kapacita využitá objekty blob stránky.  
  
**ID měřiče**: B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**Název měřiče**: QueueCapacity  
**Jednotka**: GB\*hodin  
**Poznámky**: Celková kapacita využitá frontou.  
  
**ID měřiče**: 09F8879E-87E9-4305-A572-4B7BE209F857  
**Název měřiče**: BlockBlobCapacity  
**Jednotka**: GB\*hodin  
**Poznámky**: Celková kapacita využitá objekty blob bloku.  
  
**ID měřiče**: B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**Název měřiče**: TableTransactions  
**Jednotka**: počet požadavků v 10000  
**Poznámky**: požadavky na Table Service (v 10, tisících).  
  
**ID měřiče**: 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**Název měřiče**: TableDataTransIn  
**Jednotka**: příchozí data v GB  
**Poznámky**: Table Service příchozí příchozí přenosy dat v GB.  
  
**ID měřiče**: 1B8C1DEC-EE42-414B-AA36-6229CF199370  
**Název měřiče**: TableDataTransOut  
**Jednotka**: odchozí data v GB  
**Poznámky**: Table Service výstup dat v GB.
  
**ID měřiče**: 43DAF82B-4618-444A-B994-40C23F7CD438  
**Název měřiče**: BlobTransactions  
**Jednotka**: počet požadavků za 10 000  
**Poznámky**: požadavky na BLOB Service (v 10, tisících).  
  
**ID měřiče**: 9764F92C-E44A-498E-8DC1-AAD66587A810  
**Název měřiče**: BlobDataTransIn  
**Jednotka**: příchozí data v GB  
**Poznámky**: BLOB Service příchozí příchozí přenosy dat v GB.  
  
**ID měřiče**: 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**Název měřiče**: BlobDataTransOut  
**Jednotka**: odchozí data v GB  
**Poznámky**: BLOB Service výstup dat v GB.  
  
**ID měřiče**: EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**Název měřiče**: QueueTransactions  
**Jednotka**: počet požadavků za 10 000  
**Poznámky**: požadavky na služba front (v 10, tisících).  
  
**ID měřiče**: E518E809-E369-4A45-9274-2017B29FFF25  
**Název měřiče**: QueueDataTransIn  
**Jednotka**: příchozí data v GB  
**Poznámky**: Služba front příchozí příchozí přenosy dat v GB.  
  
**ID měřiče**: DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**Název měřiče**: QueueDataTransOut  
**Jednotka**: odchozí data v GB  
**Poznámky**: Služba front odchozího přenosu dat v GB  

### <a name="compute"></a>Služby Compute
  
**ID měřiče**: FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**Název měřiče**: počet hodin základního virtuálního počítače  
**Jednotka**: počet hodin virtuálních jader  
**Poznámky**: počet virtuálních jader vynásobený počtem hodin spuštění virtuálního počítače.  
  
**ID měřiče**: 9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**Název měřiče**: hodiny velikosti virtuálního počítače s Windows  
**Jednotka**: počet hodin virtuálních jader  
**Poznámky**: počet virtuálních jader vynásobený počtem hodin spuštění virtuálního počítače.  
  
**ID měřiče**: 6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**Název měřiče**: hodiny velikosti virtuálního počítače  
**Jednotka**: hodiny VM  
**Poznámky**: zachycuje základní a virtuální počítač s Windows. Neupravuje pro jádra.  
  
### <a name="managed-disks"></a>Spravované disky

**ID měřiče**: 380874f9-300c-48e0-95A0-d2d9a21ade8f   
**Název měřiče**: S4   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Standard – 32 GB 

**ID měřiče**: 1b77d90f-427b-4435-b4f1-d78adec53222   
**Název měřiče**: S6   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Standard – 64 GB 

**Meter ID**: d5f7731b-f639-404a-89d0-e46186e22c8d   
**Název měřiče**: S10   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Standard – 128 GB 

**ID měřiče**: ff85ef31-da5b-4eac-95dd-a69d6f97b18a   
**Název měřiče**: S15   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Standard – 256 GB 

**ID měřiče**: 88ea9228-457a-4091-adc9-ad5194f30b6e   
**Název měřiče**: S20   
**Jednotka**: počet disků\*měsíc      
**Poznámky**: spravovaný disk úrovně Standard – 512 GB 

**ID měřiče**: 5b1db88a-8596-4002-8052-347947c26940   
**Název měřiče**: S30   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Standard – 1024 GB 

**ID měřiče**: 7660b45b-b29d-49cb-B816-59f30fbab011   
**Název měřiče**: P4   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Premium – 32 GB 

**ID měřiče**: 817007fd-a077-477f-BC01-b876f27205fd   
**Název měřiče**: P6   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Premium – 64 GB 

**ID měřiče**: e554b6bc-96cd-4938-a5b5-0da990278519   
**Název měřiče**: P10   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Premium – 128 GB  

**ID měřiče**: cdc0f53a-62a9-4472-a06c-e99a23b02907   
**Název měřiče**: P15  
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Premium – 256 GB 

**ID měřiče**: b9cb2d1a-84c2-4275-aa8b-70d2145d59aa   
**Název měřiče**: P20   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Premium – 512 GB 

**ID měřiče**: 06bde724-9f94-43c0-84c3-d0fc54538369   
**Název měřiče**: P30   
**Jednotka**: počet disků\*měsíc   
**Poznámky**: spravovaný disk úrovně Premium – 1024 GB 

**ID měřiče**: 7ba084ec-ef9c-4d64-a179-7732c6cb5e28   
**Název měřiče**: ActualStandardDiskSize   
**Jednotka**: GB\*měsíc      
**Poznámky**: skutečná velikost na disku standardního spravovaného disku  

**ID měřiče**: daef389a-06e5-4684-a7f7-8813d9f792d5  
**Název měřiče**: ActualPremiumDiskSize   
**Jednotka**: GB\*měsíc      
**Poznámky**: skutečná velikost disku spravovaného disku úrovně Premium 

**ID měřiče**: 108fa95b-be0d-4cd9-96e8-5b0d59505df1  
**Název měřiče**: ActualStandardSnapshotSize   
**Jednotka**: GB\*měsíc   
**Poznámky**: skutečná velikost disku spravovaného standardního snímku.  

**ID měřiče**: 578ae51d-4ef9-42f9-85ae-42b52d3d83ac   
**Název měřiče**: ActualPremiumSnapshotSize   
**Jednotka**: GB\*měsíc   
**Poznámky**: skutečná velikost disku spravovaného snímku Premium.   

**ID měřiče**: 5d76e09f-4567-452A-94cc-7d1f097761f0   
**Název měřiče**: S4   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Standard – 32 GB (zastaralé) 

**ID měřiče**: dc9fc6a9-0782-432a-b8dc-978130457494   
**Název měřiče**: S6   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Standard – 64 GB (zastaralé) 

**ID měřiče**: e5572fce-9f58-49D7-840c-b168c0f01fff   
**Název měřiče**: S10   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Standard – 128 GB (zastaralé) 

**ID měřiče**: 9a8caedd-1195-4cd5-80b4-a4c22f9302b8   
**Název měřiče**: S15   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Standard – 256 GB (zastaralé) 

**ID měřiče**: 5938f8da-0ecd-4c48-8d5a-c7c6c23546be   
**Název měřiče**: S20   
**Jednotka**: počet disků\*hodin      
**Poznámky**: spravovaný disk úrovně Standard – 512 GB (zastaralé) 

**ID měřiče**: 7705a158-bd8b-4b2b-b4c2-0782343b81e6   
**Název měřiče**: S30   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Standard – 1024 GB (zastaralé) 

**ID měřiče**: 5c105f5f-cbdf-435c-b49b-3c7174856dcc   
**Název měřiče**: P4   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Premium – 32 GB (zastaralé) 

**ID měřiče**: 518b412b-1927-4f25-985f-4aea24e55c4f   
**Název měřiče**: P6   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Premium – 64 GB (zastaralé) 

**ID měřiče**: 5cfb1fed-0902-49e3-8217-9add946fd624   
**Název měřiče**: P10   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Premium – 128 GB (zastaralé)  

**ID měřiče**: 8de91c94-F740-4d9a-b665-bd5974fa08d4   
**Název měřiče**: P15  
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Premium – 256 GB (zastaralé) 

**ID měřiče**: c7e7839c-293b-4761-ae4c-848eda91130b   
**Název měřiče**: P20   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Premium – 512 GB (zastaralé) 

**ID měřiče**: 9f502103-adf4-4488-b494-456c95d23a9f   
**Název měřiče**: P30   
**Jednotka**: počet disků\*hodin   
**Poznámky**: spravovaný disk úrovně Premium – 1024 GB (zastaralé) 

**ID měřiče**: 8a409390-1913-40AE-917b-08d0f16f3c38   
**Název měřiče**: ActualStandardDiskSize   
**Jednotka**: Byte\*hodin      
**Poznámky**: skutečná velikost na disku standardního spravovaného disku (zastaralé)  

**ID měřiče**: 1273b16f-8458-4C34-8ce2-a515de551ef6  
**Název měřiče**: ActualPremiumDiskSize   
**Jednotka**: Byte\*hodin      
**Poznámky**: skutečná velikost disku spravovaného disku úrovně Premium (zastaralé) 

**ID měřiče**: 89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**Název měřiče**: ActualStandardSnapshotSize   
**Jednotka**: Byte\*hodin   
**Poznámky**: skutečná velikost disku spravovaného standardního snímku (zastaralé) 

**ID měřiče**: 95b0c03f-8a82-4524-8961-ccfbf575f536   
**Název měřiče**: ActualPremiumSnapshotSize   
**Jednotka**: Byte\*hodin   
**Poznámky**: skutečná velikost disku spravovaného snímku Premium (zastaralé) 

**ID měřiče**: 75d4b707-1027-4403-9986-6ec7c05579c8 **měřiče**: ActualStandardSnapshotSize **jednotka**: GB\*měsíc **poznámky**: skutečná velikost na disku spravovaného standardního snímku (zastaralé)  

**ID měřiče**: 5ca1cbb9-6f14-4e76-8be8-1ca91547965e **měřiče**: ActualPremiumSnapshotSize **jednotka**: GB\*měsíc **poznámky**: skutečná velikost disku spravovaného snímku Premium (zastaralé)  

### <a name="sql-rp"></a>SQL RP
  
**ID měřiče**: CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**Název měřiče**: DatabaseSizeHourSqlMeter  
**Jednotka**: MB\*hodin  
**Poznámky**: Celková kapacita databáze při vytvoření. Pokud voláte rozhraní API pro použití s každodenní členitosti, měřič vrátí počet MB vynásobený počtem hodin.  
  
### <a name="mysql-rp"></a>MySql RP   
  
**ID měřiče**: E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**Název měřiče**: DatabaseSizeHourMySqlMeter  
**Jednotka**: MB\*hodin  
**Poznámky**: Celková kapacita databáze při vytvoření. Pokud voláte rozhraní API pro použití s každodenní členitosti, měřič vrátí počet MB vynásobený počtem hodin.    
### <a name="key-vault"></a>Key Vault   
  
**ID měřiče**: EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**Název měřiče**: Key Vault transakce  
**Jednotka**: počet požadavků v 10000  
**Poznámky**: počet požadavků REST API přijatých Key Vault rovinou dat.  
  
**ID měřiče**: 2C354225-B2FE-42E5-AD89-14F0EA302C87  
**Název měřiče**: rozšířené transakce klíčů  
**Jednotka**: 10 000 transakcí  
**Poznámky**: RSA 3K/4k, transakce klíče ECC. (Preview).  
  
### <a name="app-service"></a>App Service   
  
**ID měřiče**: 190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**Název měřiče**: App Service  
**Jednotka**: počet hodin virtuálních jader  
**Poznámky**: počet virtuálních jader používaných ke spuštění App Service. Poznámka: Microsoft pomocí tohoto měřiče účtuje App Service v centru Azure Stack. Poskytovatelé Cloud Solution Provider můžou použít jiné měřiče App Service (níže) k výpočtu využití jejich klientů.  
  
**ID měřiče**: 67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**Název měřiče**: žádosti o funkce  
**Jednotka**: 10 požadavků  
**Poznámky**: celkový počet požadovaných provedení (za 10 spuštění). Spouštění jsou počítány pokaždé, když je funkce spuštěna v reakci na událost nebo aktivována vazbou.  
  
**ID měřiče**: D1D04836-075C-4F27-BF65-0A1130EC60ED  
**Název měřiče**: Functions – COMPUTE  
**Jednotka**: GB-s  
**Poznámky**: spotřeba prostředků měřená za gigabajt sekund (GB/s). **Vypozorovaná spotřeba prostředků** se počítá vynásobením průměrné velikosti paměti v GB časem v milisekundách, jakou trvá spuštění funkce. Paměť, kterou funkce využije, se měří zaokrouhlením na nejbližší 128 MB až do maximální velikosti paměti 1 536 MB. doba spuštění se počítá se zaokrouhlením na nejbližší 1 ms. Minimální doba spuštění a paměť pro spuštění jedné funkce je 100 MS a 128 MB v uvedeném pořadí.  
  
**ID měřiče**: 957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**Název měřiče**: sdílené hodiny App Service  
**Jednotka**: 1 hodina  
**Poznámky**: využití plánu horizontálních oddílů App Service za hodinu. Plány se měří na základě jednotlivých aplikací.  
  
**ID měřiče**: 539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**Název měřiče**: Free App Service hodin  
**Jednotka**: 1 hodina  
**Poznámky**: využití bezplatného App Service plánu za hodinu. Plány se měří na základě jednotlivých aplikací.  
  
**ID měřiče**: 88039D51-A206-3A89-E9DE-C5117E2D10A6  
**Název měřiče**: malé standardní App Service hodiny  
**Jednotka**: 1 hodina  
**Poznámky**: vypočítáno na základě velikosti a počtu instancí.  
  
**ID měřiče**: 83A2A13E-4788-78DD-5D55-2831B68ED825  
**Název měřiče**: střední standardní App Service hodiny  
**Jednotka**: 1 hodina  
**Poznámky**: vypočítáno na základě velikosti a počtu instancí.  
  
**ID měřiče**: 1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**Název měřiče**: velká standardní App Service hodiny  
**Jednotka**: 1 hodina  
**Poznámky**: vypočítáno na základě velikosti a počtu instancí.  
  
### <a name="custom-worker-tiers"></a>Vlastní vrstvy pracovního procesu   
  
**ID měřiče**: *vlastní vrstvy pracovního procesu*  
**Název měřiče**: vlastní vrstvy pracovního procesu  
**Jednotka**: hodiny  
**Poznámky**: deterministické ID měřiče se vytvoří na základě SKU a vlastního názvu vlastní vrstvy pracovního procesu. Toto ID měřiče je jedinečné pro každou vlastní vrstvu pracovního procesu.  
  
**ID měřiče**: 264ACB47-AD38-47F8-Add3-47F01DC4F473  
**Název měřiče**: sni SSL  
**Jednotka**: vazba na sni SSL  
**Poznámky**: App Service podporuje dva typy připojení SSL: připojení SSL indikace názvu serveru (SNI) a IP adresa připojení SSL. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech.  
  
**ID měřiče**: 60B42D72-DC1C-472C-9895-6C516277EDB4  
**Název měřiče**: IP SSL  
**Jednotka**: vazba SSL na základě IP adresy  
**Poznámky**: App Service podporuje dva typy připojení SSL: připojení SSL indikace názvu serveru (SNI) a IP adresa připojení SSL. Připojení SSL typu SNI funguje v moderních prohlížečích, připojení SSL na základě IP adresy lze pak použít ve všech.  
  
**ID měřiče**: 73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**Název měřiče**: webový proces  
**Jednotka**:  
**Poznámky**: vypočítáno na aktivní web za hodinu.  
  
**ID měřiče**: 5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**Název měřiče**: externí šířka pásma pro výstup  
**Jednotka**: GB  
**Poznámky**: celkový počet bajtů příchozích požadavků na odpověď a celkový počet odeslaných bajtů požadavků a celkový počet příchozích odpovědí na požadavek FTP (celkem bajtů požadavků na nasazení webu).  
  
## <a name="how-do-the-azure-stack-hub-usage-apis-compare-to-the-azure-usage-apihttpsdocsmicrosoftcomazurebillingbilling-usage-rate-card-overviewazure-resource-usage-api-preview-currently-in-public-preview"></a>Jak se rozhraní API využití centra Azure Stack porovnávají s [rozhraním API využití Azure](https://docs.microsoft.com/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (aktuálně ve verzi Public Preview)?

* Rozhraní API využití tenanta je konzistentní s rozhraním API Azure s jednou výjimkou: příznak *showDetails* se v tuto chvíli nepodporuje v centru Azure Stack.
* Rozhraní API využití zprostředkovatele se vztahuje pouze na centrum Azure Stack.
* [Rozhraní RateCard API](/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) , které je dostupné v Azure, v současné době není v centru Azure Stack k dispozici.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Jaký je rozdíl mezi časem využití a nahlášeným časem?

Sestavy dat o využití mají dvě hlavní hodnoty času:

* **Nahlášený čas** Čas, kdy se událost využití zadala v systému využití.
* **Čas využití** Čas, kdy byl prostředek centra Azure Stack spotřebovaný.

Může se zobrazit nesoulad v hodnotách času využití a nahlášený čas pro konkrétní událost využití. Zpoždění může trvat až několik hodin v jakémkoli prostředí.

V současné době se můžete dotazovat pouze podle **nahlášeného času**.

## <a name="what-do-these-usage-api-error-codes-mean"></a>Co znamenají tyto chybové kódy rozhraní API pro využití?

| **Stavový kód HTTP** | **Kód chyby:** | **Popis** |
| --- | --- | --- |
| 400/Chybný požadavek |*NoApiVersion* |Chybí parametr dotazu *API-Version* . |
| 400/Chybný požadavek |*InvalidProperty* |Vlastnost chybí nebo má neplatnou hodnotu. Zpráva v kódu chyby v těle odpovědi identifikuje chybějící vlastnost. |
| 400/Chybný požadavek |*RequestEndTimeIsInFuture* |Hodnota pro *ReportedEndTime* je v budoucnosti. Hodnoty v budoucnu nejsou pro tento argument povoleny. |
| 400/Chybný požadavek |*SubscriberIdIsNotDirectTenant* |Volání rozhraní API zprostředkovatele používá ID předplatného, které není platným klientem volajícího. |
| 400/Chybný požadavek |*SubscriptionIdMissingInRequest* |Chybí ID předplatného volajícího. |
| 400/Chybný požadavek |*InvalidAggregationGranularity* |Byla požadována neplatná členitost agregace. Platné hodnoty jsou denně a každou hodinu. |
| 503 |*ServiceUnavailable* |Došlo k pokusu o opakovanou chybu, protože služba je zaneprázdněna nebo je volání omezeno. |

## <a name="what-is-the-policy-for-charging-for-vms"></a>Jaké jsou zásady pro zpoplatnění pro virtuální počítače?

Spuštění a zastavení virtuálních počítačů generuje data o využití. V souladu s Azure je zrušení přidělení nutné k zastavení emisí dat využití. V případě, že portál není k dispozici, ale poskytovatel prostředků služby COMPUTE stále běží, je využití vygenerováno.

## <a name="how-do-i-extract-usage-data-from-the-azure-stack-hub-usage-apis"></a>Návody extrahovat data o využití z rozhraní API využití centra Azure Stack?

Nejjednodušší způsob, jak extrahovat data o využití z místních rozhraní API v Azure Stackovém centru, je použití [skriptu souhrnu využití na GitHubu](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/Usagesummary.ps1). Tento skript vyžaduje počáteční a koncové datum jako vstupní parametry.

Alternativně můžete použít rozhraní REST API, jak je vysvětleno v článcích [rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md) a [rozhraní API využití prostředků tenanta](azure-stack-tenant-resource-usage-api.md) .

## <a name="how-can-i-associate-usage-extracted-from-azure-usage-apis-to-a-specific-azure-stack-hub-user-subscription"></a>Jak můžu přidružit získané informace o využití z rozhraní API využití Azure ke konkrétnímu předplatnému uživatele centra Azure Stack?

Záznamy o využití zahrnují kontejner objektů a dat s názvem **AdditionalInfo**, který obsahuje ID předplatného centra Azure Stack. Toto je uživatelské předplatné, které generuje odpovídající záznam o využití.

## <a name="next-steps"></a>Další kroky

* [Fakturace a vrácení peněz zákazníka v centru Azure Stack](azure-stack-billing-and-chargeback.md)
* [Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)
* [Rozhraní API využití prostředků tenanta](azure-stack-tenant-resource-usage-api.md)
