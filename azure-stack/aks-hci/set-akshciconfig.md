---
title: Set-AksHciConfig
author: jessicaguan
description: Příkaz Set-AksHciConfig PowerShellu aktualizuje nastavení konfigurace pro hostitele služby Azure Kubernetes.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 80a6fc50bbfcaf028d6c810bbef7e2d6b4508cab
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873748"
---
# <a name="set-akshciconfig"></a>Set-AksHciConfig

## <a name="synopsis"></a>Stručný obsah
Nastaví nebo aktualizuje nastavení konfigurace pro hostitele služby Azure Kubernetes.

## <a name="syntax"></a>Syntax

### <a name="set-configuration-for-host"></a>Nastavit konfiguraci pro hostitele
```powershell
Set-AksHciConfig [-imageDir <String>]
                 [-cloudConfigLocation <String>]
                 [-nodeConfigLocation <String>]
                 [-vnet <Virtual Network>]
                 [-controlPlaneVmSize <VmSize>]
                 [-sshPublicKey <String>]
                 [-macPoolStart <String>]
                 [-macPoolEnd <String>]       
                 [-proxyServerHTTP <String>]
                 [-proxyServerHTTPS <String>]
                 [-proxyServerNoProxy <String>]
                 [-proxyServerCertFile <String>]
                 [-proxyServerCredential <PSCredential>]
                 [-cloudServiceCidr <String>]
                 [-workingDir <String>]
                 [-version <String>]
                 [-nodeAgentPort <int>]
                 [-nodeAgentAuthorizerPort <int>]
                 [-clusterRoleName <String>]
                 [-cloudLocation <String>]
                 [-skipHostLimitChecks]
                 [-insecure]
                 [-skipUpdates]
                 [-forceDnsReplication]
                 [-enableDiagnosticData]   
```

## <a name="description"></a>Popis
Nastavte nastavení konfigurace pro hostitele služby Azure Kubernetes. Pokud nasazujete v uzlu 2-4 Azure Stack clusteru HCI nebo v clusteru s podporou převzetí služeb při selhání systému Windows Server 2019 Datacenter, je nutné zadat parametry imageDir a cloudConfigLocation. Pro jeden uzel Windows Server 2019 Datacenter jsou všechny parametry volitelné a nastavené na výchozí hodnoty. Pro zajištění optimálního výkonu však doporučujeme použít Azure Stack nasazení clusteru HCI v uzlu 2-4.

## <a name="examples"></a>Příklady

### <a name="to-deploy-on-a-2-4-node-cluster-with-dhcp-networking"></a>Nasazení na cluster uzlu 2-4 se sítí DHCP
```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config
```

### <a name="to-deploy-with-a-virtual-ip-pool"></a>Nasazení s virtuálním fondem IP adres
```powershell
PS C:\> New-AksHciNetworkSetting -name newNetwork -subnetCidr 192.168.2.0/32 -defaultGateway 192.168.2.1 -subnetVSwitch External -vipPoolStart 192.168.2.20 -vipPoolEnd 192.168.2.80   
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -networkSettings newNetwork
```

### <a name="to-deploy-with-a-proxy-server"></a>Nasazení pomocí proxy server
```powershell
PS C:\> $credential = Get-Credential
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -proxyServerHttp "http://proxy.contoso.com:8888" -proxyServerHttps "http://proxy.contoso.com:8888" -proxyServerNoProxy "localhost,127.0.0.1,.svc,10.96.0.0/12,10.244.0.0/16,10.231.110.0/24,10.68.237.0/24" -proxyServerCredential $credential
```

## <a name="parameters"></a>Parametry

### <a name="-imagedir"></a>-imageDir
Cesta k adresáři, do kterého služba Azure Kubernetes v Azure Stack HCL uloží své image VHD. Výchozí hodnota `%systemdrive%\AksHciImageStore` pro nasazení s jedním uzlem. U nasazení s více uzly je potřeba tento parametr zadat. Cesta musí ukazovat na cestu ke sdílenému úložišti, například  `C:\ClusterStorage\Volume2\ImageStore`   nebo na sdílenou složku SMB, jako je například  `\\FileShare\ImageStore` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\AksHciImageStore
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudconfiglocation"></a>-cloudConfigLocation
Umístění, do kterého bude cloudový agent ukládat svou konfiguraci. Výchozí hodnota `%systemdrive%\wssdcloudagent` pro nasazení s jedním uzlem. Umístění může být stejné jako cesta-imageDir výše.U nasazení s více uzly je potřeba tento parametr zadat. Cesta musí ukazovat na cestu ke sdílenému úložišti, například  `C:\ClusterStorage\Volume2\ImageStore`   nebo na sdílenou složku SMB, jako je například  `\\FileShare\ImageStore` . Umístění musí být na vysoce dostupné sdílené složce, aby úložiště bylo vždycky dostupné.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\wssdcloudagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeconfiglocation"></a>-nodeConfigLocation
Umístění, kam budou v agentech zaukládána konfigurace. Každý uzel má agenta uzlu, takže jeho konfigurace je místní. Toto umístění musí být místní cesta. Výchozí hodnota `%systemdrive%\programdata\wssdagent` pro všechna nasazení.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %systemdrive%\programdata\wssdagent
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vnet"></a>– VNet
Název objektu AksHciNetworkSetting vytvořeného příkazem New-AksHciNetworkSetting.
```yaml
Type: VirtualNetwork
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-controlplanevmsize"></a>-controlPlaneVmSize
Velikost virtuálního počítače, který se má vytvořit pro plochu ovládacího prvku. Výchozí hodnota je Standard_A4_V2. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Standard_A4_V2
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-sshpublickey"></a>– sshPublicKey
Cesta k souboru veřejného klíče SSH Pomocí tohoto veřejného klíče se budete moct přihlásit ke každému virtuálnímu počítači vytvořenému službou Azure Kubernetes při nasazení Azure Stack HCI. Pokud máte vlastní veřejný klíč SSH, předáte sem jeho umístění. Pokud není zadaný žádný klíč, budeme vyhledáme v `%systemdrive%\akshci\.ssh\akshci_rsa` . pub. Pokud soubor neexistuje, vygeneruje se a použije pár klíčů SSH ve výše uvedeném umístění.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolstart"></a>-macPoolStart
Tato možnost slouží k určení začátku adresy MAC pro fond adres MAC, který chcete použít pro virtuální počítač hostitele služby Azure Kubernetes. Syntaxe adresy MAC vyžaduje, aby nejméně významný bit prvního bajtu měl vždy hodnotu 0 a první bajt by měl být vždy sudým číslem (tj. 00, 02, 04, 06...). Typická adresa MAC může vypadat takto: 02:1E: 2B: 78:00:00. Používejte fondy adres MAC pro dlouhodobá nasazení, aby se přiřazené adresy MAC shodovaly. To je užitečné v případě, že máte požadavek, aby virtuální počítače měly konkrétní adresy MAC. Výchozí hodnota je none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-macpoolend"></a>-macPoolEnd
Tato možnost slouží k určení konce adresy MAC pro fond adres MAC, který chcete použít pro virtuální počítač hostitele služby Azure Kubernetes. Syntaxe adresy MAC vyžaduje, aby nejméně významný bit prvního bajtu měl vždy hodnotu 0 a první bajt by měl být vždy sudým číslem (tj. 00, 02, 04, 06...). První bajt adresy předané jako-macPoolEnd by měl být stejný jako první bajt adresy předané jako-macPoolStart. Používejte fondy adres MAC pro dlouhodobá nasazení, aby se přiřazené adresy MAC shodovaly. To je užitečné v případě, že máte požadavek, aby virtuální počítače měly konkrétní adresy MAC. Výchozí hodnota je none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttp"></a>-proxyServerHTTP
To poskytuje proxy server identifikátor URI, který by měl být používán všemi součástmi, které vyžadují přístup ke koncovým bodům HTTP. Formát identifikátoru URI zahrnuje schéma identifikátoru URI, adresu serveru a port (tj https://server.com:8888) .. Výchozí hodnota je none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyserverhttps"></a>-proxyServerHTTPS
To poskytuje proxy server identifikátor URI, který by měl být používán všemi součástmi, které vyžadují přístup k koncovým bodům HTTPS. Formát identifikátoru URI zahrnuje schéma identifikátoru URI, adresu serveru a port (tj https://server.com:8888) .. Výchozí hodnota je none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservernoproxy"></a>-proxyServerNoProxy
Jedná se o řetězec adres oddělených čárkami, který bude z proxy serveru vyloučen. Výchozí hodnota je localhost, 127.0.0.1,. svc, 10.96.0.0/12, 10.244.0.0/16. Tato operace vylučuje provoz místního hostitele (localhost, 127.0.0.1), interní provoz služby Kubernetes (. svc), Kubernetes (10.96.0.0/12) a Kubernetes POD CIDR (10.244.0.0/16) z proxy server. Tento parametr můžete použít k přidání více rozsahů podsítí nebo výjimek názvů. **Nastavení tohoto parametru je velmi důležité, protože pokud není správně nakonfigurovaný, může se stát, že se interní provoz clusteru Kubernetes neočekávaně směruje do vašeho proxy serveru. To může způsobit různé chyby v síťové komunikaci.**

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercertfile"></a>-proxyServerCertFile
Certifikát použitý k ověření proxy server.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:
 
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-proxyservercredential"></a>-proxyServerCredential
Tím se zobrazí uživatelské jméno a heslo pro ověření na proxy serverech HTTP/HTTPS. Můžete použít `Get-Credential` k vygenerování objektu PSCredential, který se předává tomuto parametru. Výchozí hodnota je none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudservicecidr"></a>-cloudServiceCidr
Dá se použít k zadání předpony statické IP adresy nebo sítě, která se má přiřadit ke službě Course CloudAgent. Tato hodnota by měla být poskytnuta pomocí formátu CIDR. (Příklad: 192.168.1.2/16). Možná budete chtít tuto skutečnost zadat, aby se zajistilo, že všechny důležité informace o síti budou vždycky dostupné, protože se IP adresa nemění. Výchozí hodnota je none.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-workingdir"></a>-workingDir
Toto je pracovní adresář, který modul používá k ukládání malých souborů. `%PROGRAMFILES%\AksHci`   U většiny nasazení je výchozí hodnota a neměla by se měnit.Nedoporučujeme měnit výchozí nastavení.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %PROGRAMFILES%\AksHci
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-version"></a>– verze
Verze služby Azure Kubernetes na Azure Stack HCL, kterou chcete nasadit. Výchozí hodnota je nejnovější verze. Nedoporučujeme měnit výchozí nastavení.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Latest version
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentport"></a>-nodeAgentPort
Číslo portu TCP/IP, na kterém mají agenti naslouchat. Výchozí hodnota je 45000.Nedoporučujeme měnit výchozí nastavení.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45000
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-nodeagentauthorizerport"></a>-nodeAgentAuthorizerPort
Číslo portu TCP/IP, které agenti uzlu použijí pro svůj autorizační port. Výchozí hodnota je 45001. Nedoporučujeme měnit výchozí nastavení. 

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 45001
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clusterrolename"></a>-clusterRoleName
Určuje název, který se použije při vytváření cloudového agenta jako generické služby v rámci clusteru. Ve výchozím nastavení se jedná o jedinečný název s předponou CA – a příponou GUID (například CA-9e6eb299-bc0b-4f00-9fd7-942843820c26). Nedoporučujeme měnit výchozí nastavení.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: A unique name with a prefix of ca- and a guid suffix
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-cloudlocation"></a>-cloudLocation
Tento parametr poskytuje vlastní název Microsoft provozovaného cloudového umístění. Výchozí název je "MocLocation". Nedoporučujeme měnit výchozí nastavení.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: MocLocation
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skiphostlimitchecks"></a>-skipHostLimitChecks
Vyžádá skript, aby přeskočil všechny kontroly, které potvrdí, že je k dispozici paměť a místo na disku, než umožní nasazení pokračovat. Toto nastavení nedoporučujeme používat.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-insecure"></a>– nezabezpečené
Nasadí službu Azure Kubernetes na Azure Stack komponenty HCI, jako je Cloud agent a agenti (s) v nezabezpečeném režimu (bez zabezpečených připojení TLS).Nedoporučujeme používat v produkčním prostředí nezabezpečený režim.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipupdates"></a>-skipUpdates
Tento příznak použijte, pokud chcete přeskočit všechny dostupné aktualizace. Toto nastavení nedoporučujeme používat.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-forcednsreplication"></a>-forceDnsReplication
Replikace DNS může v některých systémech trvat až hodinu. Tím dojde k pomalému nasazení. Pokud dojde k tomuto problému, uvidíte, že Install-AksHci zablokování ve smyčce. Pokud chcete tento problém obdržet, zkuste použít tento příznak. Příznak-forceDnsReplication není zaručená oprava. Pokud logika za příznakem selže, bude chyba skrytá a příkaz bude proveden, jako by nebyl poskytnut příznak.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-enablediagnosticdata"></a>-enableDiagnosticData'

Pomocí tohoto příznaku můžete vyjádřit souhlas s odesláním požadovaných diagnostických dat do Microsoftu. To je užitečné, pokud chcete přeskočit výzvu k vyjádření souhlasu, která `Set-AksHciConfig` bude uvedena jinak. `Install-AksHci` selže, Pokud nepřijmete výzvu k zadání souhlasu, když `Set-AksHciConfig` ho prezentuje.
