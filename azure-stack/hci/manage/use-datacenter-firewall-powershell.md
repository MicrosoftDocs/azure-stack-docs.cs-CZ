---
title: Použití brány firewall Datacenter ke konfiguraci seznamů ACL pomocí PowerShellu
description: V tomto tématu se naučíte začít používat bránu firewall Datacenter pro softwarově definované sítě v Azure Stack HCL, Windows Server 2019 a Windows Server 2016.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/09/2021
ms.openlocfilehash: 38f7511d4ccc11c4801abde82ec8ab1c7091eb69
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562638"
---
# <a name="use-datacenter-firewall-to-configure-acls-with-powershell"></a>Použití brány firewall Datacenter ke konfiguraci seznamů ACL pomocí PowerShellu

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019, Windows Server 2016

Toto téma obsahuje pokyny pro konfiguraci seznamů řízení přístupu (ACL) pro správu toku přenosů dat pomocí [brány firewall Datacenter](../concepts/datacenter-firewall-overview.md) pro softwarově definované sítě (SDN) v Azure Stack HCL pomocí prostředí Windows PowerShell. Povolíte a nakonfigurujete bránu firewall Datacenter vytvořením seznamů řízení přístupu, které se aplikují na podsíť nebo síťové rozhraní. V ukázkových skriptech v tomto tématu se používají příkazy prostředí Windows PowerShell exportované z modulu **NetworkController** . Ke konfiguraci a správě seznamů ACL můžete použít také centrum pro správu systému Windows.

## <a name="configure-datacenter-firewall-to-allow-all-traffic"></a>Konfigurace brány firewall datového centra pro povolení všech přenosů

Po nasazení SDN byste měli v novém prostředí otestovat základní síťové připojení. K tomu je potřeba vytvořit pravidlo pro bránu firewall datového centra, které umožňuje veškerý síťový provoz bez omezení.

Pomocí záznamů v následující tabulce můžete vytvořit sadu pravidel, která umožní veškerý příchozí a odchozí síťový provoz.

| Zdrojová IP adresa | Cílová IP adresa | Protokol | Zdrojový port | Cílový port | Směr | Akce | Priorita |
|:---------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|    \*     |       \*       |   Vše    |     \*      |        \*        |  Příchozí  | Povolit  |   100    |
|    \*     |       \*       |   Vše    |     \*      |        \*        | Odchozí  | Povolit  |   110    |

---

V tomto příkladu vytvoříte seznam ACL se dvěma pravidly:

1. **AllowAll_Inbound** – povolí průchod veškerého síťového provozu do síťového rozhraní, ve kterém je tento seznam řízení přístupu (ACL) nakonfigurovaný.
2. **AllowAllOutbound** – povolí předání všech přenosů ze síťového rozhraní. Tento seznam řízení přístupu identifikovaný ID prostředku "AllowAll-1" je teď připravený k použití ve virtuálních podsítích a síťových rozhraních.

Nejdřív se připojte k jednomu z uzlů clusteru otevřením relace PowerShellu:

```PowerShell
Enter-PSSession <server-name>
```

Potom spuštěním následujícího skriptu vytvořte seznam řízení přístupu:

```PowerShell
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"
$aclrule1 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule1.Properties = $ruleproperties
$aclrule1.ResourceId = "AllowAll_Inbound"
$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "110"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"
$aclrule2 = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule2.Properties = $ruleproperties
$aclrule2.ResourceId = "AllowAll_Outbound"
$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = @($aclrule1, $aclrule2)
New-NetworkControllerAccessControlList -ResourceId "AllowAll" -Properties $acllistproperties -ConnectionUri <NC REST FQDN>
```

>[!NOTE]
>Referenční informace k příkazům pro síťový adaptér prostředí Windows PowerShell najdete v tématu [rutiny síťového adaptéru](/powershell/module/networkcontroller/).

## <a name="use-acls-to-limit-traffic-on-a-subnet"></a>Omezení provozu v podsíti pomocí seznamů ACL
V tomto příkladu vytvoříte seznam řízení přístupu, který brání tomu, aby virtuální počítače v podsíti 192.168.0.0/24 vzájemně komunikovaly. Tento typ seznamu ACL je vhodný k tomu, aby se omezila možnost dalšího navýšení v rámci podsítě, a zároveň umožňuje virtuálním počítačům přijímat žádosti mimo podsíť a taky komunikovat s ostatními službami v jiných podsítích.

|   Zdrojová IP adresa    | Cílová IP adresa | Protokol | Zdrojový port | Cílový port | Směr | Akce | Priorita |
|:--------------:|:--------------:|:--------:|:-----------:|:----------------:|:---------:|:------:|:--------:|
|  192.168.0.1   |       \*       |   Vše    |     \*      |        \*        |  Příchozí  | Povolit  |   100    |
|       \*       |  192.168.0.1   |   Vše    |     \*      |        \*        | Odchozí  | Povolit  |   101    |
| 192.168.0.0/24 |       \*       |   Vše    |     \*      |        \*        |  Příchozí  | Blok  |   102    |
|       \*       | 192.168.0.0/24 |   Vše    |     \*      |        \*        | Odchozí  | Blok  |   103    |
|       \*       |       \*       |   Vše    |     \*      |        \*        |  Příchozí  | Povolit  |   104    |
|       \*       |       \*       |   Vše    |     \*      |        \*        | Odchozí  | Povolit  |   105    |

---

Seznam ACL vytvořený ukázkovým skriptem, který identifikuje **podsíť s ID podsítě-192-168-0-0**, se teď dá použít pro podsíť virtuální sítě, která používá adresu podsítě 192.168.0.0/24. Jakékoli síťové rozhraní, které je připojené k této podsíti virtuální sítě, automaticky získá výše použitá pravidla seznamu ACL.

Následuje ukázkový skript pro vytvoření tohoto seznamu ACL pomocí síťového adaptéru REST API:

```PowerShell
import-module networkcontroller
$ncURI = "https://mync.contoso.local"
$aclrules = @()

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "192.168.0.1"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "100"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.1"
$ruleproperties.Priority = "101"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowRouter_Outbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "192.168.0.0/24"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "102"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Deny"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "192.168.0.0/24"
$ruleproperties.Priority = "103"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "DenySubnet_Outbound"

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "104"
$ruleproperties.Type = "Inbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Inbound"
$aclrules += $aclrule

$ruleproperties = new-object Microsoft.Windows.NetworkController.AclRuleProperties
$ruleproperties.Protocol = "All"
$ruleproperties.SourcePortRange = "0-65535"
$ruleproperties.DestinationPortRange = "0-65535"
$ruleproperties.Action = "Allow"
$ruleproperties.SourceAddressPrefix = "*"
$ruleproperties.DestinationAddressPrefix = "*"
$ruleproperties.Priority = "105"
$ruleproperties.Type = "Outbound"
$ruleproperties.Logging = "Enabled"

$aclrule = new-object Microsoft.Windows.NetworkController.AclRule
$aclrule.Properties = $ruleproperties
$aclrule.ResourceId = "AllowAll_Outbound"
$aclrules += $aclrule

$acllistproperties = new-object Microsoft.Windows.NetworkController.AccessControlListProperties
$acllistproperties.AclRules = $aclrules

New-NetworkControllerAccessControlList -ResourceId "Subnet-192-168-0-0" -Properties $acllistproperties -ConnectionUri $ncURI
```

## <a name="add-an-acl-to-a-network-interface"></a>Přidat seznam řízení přístupu k síťovému rozhraní

Jakmile vytvoříte seznam ACL a přiřadíte ho k virtuální podsíti, možná budete chtít přepsat výchozí seznam ACL ve virtuální podsíti s konkrétním seznamem ACL pro jednotlivá síťová rozhraní. Počínaje systémem Windows Server 2019 Datacenter můžete použít určité seznamy ACL přímo na síťová rozhraní připojená k logickým sítím SDN a navíc k virtuálním sítím SDN. Pokud máte nastavené seznamy ACL ve virtuální podsíti připojené k síťovému rozhraní, budou se použít oba seznamy řízení přístupu (ACL) a seznamy ACL pro síťové rozhraní se budou upřednostňovat nad rámec seznamů ACL virtuální podsítě.

V tomto příkladu ukážeme, jak přidat seznam ACL k virtuální síti.

>[!TIP]
>Seznam ACL je také možné přidat najednou, když vytvoříte síťové rozhraní.

1. Získejte nebo vytvořte síťové rozhraní, do kterého budete přidávat seznam řízení přístupu.

   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Získejte nebo vytvořte seznam ACL, který budete přidávat do síťového rozhraní.

   ```PowerShell
   $acl = get-networkcontrolleraccesscontrollist -ConnectionUri $uri -ResourceId "AllowAllACL"
   ```

3. Přiřaďte seznam ACL k vlastnosti AccessControlList síťového rozhraní.

   ```PowerShell
    $nic.properties.ipconfigurations[0].properties.AccessControlList = $acl
   ```

4. Přidejte síťové rozhraní do síťového adaptéru.

   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="remove-an-acl-from-a-network-interface"></a>Odebrání seznamu ACL ze síťového rozhraní

V tomto příkladu vám ukážeme, jak odebrat seznam ACL ze síťového rozhraní. Odebrání seznamu ACL aplikuje výchozí sadu pravidel na síťové rozhraní. Výchozí sada pravidel umožňuje veškerý odchozí provoz, ale blokuje veškerý příchozí provoz. Pokud chcete povolit veškerý příchozí provoz, musíte postupovat podle předchozího [příkladu](#add-an-acl-to-a-network-interface) a přidat seznam ACL, který umožňuje všechny příchozí a odchozí přenosy.

1. Získejte síťové rozhraní, ze kterého budete seznam ACL odebrat.
   ```PowerShell
   $nic = get-networkcontrollernetworkinterface -ConnectionUri $uri -ResourceId "MyVM_Ethernet1"
   ```

2. Přiřaďte $null k vlastnosti ipConfiguration.
   ```PowerShell
   $nic.properties.ipconfigurations[0].properties.AccessControlList = $null
   ```

3. Přidejte objekt síťového rozhraní do síťového adaptéru.
   ```PowerShell
   new-networkcontrollernetworkinterface -ConnectionUri $uri -Properties $nic.properties -ResourceId $nic.resourceid
   ```

## <a name="firewall-auditing"></a>Auditování brány firewall

V systému Windows Server 2019 je auditování brány firewall novou funkcí pro bránu firewall datového centra, která zaznamenává libovolný tok zpracovaný pravidly brány firewall SDN. Zaznamenávají se všechny seznamy ACL s povoleným protokolováním. Soubory protokolu musí být v syntaxi, která je konzistentní s [protokoly toku služby Azure Network Watcher](/azure/network-watcher/network-watcher-nsg-flow-logging-overview). Tyto protokoly je možné použít pro diagnostiku nebo archivaci pro pozdější analýzu.

Tady je ukázkový skript, který povolí auditování brány firewall na hostitelských serverech. Aktualizujte proměnné na začátku a spusťte ji na Azure Stack clusteru HCI se nasazeným [síťovým adaptérem](../concepts/network-controller-overview.md) :

```PowerShell
$logpath = "C:\test\log1"
$servers = @("sa18n22-2", "sa18n22-3", "sa18n22-4")
$uri = "https://sa18n22sdn.sa18.nttest.microsoft.com"

# Create log directories on the hosts
invoke-command -Computername $servers  {
    param(
        $Path
    )
    mkdir $path    -force
} -argumentlist $LogPath

# Set firewall auditing settings on Network Controller
$AuditProperties = new-object Microsoft.Windows.NetworkController.AuditingSettingsProperties
$AuditProperties.OutputDirectory = $logpath
set-networkcontrollerauditingsettingsconfiguration -connectionuri $uri -properties $AuditProperties -force  | out-null

# Enable logging on each server
$servers = get-networkcontrollerserver -connectionuri $uri
foreach ($s in $servers) {
    $s.properties.AuditingEnabled = @("Firewall")
    new-networkcontrollerserver -connectionuri $uri -resourceid $s.resourceid -properties $s.properties -force | out-null
}
```

Po povolení se nový soubor zobrazí v zadaném adresáři každého hostitele přibližně jednou za hodinu.  Tyto soubory byste měli pravidelně zpracovávat a odebírat je z hostitelů.  Aktuální soubor má nulovou délku a je uzamčen, dokud není vyprázdněna v následující hodinové značce:

```syntax
PS C:\test\log1> dir

    Directory: C:\test\log1

Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/19/2018   6:28 AM          17055 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL122803093.json
-a----        7/19/2018   7:28 AM           7880 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL132803173.json
-a----        7/19/2018   8:28 AM           7867 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL142803264.json
-a----        7/19/2018   9:28 AM          10949 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL152803360.json
-a----        7/19/2018   9:28 AM              0 SdnFirewallAuditing.d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a.20180719TL162803464.json
```

Tyto soubory obsahují sekvenci událostí toku, například:

```syntax
{
    "records": [
        {
            "properties":{
                "Version":"1.0",
                "flows":[
                    {
                        "flows":[
                            {
                                "flowTuples":["1531963580,192.122.0.22,192.122.255.255,138,138,U,I,A"],
                                "portId":"9",
                                "portName":"7290436D-0422-498A-8EB8-C6CF5115DACE"
                            }
                        ],
                        "rule":"Allow_Inbound"
                    }
                ]
            },
            "operationName":"NetworkSecurityGroupFlowEvents",
            "resourceId":"394f647d-2ed0-4c31-87c5-389b8c0c8132",
            "time":"20180719:L012620622",
            "category":"NetworkSecurityGroupFlowEvent",
            "systemId":"d8b3b697-5355-40e2-84d2-1bf2f0e0dc4a"
            },
```

Všimněte si, že protokolování probíhá pouze pro pravidla, která mají **protokolování** nastavené na **povoleno**, například:

```syntax
{
    "Tags":  null,
    "ResourceRef":  "/accessControlLists/AllowAll",
    "InstanceId":  "4a63e1a5-3264-4986-9a59-4e77a8b107fa",
    "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
    "ResourceMetadata":  null,
    "ResourceId":  "AllowAll",
    "Properties":  {
                       "ConfigurationState":  null,
                       "ProvisioningState":  "Succeeded",
                       "AclRules":  [
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Inbound",
                                            "InstanceId":  "ba8710a8-0f01-422b-9038-d1f2390645d7",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Inbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "101",
                                                               "Description":  null,
                                                               "Type":  "Inbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        },
                                        {
                                            "ResourceMetadata":  null,
                                            "ResourceRef":  "/accessControlLists/AllowAll/aclRules/AllowAll_Outbound",
                                            "InstanceId":  "068264c6-2186-4dbc-bbe7-f504c6f47fa8",
                                            "Etag":  "W/\"1535a780-0fc8-4bba-a15a-093ecac9b88b\"",
                                            "ResourceId":  "AllowAll_Outbound",
                                            "Properties":  {
                                                               "Protocol":  "All",
                                                               "SourcePortRange":  "0-65535",
                                                               "DestinationPortRange":  "0-65535",
                                                               "Action":  "Allow",
                                                               "SourceAddressPrefix":  "*",
                                                               "DestinationAddressPrefix":  "*",
                                                               "Priority":  "110",
                                                               "Description":  null,
                                                               "Type":  "Outbound",
                                                               "Logging":  "Enabled",
                                                               "ProvisioningState":  "Succeeded"
                                                           }
                                        }
                                    ],
                       "IpConfigurations":  [

                                            ],
                       "Subnets":  [
                                       {
                                           "ResourceMetadata":  null,
                                           "ResourceRef":  "/virtualNetworks/10_0_1_0/subnets/Subnet1",
                                           "InstanceId":  "00000000-0000-0000-0000-000000000000",
                                           "Etag":  null,
                                           "ResourceId":  null,
                                           "Properties":  null
                                       }
                                   ]
                   }
}
```

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Přehled brány firewall Datacenter](../concepts/datacenter-firewall-overview.md)
- [Přehled síťového adaptéru](../concepts/network-controller-overview.md)
- [SDN v Azure Stack HCI a Windows Server](../concepts/software-defined-networking.md)