---
title: Řešení potíží s vytvářením sestav ověření clusteru
description: Řešení potíží s vytvářením sestav ověření clusteru a ověření konfigurace nastavení QoS pro Azure Stack clustery HCI
author: khdownie
ms.topic: troubleshooting
ms.date: 07/21/2020
ms.author: v-kedow
ms.reviewer: JasonGerend
ms.openlocfilehash: c4da92a6d88a3d2046ee6136f2481ac23e5bd476
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86867201"
---
# <a name="troubleshoot-cluster-validation-reporting"></a>Řešení potíží s vytvářením sestav ověření clusteru

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Toto téma vám pomůže při řešení potíží s vytvářením sestav ověřování clusteru pro síť a nastavení QoS (Quality of Service) v rámci serverů v Azure Stack clusteru HCI a ověřte, že jsou definovaná důležitá pravidla. Pro zajištění optimálního připojení a výkonu proces ověření clusteru ověří, jestli je konfigurace technologie QoS přemostění Datacenter (DCB), a jestli je definovaná, obsahuje vhodná pravidla pro třídy přenosů s podporou převzetí služeb při selhání a SMB/SMB Direct.

## <a name="install-data-center-bridging"></a>Instalace přemostění datového centra

Aby bylo možné používat rutiny specifické pro technologii QoS, je nutné nainstalovat přemostění datového centra. Pokud chcete zjistit, jestli je na serveru už nainstalovaná funkce přemostění datového centra, spusťte v PowerShellu tuto rutinu:

```PowerShell
Get-WindowsFeature -Name Data-Center-Bridging -ComputerName Server1
```

Pokud není přemostění Datacenter nainstalované, nainstalujte ho spuštěním následující rutiny na každém serveru v clusteru:

```PowerShell
Install-WindowsFeature –Name Data-Center-Bridging -ComputerName Server1
```

## <a name="run-a-cluster-validation-test"></a>Spustit test ověření clusteru

Buď použijte funkci ověřit v centru pro správu systému Windows, vyberte **nástroje > servery > inventarizace > ověřit cluster**nebo spusťte následující příkaz prostředí PowerShell:

```PowerShell
Test-Cluster –Node Server1, Server2
```

Kromě toho test ověří, že konfigurace QoS DCB je konzistentní a že všechny servery v clusteru mají stejný počet tříd přenosů a pravidel QoS. Ověří také, že všechny servery mají pravidla QoS definovaná pro třídy clusteringu s podporou převzetí služeb při selhání a SMB/SMB Direct.

Můžete zobrazit sestavu ověření v centru pro správu systému Windows nebo získat přístup k souboru protokolu v aktuálním pracovním adresáři. Příklad: C:\Users \<username> \AppData\Local\Temp\

V dolní části sestavy se zobrazí zpráva "ověřit konfiguraci nastavení QoS" a odpovídající sestavu pro každý server v clusteru.

Chcete-li zjistit, které třídy přenosů jsou již nastaveny na serveru, použijte `Get-NetQosTrafficClass` rutinu.

Další informace najdete v tématu [ověření clusteru Azure Stack HCI](../deploy/validate.md).

## <a name="validate-networking-qos-rules"></a>Ověřit pravidla QoS sítě

Ověří konzistenci nastavení stavu řízení toku DCB a priority mezi servery v clusteru.

### <a name="dcb-willing-status"></a>DCB ochotný stav

Síťové adaptéry, které podporují protokol DCBX (Datacenter přemostění schopností protokolu Exchange), můžou na vzdáleném zařízení přijímat konfigurace. Chcete-li povolit tuto funkci, musí být na síťovém adaptéru DCB ochotný bit nastaven na hodnotu true. Pokud je přidaný bit nastaven na hodnotu false, zařízení odmítne všechny pokusy o konfiguraci ze vzdálených zařízení a vyhodnotí pouze místní konfigurace. Pokud používáte RDMA přes sblížené adaptéry sítě Ethernet (RoCE), měl by být na všech serverech nastaven na hodnotu false.

Všechny servery v clusteru Azure Stack HCI by měly mít stejný bit nastavený jako DCB.

Pomocí `Set-NetQosDcbxSetting` rutiny nastavte DCBý bit na hodnotu true nebo false, jak je uvedeno v následujícím příkladu:

```PowerShell
Set-NetQosDcbxSetting –Willing $false
```

### <a name="dcb-flow-control-status"></a>Stav řízení toku DCB

Řízení toku založené na prioritách je důležité, pokud protokol horní vrstvy, třeba Fiber Channel, spoléhá na bezztrátový základní přenos. Řízení toku DCB lze povolit nebo zakázat globálně nebo pro jednotlivé síťové adaptéry. Pokud je povoleno, umožňuje vytváření zásad QoS, které stanovují prioritu určitých aplikačních přenosů.

Aby zásady QoS fungovaly bez problémů během převzetí služeb při selhání, měly by mít všechny servery v clusteru Azure Stack HCI stejné nastavení stavu řízení toku. Pokud používáte adaptéry RoCE, musí být na všech serverech povolená možnost řízení toku priority.

Pomocí `Get-NetQosFlowControl` rutiny Získejte aktuální konfiguraci řízení toku. Ve výchozím nastavení jsou všechny priority zakázané.

Pomocí `Enable-NetQosFlowControl` rutin a `Disable-NetQosFlowControl` s parametrem-priority můžete zapnout nebo vypnout řízení toku priority. Například následující příkaz povolí řízení toku u provozu označeného s prioritou 3:

```PowerShell
Enable-NetQosFlowControl –Priority 3
```

## <a name="validate-storage-qos-rules"></a>Ověřit pravidla QoS úložiště

Ověřte, že všechny uzly mají pravidlo QoS pro clustering s podporou převzetí služeb při selhání a pro SMB nebo SMB Direct. V opačném případě mohou nastat problémy s připojením a problémy s výkonem.

### <a name="qos-rule-for-failover-clustering"></a>Pravidlo QoS pro clustering s podporou převzetí služeb při selhání

Pokud jsou v clusteru definovaná **nějaká** pravidla QoS pro úložiště, měla by se zobrazit pravidla QoS pro clustering s podporou převzetí služeb při selhání nebo můžou nastat problémy s připojením. Pokud chcete přidat nové pravidlo QoS pro clustering s podporou převzetí služeb při selhání, použijte `New-NetQosPolicy` rutinu jako v následujícím příkladu:

```PowerShell
New-NetQosPolicy "Cluster" -IPDstPort 3343 -Priority 6
```

### <a name="qos-rule-for-smb"></a>Pravidlo QoS pro protokol SMB

Pokud jsou u některých nebo všech uzlů definovaná pravidla QOS, ale nemají pravidlo QOS pro protokol SMB, může to způsobit problémy s připojením a výkonem pro protokol SMB. Chcete-li přidat nové pravidlo QoS sítě pro protokol SMB, použijte `New-NetQosPolicy` rutinu jako v následujícím příkladu:

```PowerShell
New-NetQosPolicy -Name "SMB" -SMB -PriorityValue8021Action 3
```

### <a name="qos-rule-for-smb-direct"></a>Pravidlo QoS pro SMB Direct

SMB Direct obchází zásobník sítě a místo toho používá metody RDMA k přenosu dat. Pokud jsou u některých nebo všech uzlů definované pravidla QOS, ale nemají pravidlo QOS pro funkci SMB Direct, může to způsobit problémy s připojením a výkonem pro funkci SMB Direct. Pokud chcete vytvořit novou zásadu QoS pro funkci SMB Direct, vydejte následující příkazy:

```PowerShell
New-NetQosPolicy "SMB Direct" –NetDirectPort 445 –Priority 3
```

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Přemostění datacenter](/windows-server/networking/technologies/dcb/dcb-top)
- [Správa přemostění datového centra](/windows-server/networking/technologies/dcb/dcb-manage)
- [Běžné konfigurace služby QoS](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj735302(v=ws.11))