---
title: Otočení Kubernetes certifikátů v centru Azure Stack
description: Naučte se, jak na rozbočovači Azure Stack otočit certifikáty Kubernetes.
author: mattbriggs
ms.topic: how-to
ms.date: 3/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/1/2021
ms.openlocfilehash: fa46de76f59e664fb3dd4a1c771ad7b835f97ca4
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102233840"
---
# <a name="rotate-kubernetes-certificates-on-azure-stack-hub"></a>Otočení Kubernetes certifikátů v centru Azure Stack

Tento dokument poskytuje pokyny k otočení certifikátů v existujícím clusteru AKS Engine a doporučení pro použití přijetí `aks-engine rotate-certs` jako nástroje.

> [!IMPORTANT]  
> Tato funkce je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Požadavky

V tomto průvodci se předpokládá, že jste už nasadili cluster pomocí modulu AKS a že je cluster v dobrém stavu.
## <a name="planning-for-certificate-rotation"></a>Plánování rotace certifikátu

Při zvažování použití této funkce mějte na paměti, že během kroků aktualizace, ověření a restartu nebude k dispozici Řídicí rovina Kubernetes. Naplánujte tuto operaci údržby odpovídajícím způsobem. Také je třeba naplánovat provedení této operace v přípravném prostředí se stejnou konfigurací do provozního prostředí před tím, než se pokusíte v produkčním prostředí.

Před pokusem o provedení této operace si přečtěte následující skutečnosti:

-  Budete potřebovat přístup k modelu rozhraní API ( `apimodel.json` ), který byl vygenerován příkazy `aks-engine deploy` nebo `aks-engine generate` . Ve výchozím nastavení se tento soubor umístí do relativního adresáře, jako je například `_output/<clustername>/` .
-  `aks-engine rotate-certs`Operace způsobí výpadek serveru rozhraní API.
-  `aks-engine rotate-certs` očekává model rozhraní API, který odpovídá aktuálnímu stavu clusteru. `aks-engine rotate-certs` spustí vzdálené příkazy na uzlech clusteru a použije informace o modelu rozhraní API k navázání zabezpečeného připojení SSH. `aks-engine rotate-certs` také spoléhá na to, že některé prostředky mají být pojmenovány v souladu s původním `aks-engine` nasazením, například virtuální počítače musí splňovat pojmenování, které poskytuje `aks-engine` .
-  `aks-engine rotate-certs` při rotaci certifikátů spoléhá na funkční připojení k rovině řízení clusteru:
    - Pro ověření každého kroku procesu.
    - Chcete-li restartovat nebo znovu vytvořit prostředky clusteru, například **Kube – systémové lusky** a tokeny účtu služby.

    Pokud předáváte certifikáty clusteru ve virtuální síti uzavřené mimo přístup, musíte spustit `aks-engine rotate-certs` z hostitelského virtuálního počítače, který má přístup k síti, například JUMPBOX virtuální počítač, který se nachází ve stejné virtuální síti jako hlavní virtuální počítače.

- Pokud používáte `aks-engine rotate-certs` v produkčním prostředí, doporučuje se připravit test rotace certifikátu na clusteru, který byl sestavený se stejnými specifikacemi. To znamená, že cluster je vytvořen se stejnou konfigurací clusteru, stejnou verzí nástroje příkazového řádku modul AKS a stejnou sadou povolených doplňků jako v produkčním clusteru před provedením rotace certifikátu. Modul AKS podporuje různé konfigurace clusteru a rozsah komplexních testů, které tým AKS Engine nedokáže v podstatě pokrývat s každou možnou konfigurací. Proto doporučujeme, abyste `aks-engine rotate-certs` před provedením operace v produkčním clusteru zajistili v testovacím prostředí, se kterým funguje konkrétní konfigurace clusteru.
-  `aks-engine rotate-certs`nezaručuje zpětnou kompatibilitu.  Pokud jste nasadili s AKS verzí 0.60. x, měli byste preferovat provádění procesu rotace certifikátů s verzí 0.60. x.
-  Načítání nové sady certifikátů z Key Vault není v tomto okamžiku podporováno.
- Použijte spolehlivé síťové připojení. `aks-engine rotate-certs` vyžaduje spuštění několika vzdálených příkazů, které podléhají potenciálním selháním, většinou v případě, že připojení k uzlům clusteru není spolehlivé. Spuštění `aks-engine rotate-certs` z virtuálního počítače běžícího na cílovém Azure Stack razítko může snížit výskyt přechodných problémů.

## <a name="parameters"></a>Parametry

| Parametr           | Povinné | Popis |
| --- | --- | --- |
| --API-model             | ano          | Relativní cesta k modelu rozhraní API (definice clusteru), která deklaruje očekávanou konfiguraci clusteru.       |
| --SSH-Host              | ano          | Plně kvalifikovaný název domény (FQDN) nebo IP adresa naslouchacího procesu SSH, který může mít přístup ke všem uzlům v clusteru.                            |
| --Linux-SSH – privátní klíč | ano          | Cesta k platnému privátnímu klíči SSH pro přístup k uzlům Linux clusteru.                                        |
| --umístění              | ano          | Umístění Azure, do kterého se cluster nasazuje.                                                               |
| --ID předplatného       | ano          | Předplatné Azure, ve kterém je nasazený cluster pro clustery.                                                     |
| --Resource-Group        | ano          | Skupina prostředků Azure, ve které je nasazený cluster.                                                   |
| --ID klienta             | závislosti      | ID klienta instančního objektu Vyžaduje se, pokud je metoda auth nastavená na client_secret nebo client_certificate. |
| --Client-tajný kód         | závislosti      | Tajný kód klienta instančního objektu Vyžaduje se, pokud je metoda auth nastavená na client_secret.                   |
| --Azure-ENV             | závislosti      | Název cílového cloudu Volitelné, pokud je cílový Cloud AzureCloud.                                              |
| --Certificate-Profile   | ne           | Relativní cesta k souboru JSON obsahujícímu novou sadu certifikátů                                        |
| --Force                 | ne           | Vynutit provádění i v případě, že Server API neodpovídá.                                                       |

## <a name="simple-steps-to-rotate-certificates"></a>Jednoduché kroky k otočení certifikátů

Po přečtení všech [požadavků](https://github.com/Azure/aks-engine/blob/master/docs/topics/rotate-certs.md#pre-requirements)spusťte `aks-engine rotate-certs` s příslušnými argumenty:

```bash  
./bin/aks-engine rotate-certs \
  --location <resource-group-location> \
  --api-model <generated-apimodel.json> \
  --linux-ssh-private-key <private-SSH-key> \
  --ssh-host <apiserver-URI> \
  --resource-group <resource-group-name> \
  --client-id <service-principal-id> \
  --client-secret <service-principal-secret> \
  --subscription-id <subscription-id> \
  --azure-env <cloud-name>
```

Například:

```bash  
./bin/aks-engine rotate-certs \
  --location "westus2" \
  --api-model "_output/my-cluster/apimodel.json" \
  --linux-ssh-private-key "~/.ssh/id_rsa" \
  --ssh-host "my-cluster.westus2.cloudapp.azure.com"\
  --resource-group "my-cluster" \
  --client-id "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --client-secret "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --subscription-id "12345678-XXXX-YYYY-ZZZZ-1234567890ab" \
  --azure-env "AzureStackCloud" # optional if targeting AzureCloud
```

## <a name="troubleshooting"></a>Řešení potíží

Pokud se proces rotace certifikátů před dokončením zastaví z důvodu chyby nebo přechodného problému, například připojení k síti, je bezpečné ho znovu spustit `aks-engine rotate-certs` pomocí `--force` příznaku.

Všimněte si také, že `aks-engine rotate-certs` protokoluje výstup všech kroků v souboru `/var/log/azure/rotate-certs.log` (Linux) a `c:\\k\\rotate-certs.log` (Windows).

Další informace o tom, co se stane v digestoři při spuštění této operace nebo pro další přizpůsobení, najdete v [části pod kapotou](https://github.com/Azure/aks-engine/blob/master/docs/topics/rotate-certs.md#under-the-hood).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .  
