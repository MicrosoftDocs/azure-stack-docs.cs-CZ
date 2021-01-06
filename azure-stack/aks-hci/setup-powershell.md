---
title: Rychlý Start pro nastavení hostitele služby Azure Kubernetes na Azure Stack HCI pomocí prostředí Windows PowerShell
description: Přečtěte si, jak nastavit hostitele služby Azure Kubernetes na Azure Stack HCL pomocí Windows PowerShellu.
author: jessicaguan
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: jeguan
ms.openlocfilehash: 7e36deb4be03e7866371bba3039295fcce065ff7
ms.sourcegitcommit: 0e2c814cf2c154ea530a4e51d71aaf0835fb2b5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918709"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Rychlý Start: nastavení hostitele služby Azure Kubernetes na Azure Stack HCI pomocí prostředí PowerShell

> Platí pro: Azure Stack HCI, Windows Server 2019 Datacenter

V tomto rychlém startu se dozvíte, jak nastavit hostitele služby Azure Kubernetes pomocí prostředí PowerShell. Pokud chcete místo toho použít Centrum pro správu systému Windows, přečtěte si téma [nastavení v centru pro správu Windows](setup.md).

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že máte jednu z následujících možností:
 - cluster 2-4 Azure Stack HCL
 - Cluster s podporou převzetí služeb při selhání datacenter Windows serveru 2019
 - Windows Server 2019 Datacenter s jedním uzlem 
 
Než začnete, ujistěte se, že jste splnili všechny požadavky na stránce [požadavky na systém](.\system-requirements.md) . 
**Doporučujeme, abyste měli Azure Stack clusteru HCI v uzlu 2-4.** Pokud nemáte žádnou z výše uvedených informací, postupujte podle pokynů na [stránce registrace rozhraní HCI Azure Stack](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).    

   > [!IMPORTANT]
   > Při odebírání služby Azure Kubernetes na Azure Stack HCI si přečtěte téma [Odebrání služby Azure Kubernetes na Azure Stack HCL](#remove-azure-kubernetes-service-on-azure-stack-hci) a pečlivě postupujte podle pokynů. 

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>Krok 1: stažení a instalace modulu AksHci PowerShellu

Stáhněte si `AKS-HCI-Public-Preview-Dec-2020` ze [služby Azure Kubernetes na stránce registrace HCL Azure Stack](https://aka.ms/AKS-HCI-Evaluate). Soubor zip `AksHci.Powershell.zip` obsahuje modul PowerShellu.

Pokud jste dříve nainstalovali službu Azure Kubernetes Service na Azure Stack HCI pomocí prostředí PowerShell nebo centra pro správu Windows, existují dva toky instalace pro nový modul prostředí PowerShell:
 - Proveďte čistou instalaci modulu PowerShell, takže začnete s čistým systémem a odebrali jste dříve nasazené úlohy. Chcete-li provést čistou instalaci, použijte krok 1,1.
 - Pokud chcete zajistit, aby systém a úlohy byly na místě, upgradujte modul prostředí PowerShell. Pokud chcete upgradovat modul PowerShellu, navštivte krok 1,2.

### <a name="step-11-clean-install-of-the-akshci-powershell-module"></a>Krok 1,1: čistá instalace modulu PowerShellu pro AksHci

Než budete pokračovat, spusťte následující příkaz.
   ```powershell
   Uninstall-AksHci
   ```

**Zavřete všechna okna PowerShellu.** Odstraňte všechny existující adresáře pro AksHci, AksHci. UI, Course a MSK8sDownloadAgent nacházející se v cestě `%systemdrive%\program files\windowspowershell\modules` . Po odstranění existujících adresářů můžete extrahovat obsah nového souboru ZIP. Přesvědčte se, zda je soubor zip extrahován ve správném umístění ( `%systemdrive%\program files\windowspowershell\modules` ). Pak spusťte následující příkazy.

   ```powershell
   Import-Module AksHci
   ```

Zavřete všechna okna PowerShellu znovu a znovu otevřete relaci pro správu a pokračujte krokem 1,3 – ověřte upgradovaný modul PowerShellu.

### <a name="step-12-upgrade-the-akshci-powershell-module"></a>Krok 1,2: upgrade modulu PowerShellu pro AksHci

**Zavřete všechna okna PowerShellu.** Odstraňte všechny existující adresáře pro AksHci, AksHci. UI, Course a MSK8sDownloadAgent nacházející se v cestě `%systemdrive%\program files\windowspowershell\modules` . Po odebrání těchto adresářů můžete extrahovat obsah nového souboru ZIP. Přesvědčte se, zda je soubor zip extrahován ve správném umístění ( `%systemdrive%\program files\windowspowershell\modules` ). Pak spusťte následující příkazy.

   ```powershell
   Import-Module AksHci
   ```
  
Po spuštění výše uvedených příkazů zavřete všechna okna PowerShellu a znovu otevřete relaci pro správu, abyste ověřili upgrade modulu PowerShellu, jak je uvedeno níže, a pak `Update-AksHci` příkaz spusťte podle pokynů dále v dokumentu.

### <a name="step-13-validate-upgraded-powershell-module"></a>Krok 1,3: ověření aktualizovaného modulu PowerShellu

**Zavřete všechna okna prostředí PowerShell** a znovu otevřete novou relaci pro správu, abyste zkontrolovali, jestli máte nejnovější verzi modulu PowerShellu.  

   ```powershell
   Get-Command -Module AksHci
   ```
 
**Výkonem**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.12     AksHci
Function        Get-AksHciCluster                                  0.2.12     AksHci
Function        Get-AksHciConfig                                   0.2.12     AksHci
Function        Get-AksHciCredential                               0.2.12     AksHci
Function        Get-AksHciKubernetesVersion                        0.2.12     AksHci
Function        Get-AksHciLogs                                     0.2.12     AksHci
Function        Get-AksHciUpdates                                  0.2.12     AksHci
Function        Get-AksHciVersion                                  0.2.12     AksHci
Function        Get-AksHciVmSize                                   0.2.12     AksHci
Function        Install-AksHci                                     0.2.12     AksHci
Function        Install-AksHciAdAuth                               0.2.12     AksHci
Function        Install-AksHciArcOnboarding                        0.2.12     AksHci
Function        New-AksHciCluster                                  0.2.12     AksHci
Function        Remove-AksHciCluster                               0.2.12     AksHci
Function        Restart-AksHci                                     0.2.12     AksHci
Function        Set-AksHciClusterNodeCount                         0.2.12     AksHci
Function        Set-AksHciConfig                                   0.2.12     AksHci
Function        Uninstall-AksHci                                   0.2.12     AksHci
Function        Uninstall-AksHciAdAuth                             0.2.12     AksHci
Function        Uninstall-AksHciArcOnboarding                      0.2.12     AksHci
Function        Update-AksHci                                      0.2.12     AksHci
Function        Update-AksHciCluster                               0.2.12     AksHci
```

## <a name="step-2-prepare-your-machines-for-deployment"></a>Krok 2: Příprava počítačů na nasazení

Spuštěním kontrol na každém fyzickém uzlu zjistíte, jestli jsou splněné všechny požadavky, aby se nainstalovala služba Azure Kubernetes na Azure Stack HCL.

Otevřete PowerShell jako správce a spusťte následující příkaz.

   ```powershell
   Initialize-AksHciNode
   ```

Po dokončení kontrol se zobrazí text "Hotovo" zobrazený zeleným textem.

## <a name="step-3-configure-your-deployment"></a>Krok 3: konfigurace nasazení

Nastavte nastavení konfigurace pro hostitele služby Azure Kubernetes. **Pokud nasazujete v uzlu 2-4 Azure Stack clusteru HCI nebo v clusteru s podporou převzetí služeb při selhání s Windows serverem 2019 Datacenter, musíte zadat `imageDir` `cloudConfigLocation` parametry a.** Pro jeden uzel Windows Server 2019 Datacenter jsou všechny parametry volitelné a nastavené na výchozí hodnoty. Pro zajištění optimálního výkonu však doporučujeme **použít Azure Stack nasazení clusteru HCI v uzlu 2-4.**

Nakonfigurujte nasazení pomocí následujícího příkazu.

   ```powershell
   Set-AksHciConfig [-imageDir <String>]
                    [-cloudConfigLocation <String>]
                    [-nodeConfigLocation <String>]
                    [-vnetName <String>]
                    [-controlPlaneVmSize <VmSize>]
                    [-loadBalancerVmSize <VmSize>]
                    [-sshPublicKey <String>]
                    [-vipPoolStartIp <String>]
                    [-vipPoolEndIp <String>]
                    [-macPoolStart <String>]
                    [-macPoolEnd <String>]
                    [-vlandID <int>]
                    [-kvaLoadBalancerType {unstacked_haproxy, stacked_kube_vip}]
                    [-kvaControlPlaneEndpoint <String>]
                    [-proxyServerHTTP <String>]
                    [-proxyServerHTTPS <String>]
                    [-proxyServerNoProxy <String>]
                    [-proxyServerCredential <PSCredential>]
                    [-cloudServiceCidr <String>]
                    [-workingDir <String>]
                    [-version <String>]
                    [-vnetType <String>]
                    [-nodeAgentPort <int>]
                    [-nodeAgentAuthorizerPort <int>]
                    [-clusterRoleName <String>]
                    [-cloudLocation <String>]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="example"></a>Příklad

Postup nasazení v clusteru uzlu 2-4 se sítí DHCP:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
   ```

Nasazení s virtuálním fondem IP adres:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vipPoolStartIp 10.0.0.20 -vipPoolEndIp 10.0.0.80
   ```

Nasazení pomocí `stacked_kube_vip` Nástroje pro vyrovnávání zatížení:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -kvaLoadBalancerType stacked_kube_vip -kvaControlPlaneEndpoint 10.0.1.10
   ```

Nasazení pomocí proxy server:

   ```powershell
   Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
   ```  

### <a name="optional-parameters"></a>Volitelné parametry

`-imageDir`

Cesta k adresáři, do kterého služba Azure Kubernetes v Azure Stack HCL uloží své image VHD. Výchozí hodnota `%systemdrive%\AksHciImageStore` pro nasazení s jedním uzlem. *U nasazení s více uzly je potřeba tento parametr zadat*. Cesta musí ukazovat na cestu ke sdílenému úložišti, například  `C:\ClusterStorage\Volume2\ImageStore`   nebo na sdílenou složku SMB, jako je například  `\\FileShare\ImageStore` .

`-cloudConfigLocation`

Umístění, do kterého bude cloudový agent ukládat svou konfiguraci. Výchozí hodnota `%systemdrive%\wssdcloudagent` pro nasazení s jedním uzlem. Umístění může být stejné jako cesta  `-imageDir` uvedená výše.U *nasazení s více uzly je potřeba tento parametr zadat*. Cesta musí ukazovat na cestu ke sdílenému úložišti, například  `C:\ClusterStorage\Volume2\ImageStore`   nebo na sdílenou složku SMB, jako je například  `\\FileShare\ImageStore` . Umístění musí být na vysoce dostupné sdílené složce, aby úložiště bylo vždycky dostupné.

`-nodeConfigLocation`

Umístění, kam budou v agentech zaukládána konfigurace. Každý uzel má agenta uzlu, takže jeho konfigurace je místní. Toto umístění musí být místní cesta. Výchozí hodnota `%systemdrive%\programdata\wssdagent` pro všechna nasazení.

`-vnetName`

Název virtuálního přepínače, ke kterému se mají připojit virtuální počítače Pokud již máte na hostiteli externí přepínač, měli byste sem předat název tohoto přepínače. Přepínač bude vytvořen, pokud neexistuje.Použije se výchozí název external.  

`-controlPlaneVmSize`

Velikost virtuálního počítače, který se má vytvořit pro plochu ovládacího prvku. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

`-loadBalancerVmSize`

Velikost virtuálního počítače, který se má vytvořit pro virtuální počítače s Load Balancer Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

`-sshPublicKey`

Cesta k souboru veřejného klíče SSH Pomocí tohoto veřejného klíče se budete moct přihlásit ke každému virtuálnímu počítači vytvořenému službou Azure Kubernetes při nasazení Azure Stack HCI. Pokud máte vlastní veřejný klíč SSH, předáte sem jeho umístění. Pokud není zadaný žádný klíč, budeme ho podíváme za `%systemdrive%\akshci\.ssh\akshci_rsa.pub` . Pokud soubor neexistuje, vygeneruje se a použije pár klíčů SSH ve výše uvedeném umístění.

`-vipPoolStartIp`

Při použití fondů VIP pro vaše nasazení tento parametr určuje spuštění sítě ve fondu. Fondy VIP byste měli použít pro dlouhotrvající nasazení, aby se zaručilo, že fond IP adres zůstane konzistentní. To je užitečné v případě, že máte úlohy, které vždycky potřebují být dosažitelné. Výchozí hodnota je none.

`-vipPoolEndIp`

Při použití fondů VIP pro vaše nasazení tento parametr určuje síťový konec fondu. Fondy VIP byste měli použít pro dlouhotrvající nasazení, aby se zaručilo, že fond IP adres zůstane konzistentní. To je užitečné v případě, že máte úlohy, které vždycky potřebují být dosažitelné. Výchozí hodnota je none.

`-macPoolStart` 

Tato možnost slouží k určení začátku adresy MAC pro fond adres MAC, který chcete použít pro virtuální počítač hostitele služby Azure Kubernetes. Syntaxe adresy MAC vyžaduje, aby nejméně významný bit prvního bajtu měl vždy hodnotu 0 a první bajt by měl být vždy sudým číslem (tj. 00, 02, 04, 06...). Typická adresa MAC může vypadat takto: 02:1E: 2B: 78:00:00. Používejte fondy adres MAC pro dlouhodobá nasazení, aby se přiřazené adresy MAC shodovaly. To je užitečné v případě, že máte požadavek, aby virtuální počítače měly konkrétní adresy MAC. Výchozí hodnota je none.

`-macPoolEnd`

Tato možnost slouží k určení konce adresy MAC pro fond adres MAC, který chcete použít pro virtuální počítač hostitele služby Azure Kubernetes. Syntaxe adresy MAC vyžaduje, aby nejméně významný bit prvního bajtu měl vždy hodnotu 0 a první bajt by měl být vždy sudým číslem (tj. 00, 02, 04, 06...). První bajt předané adresy jako `-macPoolEnd` by měl být stejný jako první bajt adresy předané jako `-macPoolStart` . Používejte fondy adres MAC pro dlouhodobá nasazení, aby se přiřazené adresy MAC shodovaly. To je užitečné v případě, že máte požadavek, aby virtuální počítače měly konkrétní adresy MAC. Výchozí hodnota je none.

`-vlandID`

Dá se použít k zadání ID sítě VLAN. Síťové adaptéry hostitele služby Azure Kubernetes a síťové adaptéry Kubernetes clusteru se označí pomocí zadaného ID sítě VLAN. Tato možnost by se měla použít, pokud existuje konkrétní ID sítě VLAN, které je potřeba označit a získat tak správné připojení. Výchozí hodnota je none.

`-kvaLoadBalancerType`

To má buď `unstacked_haproxy` nebo `stacked_kube_vip` . `unstacked_haproxy` je výchozí, kde se jako koncový bod serveru rozhraní API hostitele služby Azure Kubernetes nasadí samostatný virtuální počítač nástroje pro vyrovnávání zatížení s HAProxy. `stacked_kube_vip`je [Kubevip](https://kube-vip.io/)řešení pro vyrovnávání zatížení pro hostitele služby Azure Kubernetes. Umožňuje zadat statickou IP adresu v hostiteli jako plovoucí IP adresu napříč uzly řídicí roviny, aby byl Server API vysoce dostupný prostřednictvím IP adresy. Pokud zvolíte tuto možnost, musíte v parametru zadat statickou IP adresu `kvaControlPlaneEndpoint` a nasazený virtuální počítač nástroje pro vyrovnávání zatížení není nasazený.

`stacked_kube_vip` vyžaduje IP adresu a více prostředků je uživatelsky přívětivé při ukládání paměti, procesoru a času nasazení. Pokud nemáte IP adresu, která se má použít jako plovoucí IP adresa, měli byste použít `unstacked_haproxy` . Druhá možnost vyžaduje virtuální počítač nástroje pro vyrovnávání zatížení. 

`-kvaControlPlaneEndpoint`

Tato možnost určuje statickou IP adresu, která se použije jako adresa serveru rozhraní API hostitele služby Azure Kubernetes, pokud `kvaLoadBalancerType` je parametr nastavený na hodnotu `stacked_kube_vip` . `stacked_kube_vip`Je-li použit, je nutné zadat tento parametr.

`-proxyServerHTTP`

To poskytuje proxy server identifikátor URI, který by měl být používán všemi součástmi, které vyžadují přístup ke koncovým bodům HTTP. Formát identifikátoru URI zahrnuje schéma identifikátoru URI, adresu serveru a port (tj https://server.com:8888) .. Výchozí hodnota je none.

`-proxyServerHTTPS`

To poskytuje proxy server identifikátor URI, který by měl být používán všemi součástmi, které vyžadují přístup k koncovým bodům HTTPS. Formát identifikátoru URI zahrnuje schéma identifikátoru URI, adresu serveru a port (tj https://server.com:8888) .. Výchozí hodnota je none.

`-proxyServerNoProxy`

Jedná se o řetězec adres oddělených čárkami, který bude z proxy serveru vyloučen. Výchozí hodnota je `localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16`. Tato operace vylučuje provoz místního hostitele (localhost, 127.0.0.1), interní provoz služby Kubernetes (. svc), Kubernetes (10.96.0.0/12) a Kubernetes POD CIDR (10.244.0.0/16) z proxy server. Tento parametr můžete použít k přidání více rozsahů podsítí nebo výjimek názvů. **Nastavení tohoto parametru je velmi důležité, protože pokud není správně nakonfigurovaný, může se stát, že se interní provoz clusteru Kubernetes neočekávaně směruje do vašeho proxy serveru. To může způsobit různé chyby v síťové komunikaci.**


`-proxyServerCredential`

Tím se zobrazí uživatelské jméno a heslo pro ověření na proxy serverech HTTP/HTTPS. Můžete použít `Get-Credential` k vygenerování `PSCredential` objektu, který se předává tomuto parametru. Výchozí hodnota je none.

`-cloudServiceCidr`

Dá se použít k zadání předpony statické IP adresy nebo sítě, která se má přiřadit ke službě Course CloudAgent. Tato hodnota by měla být poskytnuta pomocí formátu CIDR. (Příklad: 192.168.1.2/16). Možná budete chtít tuto skutečnost zadat, aby se zajistilo, že všechny důležité informace o síti budou vždycky dostupné, protože se IP adresa nemění. Výchozí hodnota je none.

`-workingDir`

Toto je pracovní adresář, který modul používá k ukládání malých souborů. `%PROGRAMFILES%\AksHci`   U většiny nasazení je výchozí hodnota a neměla by se měnit.Nedoporučujeme měnit výchozí nastavení. 

`-version`

Verze služby Azure Kubernetes na Azure Stack HCL, kterou chcete nasadit. Výchozí hodnota je nejnovější verze. Nedoporučujeme měnit výchozí nastavení.

`-vnetType`

Typ virtuálního přepínače, ke kterému se chcete připojit nebo kterou chcete vytvořit. Toto je výchozí nastavení typu přepínače "externí". Nedoporučujeme měnit výchozí nastavení.

`-nodeAgentPort`

Číslo portu TCP/IP, na kterém mají agenti naslouchat. Výchozí hodnota je 45000.Nedoporučujeme měnit výchozí nastavení. 

`-nodeAgentAuthorizerPort`

Číslo portu TCP/IP, které agenti uzlu použijí pro svůj autorizační port. Výchozí hodnota je 45001. Nedoporučujeme měnit výchozí nastavení.  

`-clusterRoleName`

Určuje název, který se použije při vytváření cloudového agenta jako generické služby v rámci clusteru. Ve výchozím nastavení se jedná o jedinečný název s předponou CA – a příponou GUID (například CA-9e6eb299-bc0b-4f00-9fd7-942843820c26). Nedoporučujeme měnit výchozí nastavení.

`-cloudLocation` 

Tento parametr poskytuje vlastní název Microsoft provozovaného cloudového umístění. Výchozí název je "MocLocation". Nedoporučujeme měnit výchozí nastavení.

`-skipHostLimitChecks`

Vyžádá skript, aby přeskočil všechny kontroly, které potvrdí, že je k dispozici paměť a místo na disku, než umožní nasazení pokračovat. Toto nastavení nedoporučujeme používat.

`-insecure`

Nasadí službu Azure Kubernetes na Azure Stack komponenty HCI, jako je Cloud agent a agenti (s) v nezabezpečeném režimu (bez zabezpečených připojení TLS).Nedoporučujeme používat v produkčním prostředí nezabezpečený režim.

`-skipUpdates`

Tento příznak použijte, pokud chcete přeskočit všechny dostupné aktualizace. Toto nastavení nedoporučujeme používat.

`-forceDnsReplication`

Replikace DNS může v některých systémech trvat až hodinu. Tím dojde k pomalému nasazení. Pokud dojde k tomuto problému, uvidíte, že Install-AksHci zablokování ve smyčce. Pokud chcete tento problém obdržet, zkuste použít tento příznak. `-forceDnsReplication`Příznak není zaručená oprava. Pokud logika za příznakem selže, bude chyba skrytá a příkaz bude proveden, jako by nebyl poskytnut příznak. 

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Resetování služby Azure Kubernetes v Azure Stack konfiguraci rozhraní HCI

Pokud chcete resetovat službu Azure Kubernetes na Azure Stack konfiguraci HCI, spusťte následující příkazy. Spuštění tohoto příkazu na vlastní nastavení obnoví výchozí hodnoty konfigurace.

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>Krok 4: zahájení nového nasazení

Po dokončení konfigurace nasazení musíte spustit nasazení. Tím se nainstaluje služba Azure Kubernetes na Azure Stack agenti a služby HCI a hostitele služby Azure Kubernetes.

Nasazení zahájíte spuštěním následujícího příkazu.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Ověření nasazeného hostitele služby Azure Kubernetes

Pokud chcete zajistit, aby byl nasazený hostitel služby Azure Kubernetes, spusťte následující příkaz. Po nasazení budete moci získat clustery s Kubernetes pomocí stejného příkazu.

```powershell
Get-AksHciCluster
```

**Výkonem**
```

Name            : clustergroup-management
Version         : v1.18.8
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>Krok 5: přístup ke svým clusterům pomocí kubectl

Pokud chcete získat přístup k hostiteli služby Azure Kubernetes nebo ke clusteru Kubernetes pomocí kubectl, spusťte následující příkaz. Tato akce použije zadaný soubor kubeconfig clusteru jako výchozí soubor kubeconfig pro kubectl.

```powershell
Get-AksHciCredential -clusterName <String>
                     [-outputLocation <String>]
```

### <a name="example"></a>Příklad

```powershell
Get-AksHciCredential -clusterName clustergroup-management
```

### <a name="required-parameters"></a>Povinné parametry

`clusterName`

Název clusteru.

### <a name="optional-parameters"></a>Volitelné parametry

`outputLocation`

Umístění, kam chcete stáhnout kubeconfig. Výchozí je `%USERPROFILE%\.kube`.

## <a name="get-logs"></a>Získání protokolů

Pokud chcete získat protokoly ze všech lusků, spusťte následující příkaz. Tento příkaz vytvoří výstupní složku zip s názvem `akshcilogs` v cestě `C:\wssd\akshcilogs` .

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>Aktualizace na nejnovější verzi služby Azure Kubernetes ve Azure Stack HCI

Pokud chcete aktualizovat na nejnovější verzi služby Azure Kubernetes v Azure Stack HCI, spusťte následující příkaz. Příkaz Update funguje jenom v případě, že máte nainstalovanou verzi nástroje OCT. Nebude fungovat pro vydání starší verze než v říjnu. Tento příkaz Update aktualizuje hostitele služby Azure Kubernetes a místní Microsoft provozovanou cloudovou platformu. V této verzi Preview je verze Kubernetes a verze operačního systému AKS stále stejná. Tento příkaz neupgraduje žádné existující clustery úloh. Nové clustery úloh vytvořené po aktualizaci hostitele AKS se budou lišit od stávajících clusterů úloh v souvislosti s verzí operačního systému Windows node a verzí Kubernetes.

   ```powershell
   Update-AksHci
   ```
   
Doporučujeme aktualizovat clustery úloh hned po aktualizaci clusteru pro správu, aby nedocházelo ke spouštění nepodporovaných verzí operačního systému Windows Server v clusterech Kubernetes s uzly Windows. Pokud chcete aktualizovat svůj cluster úloh, přejděte na web [aktualizovat cluster úloh](create-kubernetes-cluster-powershell.md).

## <a name="restart-azure-kubernetes-service-on-azure-stack-hci"></a>Restartovat službu Azure Kubernetes v Azure Stack HCL

Po restartování služby Azure Kubernetes v systému Azure Stack HCL dojde k odebrání všech clusterů Kubernetes a hostitele služby Azure Kubernetes. Odinstaluje taky službu Azure Kubernetes na Azure Stack agenty a služby HCI v uzlech. Pak se vrátí zpět kroky původní instalace, dokud se hostitel znovu nevytvoří. Služba Azure Kubernetes na Azure Stack konfiguraci HCI, kterou jste nakonfigurovali prostřednictvím nástroje `Set-AksHciConfig` , a stažené image VHDX se zachovají.

Pokud chcete restartovat službu Azure Kubernetes v Azure Stack HCL se stejným nastavením konfigurace, spusťte následující příkaz.

```powershell
Restart-AksHci
```

## <a name="reset-configuration-settings-and-reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Resetovat nastavení konfigurace a přeinstalovat službu Azure Kubernetes Service na Azure Stack HCI

Pokud chcete službu Azure Kubernetes přeinstalovat na Azure Stack HCL s jiným nastavením konfigurace, spusťte nejdřív následující příkaz.

```powershell
Uninstall-AksHci
```

Po spuštění výše uvedeného příkazu můžete změnit nastavení konfigurace pomocí následujícího příkazu. Parametry zůstávají stejné, jak je popsáno v kroku 3. Pokud tento příkaz spustíte bez zadaných parametrů, obnoví se výchozí hodnoty parametrů.

```powershell
Set-AksHciConfig
```

Po změně konfigurace na požadovaná nastavení spusťte následující příkaz, který přeinstaluje Azure Stack Kubernetes na Azure Stack HCI.

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Odebrat službu Azure Kubernetes v Azure Stack HCI

Pokud chcete odebrat službu Azure Kubernetes v Azure Stack HCI, spusťte následující příkaz. **Pokud odinstalujete nasazení centra pro správu Windows pomocí PowerShellu, musíte příkaz Spustit s `-Force` příznakem.**

```powershell
Uninstall-AksHci
```

Po spuštění výše uvedeného příkazu můžete spustit `Install-AksHci` příkaz pro instalaci hostitele služby Azure Kubernetes se stejnou konfigurací, jako jste předtím. Pokud chcete konfiguraci změnit, spusťte `Set-AksHciConfig` se změnami, které chcete provést před spuštěním příkazu install.

Pokud nechcete zachovat starou konfiguraci, spusťte následující příkaz.

```powershell
Uninstall-AksHci -Force
```

Pokud se příkazy PowerShellu spouštějí v clusteru, kde se dřív používalo centrum pro správu Windows k nasazení, modul PowerShell zkontroluje existenci konfiguračního souboru centra pro správu systému Windows. Centrum pro správu systému Windows umístí konfigurační soubor centra pro správu systému Windows ve všech uzlech. **Pokud použijete příkaz uninstall a vrátíte se zpátky do centra pro správu systému Windows, spusťte výše uvedený příkaz uninstall s `-Force` příznakem. Pokud tomu tak není, PowerShell a centrum pro správu Windows nebudou synchronizovány.**

## <a name="next-steps"></a>Další kroky

- [Vytvoření clusteru Kubernetes pro vaše aplikace](create-kubernetes-cluster-powershell.md)
