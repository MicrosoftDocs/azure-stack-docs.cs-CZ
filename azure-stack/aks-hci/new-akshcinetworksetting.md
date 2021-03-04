---
title: New-AksHciNetworkSetting
author: jessicaguan
description: Příkaz New-AksHciNetworkSetting PowerShell vytvoří objekt pro novou virtuální síť.
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 5babbe99254b23d642696b7ce7c5b865105bd906
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873644"
---
# <a name="new-akshcinetworksetting"></a>New-AksHciNetworkSetting

## <a name="synopsis"></a>Stručný obsah
Vytvořte objekt pro novou virtuální síť.

## <a name="syntax"></a>Syntax
```powershell
New-AksHciNetworkSetting -vnetName <String>
                         -gateway <String>
                         -dnsServers <String[]>
                         -ipAddressPrefix <String>
                         -vipPoolStart <IP address>
                         -vipPoolEnd <IP address>
                         -k8sNodeIpPoolStart <IP address>
                         -k8sNodeIpPoolEnd <IP address>
                        [-vlanID <int>]
                    
```

## <a name="description"></a>Popis
Vytvořte virtuální síť pro nastavení DHCP nebo statickou IP adresu pro řídicí plochu, nástroj pro vyrovnávání zatížení, koncové body agenta a rozsah statických IP adres pro uzly ve všech clusterech Kubernetes. Tato rutina vrátí objekt VirtualNetwork, který lze použít později v rámci kroků konfigurace.

## <a name="examples"></a>Příklady

### <a name="deploy-with-a-static-ip-environment"></a>Nasazení pomocí prostředí se statickými IP adresami

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -k8sNodeIpPoolStart "172.16.10.0" -k8sNodeIpPoolEnd "172.16.10.255" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" -ipAddressPrefix "172.16.0.0/16" -gateway "172.16.0.1" -dnsServers "172.16.0.1" 
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16"
```

> [!NOTE]
> Hodnoty uvedené v tomto ukázkovém příkazu se budou muset přizpůsobit pro vaše prostředí.

### <a name="deploy-with-a-dhcp-environment"></a>Nasazení s prostředím DHCP

```powershell
PS C:\> $vnet = New-AksHciNetworkSetting -vnetName "External" -vipPoolStart "172.16.255.0" -vipPoolEnd "172.16.255.254" 
```

```powershell
PS C:\> Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData"
```

## <a name="parameters"></a>Parametry

### <a name="-vnetname"></a>– vnetName
Název externího přepínače. Pokud chcete získat seznam názvů dostupných přepínačů, spusťte příkaz `Get-VMSwitch` .

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-gateway"></a>– Brána
IP adresa výchozí brány podsítě.

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-dnsservers"></a>-dnsServers
Vyžaduje se při vytváření sítě se statickou IP adresou. Pole IP adres ukazující na servery DNS, které se mají použít pro podsíť Je možné zadat minimálně jeden a maximálně 3 servery. například "8.8.8.8", "192.168.1.1".

```yaml
Type: System.String[]
Parameter Sets: (StaticIP)
Aliases:

Required: False (This is required when creating a network with a static IP.)
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-ipaddressprefix"></a>-ipAddressPrefix
Předpona adresy, která se má použít pro přiřazení statických IP adres.

```yaml
Type: System.String
Parameter Sets: (StaticIP)
Aliases:

Required: True
Position: Named
Default value: external
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolstart"></a>-vipPoolStart
Počáteční IP adresa fondu VIP. Adresa musí být v rozsahu, který je obsluhován serverem DHCP nebo v rozsahu zadaném v podsíti CIDR. IP adresy ve fondu VIP se použijí pro Server API a pro služby Kubernetes Services. Pokud používáte protokol DHCP, ujistěte se, že vaše virtuální IP adresy jsou součástí rezervy IP adresy DHCP. Pokud používáte statickou IP adresu, ujistěte se, že virtuální IP adresy jsou ze stejné podsítě.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-vippoolend"></a>-vipPoolEnd
Koncová IP adresa fondu VIP. Adresa musí být v rozsahu, který je obsluhován serverem DHCP nebo v rozsahu zadaném v podsíti CIDR. IP adresy ve fondu VIP se použijí pro Server API a pro služby Kubernetes Services. Pokud používáte protokol DHCP, ujistěte se, že vaše virtuální IP adresy jsou součástí rezervy IP adresy DHCP. Pokud používáte statickou IP adresu, ujistěte se, že virtuální IP adresy jsou ze stejné podsítě.

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-k8snodeippoolstart"></a>-k8sNodeIpPoolStart
Počáteční IP adresa fondu virtuálních počítačů. Adresa musí být v rozsahu podsítě. To je vyžadováno pro nasazení statických IP adres.

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

### <a name="-k8snodeippoolend"></a>-k8sNodeIpPoolEnd
Koncová IP adresa fondu virtuálních počítačů. Adresa musí být v rozsahu podsítě. To je vyžadováno pro nasazení statických IP adres.

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

### <a name="-vlanid"></a>– vlanID
ID sítě vLAN zadané pro danou síť. Pokud tento parametr vynecháte, nebude síť označená.

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

<!--- ### -macPoolName
The name of the MAC address pool that you wish to use for the Azure Kubernetes Service host VM. The pool will be created with the New-AksHciMacPoolSetting command.

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
--->
