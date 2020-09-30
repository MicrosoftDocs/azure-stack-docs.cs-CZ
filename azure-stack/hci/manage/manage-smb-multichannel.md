---
title: Správa vícekanálového protokolu SMB
description: V tomto tématu můžete použít vícekanálový protokol SMB ke zvýšení propustnosti a odolnosti proti chybám sítě.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/30/2020
ms.openlocfilehash: b7dc0c8050bc9d813115d2c3ebd7716b87bd89fa
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585896"
---
# <a name="manage-smb-multichannel"></a>Správa vícekanálového protokolu SMB

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019; Windows Server 2016; Windows Server 2012 R2; Windows Server 2012; Windows 10

Vícekanálový protokol SMB je součástí protokolu SMB (Server Message Block) 3,0, který zvyšuje výkon sítě a dostupnost souborových serverů.

Protokol SMB vícekanálový umožňuje souborovým serverům používat víc síťových připojení současně. Usnadňuje agregaci šířky pásma sítě a odolnost sítě, pokud je mezi klientem SMB 3,0 a serverem SMB 3,0 k dispozici více cest. To umožňuje serverovým aplikacím plně využít veškerou dostupnou šířku pásma sítě a díky tomu je pružnější pro chyby sítě.

Vícekanálový protokol SMB nabízí následující možnosti:

- **Vyšší propustnost.** Souborový server může současně přenášet další data pomocí více připojení pro vysokorychlostní síťové adaptéry nebo více síťových adaptérů.

- **Odolnost proti chybám sítě.** Pokud klienti současně používají více síťových připojení, klienti mohou bez přerušení pokračovat navzdory ztrátě připojení k síti.

- **Automatická konfigurace.** Vícekanálový protokol SMB automaticky zjišťuje více dostupných síťových cest a v případě potřeby dynamicky přidává připojení.

## <a name="requirements-for-smb-multichannel"></a>Požadavky na vícekanálový protokol SMB

Vzhledem k tomu, že je ve výchozím nastavení povolená možnost SMB vícekanálový, není nutné instalovat další role, služby rolí ani funkce. Klient SMB automaticky detekuje a používá více síťových připojení, když je identifikována konfigurace. Nicméně vícekanálové rozhraní SMB má následující požadavky:

- Aspoň dva počítače, které používají operační systém Azure Stack HCL, Windows Server nebo Windows 10.

- Aspoň jedna z následujících konfigurací:

    - Víc síťových adaptérů

    - Jeden nebo více síťových adaptérů, které podporují škálování na straně příjmu (RSS)

    - Několik síťových adaptérů, které jsou seskupené (viz [seskupování](#nic-teaming)síťových adaptérů)

    - Jeden nebo více síťových adaptérů, které podporují přímý přístup do paměti vzdáleného počítače (RDMA)

## <a name="configure-smb-multichannel"></a>Konfigurace vícekanálového protokolu SMB

Tato část popisuje některé možnosti konfigurace pro nasazení vícekanálového protokolu SMB pomocí pole síťových adaptérů. Tyto konfigurace jsou jenom vzorové konfigurace. K dispozici je mnoho dalších možných konfigurací, které nejsou součástí tohoto tématu.

### <a name="single-rss-capable-network-adapter"></a>Jeden síťový adaptér podporující technologii RSS

V této typické konfiguraci se klient SMB a server SMB konfigurují pomocí jednoho síťového adaptéru ethernetového Ethernetu (10 GbE). Když je server SMB nasazený bez protokolu SMB vícekanálový a v případě, že je navázána pouze jedna relace SMB, vytvoří protokol SMB jedno připojení TCP/IP. V případě pouze jednoho jádra procesoru Tato konfigurace vede k zahlcení, zejména v případě, že je provedeno mnoho malých vstupně-výstupních operací. Proto je možné, že problém s kritickým výkonem je významný.

Většina současných síťových adaptérů nabízí funkci s názvem škálování na straně příjmu (RSS), která umožňuje automatické rozprostření několika připojení mezi více jádry procesoru. Pokud ale používáte jedno připojení, nemůžete vám RSS pomáhat. Při použití vícekanálového protokolu SMB se síťovým adaptérem podporujícím technologii RSS vytvoří protokol SMB pro příslušnou relaci několik připojení TCP/IP. Tato konfigurace zabrání možnému kritickému bodu v jednom jádru procesoru, pokud je potřeba velký počet malých vstupně-výstupních operací.

### <a name="multiple-network-adapters"></a>Víc síťových adaptérů

V této konfiguraci jsou klient SMB a server SMB nakonfigurováni pomocí více síťových adaptérů s 10 adaptéry. Když je server SMB nasazený bez protokolu SMB vícekanálový a v případě, že je navázána pouze jedna relace SMB, používá protokol SMB jeden z dostupných síťových adaptérů k vytvoření jednoho připojení TCP/IP. V tomto scénáři není možné agregovat šířku pásma více síťových adaptérů; nemůžete třeba dosáhnout 2 gigabitů za sekundu (GB/s) při použití dvou síťových adaptérů s jedním síťovým adaptérem. Pokud je vybraný síťový adaptér odpojený nebo zakázaný, existuje i potenciální chyba.

Když je server SMB nasazený pomocí protokolu SMB vícekanálový, vytvoří protokol SMB několik připojení TCP/IP pro jednu relaci s aspoň jedním nebo více připojeními na rozhraní, pokud síťové adaptéry podporují technologii RSS. Tato konfigurace umožňuje protokolu SMB používat kombinovanou šířku pásma síťového adaptéru, která je k dispozici, a umožňuje klientovi SMB bez přerušení pokračovat, pokud síťový adaptér dojde k chybě.

### <a name="nic-teaming"></a>Seskupování síťových adaptérů

Azure Stack HCI a Windows Server podporují možnost zkombinovat více síťových adaptérů k jednomu síťovému adaptéru pomocí funkce označované jako seskupování síťových adaptérů. I když tým vždy poskytuje odolnost proti chybám, když je protokol SMB nasazený bez vícekanálového protokolu SMB, vytvoří protokol SMB pro každý tým jenom jedno připojení TCP/IP. Tato konfigurace vede k omezením v počtu jader procesoru, které se zajišťují, a maximálnímu využití šířky pásma týmu.

Když je protokol SMB nasazený pomocí protokolu SMB vícekanálový, vytvoří protokol SMB pro jednu relaci několik připojení TCP/IP, aby bylo dosaženo lepšího zůstatku napříč jádry procesoru a lepším využitím dostupné šířky pásma. Seskupování síťových adaptérů i nadále nabízí možnost převzetí služeb při selhání, která funguje rychleji než používání protokolu SMB vícekanálového sami. Doporučujeme také seskupování síťových adaptérů, protože nabízí možnosti převzetí služeb při selhání pro jiné úlohy, které nespoléhají na protokol SMB, protože tyto úlohy nemůžou využívat možnosti převzetí služeb při selhání ve vícekanálovém protokolu SMB.

Pokud používáte vyhrazenou sadu síťových adaptérů pro Prostory úložiště s přímým přístupem provoz, protože se někdy v Azure Stack HCI používá, je seskupení těchto síťových adaptérů striktně volitelné – neposkytuje žádné významné výhody ani nevýhody.

>[!IMPORTANT]
>Pokud v systému Windows Server 2012 R2 a starším máte v úmyslu používat možnosti RDMA pro síťové adaptéry, nepoužívejte seskupování síťových adaptérů. V těchto operačních systémech se tým síťových adaptérů s podporou RDMA vždycky hlásí jako nerdma schopný, protože seskupování zakáže schopnost RDMA síťového adaptéru.

### <a name="single-or-multiple-rdma-capable-network-adapters"></a>Jeden nebo více síťových adaptérů s podporou RDMA

Vícekanálový protokol SMB detekuje možnosti RDMA síťových adaptérů, které umožňují funkci SMB Direct s názvem SMB Direct přes RDMA. Bez vícekanálového protokolu SMB používá protokol SMB běžná připojení TCP/IP se síťovými adaptéry s podporou RDMA, kde všechny síťové adaptéry poskytují zásobník protokolu TCP/IP, který existuje souběžně s novým zásobníkem RDMA.

Když je protokol SMB nasazený pomocí protokolu SMB vícekanálový, zjistí protokol SMB schopnost RDMA síťového adaptéru a pro tuto jednu relaci vytvoří několik připojení RDMA se dvěma připojeními RDMA na jedno rozhraní. Tato konfigurace umožňuje protokolu SMB využít vysokou propustnost, nízkou latenci a nízké využití procesoru nabízené síťovými adaptéry podporujícími RDMA. Nabízí také odolnost proti chybám při použití více rozhraní RDMA.

>[!IMPORTANT]
>Po vytvoření připojení RDMA se připojení TCP/IP pro původní vyjednávání protokolu už nepoužívá. Toto připojení se ale zachovává pro případ, že se nezdařilo jiné připojení RDMA.

### <a name="smb-multichannel-rdma-capable-network-adapters-and-nic-teaming-compatibility"></a>Síťové adaptéry s podporou protokolu RDMA, síťové adaptéry s podporou RDMA a kompatibilita seskupování síťových adaptérů

Následující tabulka shrnuje různé možnosti, které jsou k dispozici při kombinaci funkce SMB vícekanálový, RDMA (SMB Direct) a seskupování síťových adaptérů.

| Konfigurace                                           | Propustnost | Odolnost proti chybám u SMB | Odolnost proti chybám pro non-SMB | Nižší využití procesoru |
|:-------------------------------------------------------:|:----------:|:-----------------------:|:---------------------------:|:---------------------:|
| Jeden síťový adaptér (bez kanálu RSS)                         | *          |                         |                             |                       |
| Více síťových adaptérů (žádné RSS)                      | **         | *                       |                             |                       |
| Více síťových adaptérů (žádné RSS) se seskupováním síťových adaptérů     | **         | **                      | *                           |                       |
| Jeden síťový adaptér s RSS                         | *          |                         |                             |                       |
| Více síťových adaptérů s RSS                      | **         | *                       |                             |                       |
| Více síťových adaptérů se seskupováním kanálů RSS a síťových adaptérů      | **         | **                      | *                           |                       |
| Jeden síťový adaptér s podporou RDMA                     | *          |                         |                             | *                     |
| Více síťových adaptérů s podporou RDMA                  | ***        | *                       |                             | *                     |
| Víc síťových adaptérů s podporou RDMA se seskupováním síťových adaptérů | ***        | **                      | *                           | *                     |

Pokud používáte Windows Server 2016 nebo novější, ideálním řešením je použití více síťových adaptérů s podporou RDMA a kombinování seskupování síťových adaptérů s protokolem SMB vícekanálový. Tato kombinace nabízí nejlepší propustnost, poskytuje odolnost proti chybám pro aplikace, které používají protokol SMB a další protokoly, a má nejnižší dopad na procesor.

Jak je uvedeno výše, při použití síťových adaptérů s podporou RDMA na Windows Serveru 2012 R2 nebo starších verzí není seskupování síťových adaptérů dobrou volbou, protože zakáže schopnost RDMA síťového adaptéru.

### <a name="example-configurations-without-smb-multichannel"></a>Příklady konfigurací bez protokolu SMB vícekanálový

Pokud máte v úmyslu použít jeden síťový adaptér bez kanálu RSS, nebudete využívat více síťových připojení, a proto se nepoužije vícekanálový protokol SMB. Pokud plánujete používat i síťové adaptéry s různou rychlostí, vybere funkce SMB vícekanálový automaticky tento nejrychlejší síťový adaptér. Důvodem je to, že síťové adaptéry, které jsou stejného typu (například RDMA, RSS nebo ne), mají stejnou rychlost současně používá protokol SMB vícekanálový. Pomalejší síťové adaptéry jsou nečinné.

## <a name="disable-smb-multichannel"></a>Zakázat vícekanálový protokol SMB
Obvykle nemusíte zakazovat vícekanálový protokol SMB. Pokud ale chcete zakázat vícekanálový protokol SMB, například v testovacím prostředí, použijte následující procedury Windows PowerShellu.

Nejdřív se připojte k jednomu ze serverů tak, že otevřete relaci PowerShellu:

```PowerShell
Enter-PSSession <server-name>
```

Chcete-li zakázat vícekanálový protokol SMB na straně serveru, použijte následující rutinu:

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $false
```

Chcete-li zakázat funkci SMB vícekanálový na straně klienta, použijte následující rutinu:

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $false
```

>[!NOTE]
>Zakázání funkce SMB vícekanálový na klientovi nebo na serveru zablokuje jejich použití v obou systémech.

## <a name="re-enable-smb-multichannel"></a>Znovu povolit vícekanálový protokol SMB

Pokud jste zakázali režim Vícekanálový s protokolem SMB a chcete ho znovu povolit, použijte následující postupy.

Pokud chcete znovu povolit vícekanálový protokol SMB na straně serveru, použijte tuto rutinu:

```PowerShell
Set-SmbServerConfiguration -EnableMultiChannel $true
```

Chcete-li znovu povolit vícekanálový protokol SMB na straně klienta, použijte následující rutinu:

```PowerShell
Set-SmbClientConfiguration -EnableMultiChannel $true
```

>[!NOTE]
>Pokud chcete znovu začít používat, musíte znovu povolit vícekanálový protokol SMB na klientovi i na serveru.

## <a name="test-smb-multichannel"></a>Test pro vícekanálový protokol SMB

Tato část popisuje některé scénáře testování vícekanálového protokolu SMB, které zahrnují porovnání kopírování souborů s a pak bez protokolu SMB vícekanálový a úmyslně způsobuje selhání síťového adaptéru během operace kopírování souborů.

### <a name="compare-a-file-copy-with-and-without-smb-multichannel"></a>Porovnání kopírování souborů s využitím protokolu SMB vícekanálový a bez něj

Pro měření zvýšené propustnosti, kterou poskytuje protokol SMB vícekanálový, proveďte následující testy. Před každým testem restartujte server i klienta, aby se zajistilo, že budou fungovat v podobných podmínkách.

1. Nakonfigurujte vícekanálový protokol SMB pomocí jedné z předchozích popsaných konfigurací.

1. Změřte dobu potřebnou k provedení dlouhotrvající operace kopírování souborů pomocí protokolu SMB vícekanálový.

1. Zakáže protokol SMB vícekanálový. Pokyny najdete v tématu [zakázání protokolu SMB vícekanálový](#disable-smb-multichannel).

1. Změřte dobu potřebnou k provedení stejné kopie souboru bez použití protokolu SMB vícekanálový.

1. Znovu povolte vícekanálový protokol SMB. Pokyny najdete v tématu [opětovné povolení vícekanálového protokolu SMB](#re-enable-smb-multichannel).

1. Porovnejte oba výsledky.

>[!IMPORTANT]
>Abyste se vyhnuli vlivu na výkon při ukládání do mezipaměti, nejdřív zkopírujte velké množství dat, které překračuje velikost dostupné paměti. Operaci kopírování pak znovu spusťte s první operací kopírování, která slouží jako zahřívání. Pouze čas druhé operace kopírování.

### <a name="cause-one-of-the-network-adapters-to-fail-during-a-file-copy-with-smb-multichannel"></a>Způsobí, že jeden ze síťových adaptérů selže při kopírování souborů pomocí protokolu SMB vícekanálový.

Ověření schopnosti převzetí služeb při selhání ve vícekanálovém protokolu SMB:

1. Ujistěte se, že vícekanálový protokol SMB funguje v konfiguraci více síťových adaptérů.

1. Proveďte dlouhotrvající operaci kopírování souborů.

1. I když je operace kopírování souborů spuštěná, simulujte selhání jedné síťové cesty odpojením jednoho z kabelů nebo zakázáním jednoho ze síťových adaptérů.

1. Potvrďte, že kopírování souborů pokračuje v používání zbývajícího síťového adaptéru a funguje bez chyb kopírování souborů.

Ujistěte se, že neexistují žádné jiné úlohy, které používají cestu k odpojenému síťovému připojení. Tato preventivní opatrnost zabraňuje možné chybě v úlohách, které nevyužívají protokol SMB vícekanálový.

## <a name="verify-that-smb-multichannel-works"></a>Ověření, jestli funguje vícekanálový protokol SMB

Pomocí následujícího postupu ověříte, jestli funguje vícekanálový protokol SMB.

1. Pokud chcete ověřit správnost konfigurace síťového adaptéru, zadejte v prostředí Windows PowerShell následující příkaz na server SMB i klienta SMB.

   ```PowerShell
   Get-NetAdapter
   Get-NetAdapterRSS
   Get-NetAdapterRDMA
   Get-NetAdapterHardwareInfo
   ```

2. Pokud chcete ověřit, jestli je povolená funkce SMB vícekanálový, potvrďte, že protokol SMB správně identifikuje síťové adaptéry a že jsou správně identifikované možnosti RSS a RSS síťového adaptéru, udělejte toto:

   V klientovi SMB zadejte v prostředí Windows PowerShell následující příkaz:

   ```PowerShell
   Get-SmbClientConfiguration | Select EnableMultichannel
   Get-SmbClientNetworkInterface
   ```

   Na serveru SMB zadejte v prostředí Windows PowerShell následující příkaz:

   ```PowerShell
   Get-SmbServerConfiguration | Select EnableMultichannel
   Get-SmbServerNetworkInterface
   ```

3. V klientovi SMB proveďte dlouhotrvající operaci kopírování souborů, která vytvoří průběžnou relaci se serverem SMB. Když je operace kopírování spuštěná, zadejte ve Windows PowerShellu následující příkaz, který ověří, jestli připojení používá správnou verzi protokolu SMB a jestli funguje s protokolem SMB vícekanálový.

   ```PowerShell
   Get-SmbConnection
   Get-SmbMultichannelConnection
   Get-SmbMultichannelConnection -IncludeNotSelected
   ```

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Přehled sdílení souborů pomocí protokolu SMB 3 v systému Windows Server](/windows-server/storage/file-server/file-server-smb-overview)
- [Zjednodušené sítě s clustery s funkcí SMB Multichannel a několika síťovými kartami](/windows-server/failover-clustering/smb-multichannel)
- [SMB Direct](/windows-server/storage/file-server/smb-direct)
