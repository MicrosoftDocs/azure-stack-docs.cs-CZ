---
title: Rychlý Start pro nastavení hostitele služby Azure Kubernetes na Azure Stack HCI pomocí prostředí Windows PowerShell
description: Přečtěte si, jak nastavit hostitele služby Azure Kubernetes na Azure Stack HCL pomocí Windows PowerShellu.
author: jessicaguan
ms.topic: quickstart
ms.date: 02/12/2021
ms.author: jeguan
ms.openlocfilehash: 291dd37ec08b6fe724dfdecd064454b8d96fd5f3
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116695"
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

Stáhněte si `AKS-HCI-Public-Preview-Feb-2021` ze [služby Azure Kubernetes na stránce registrace HCL Azure Stack](https://aka.ms/AKS-HCI-Evaluate). Soubor zip `AksHci.Powershell.zip` obsahuje modul PowerShellu.

**Zavřete všechna okna PowerShellu.** Odstraňte všechny existující adresáře pro AksHci, AksHci. Day2, kVA, Course a MSK8sDownloadAgent umístěné v cestě `%systemdrive%\program files\windowspowershell\modules` . Až to uděláte, můžete extrahovat obsah nového souboru ZIP. Přesvědčte se, zda je soubor zip extrahován ve správném umístění ( `%systemdrive%\program files\windowspowershell\modules` ).

   ```powershell
   Import-Module AksHci
   ```

**Zavřete všechna okna prostředí PowerShell** a znovu otevřete novou relaci pro správu, abyste zkontrolovali, jestli máte nejnovější verzi modulu PowerShellu.
  
   ```powershell
   Get-Command -Module AksHci
   ```

 **Výkonem**
```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Alias           Initialize-AksHciNode                              0.2.19     akshci
Function        Get-AksHciCluster                                  0.2.19     akshci
Function        Get-AksHciClusterUpgrades                          0.2.19     akshci
Function        Get-AksHciConfig                                   0.2.19     akshci
Function        Get-AksHciCredential                               0.2.19     akshci
Function        Get-AksHciEventLog                                 0.2.19     akshci
Function        Get-AksHciKubernetesVersion                        0.2.19     akshci
Function        Get-AksHciLogs                                     0.2.19     akshci
Function        Get-AksHciUpdates                                  0.2.19     akshci
Function        Get-AksHciVersion                                  0.2.19     akshci
Function        Get-AksHciVmSize                                   0.2.19     akshci
Function        Install-AksHci                                     0.2.19     akshci
Function        Install-AksHciAdAuth                               0.2.19     akshci
Function        Install-AksHciArcOnboarding                        0.2.19     akshci
Function        New-AksHciCluster                                  0.2.19     akshci
Function        New-AksHciNetworkSetting                           0.2.19     akshci
Function        Remove-AksHciCluster                               0.2.19     akshci
Function        Restart-AksHci                                     0.2.19     akshci
Function        Set-AksHciClusterNodeCount                         0.2.19     akshci
Function        Set-AksHciConfig                                   0.2.19     akshci
Function        Uninstall-AksHci                                   0.2.19     akshci
Function        Uninstall-AksHciAdAuth                             0.2.19     akshci
Function        Uninstall-AksHciArcOnboarding                      0.2.19     akshci
Function        Update-AksHci                                      0.2.19     akshci
Function        Update-AksHciCluster                               0.2.19     akshci
```

Po spuštění výše uvedeného příkazu zavřete všechna okna prostředí PowerShell a znovu otevřete relaci pro správu a spusťte příkazy v následujících krocích.

### <a name="update-your-clusters-to-the-latest-aks-hci-version"></a>Aktualizujte své clustery na nejnovější verzi AKS-HCI.

Pokud máte existující nasazení a chcete ho aktualizovat, postupujte podle tohoto kroku. Pokud nemáte existující nasazení, přeskočte tento krok a přejděte ke kroku 2. Případně, pokud máte nasazení, ale chcete spustit čistou instalaci další verze AKSHCI, spusťte `Uninstall-AksHci` a přejděte ke kroku 2.

Pokud chcete aktualizovat na nejnovější verzi služby Azure Kubernetes v Azure Stack HCI, spusťte příkaz [Update-akshci](./update-akshci.md) . Tento příkaz aktualizuje hostitele služby Azure Kubernetes a místní Microsoft provozovanou cloudovou platformu. Tento příkaz neprovede upgrade Kubernetes a verze operačního systému uzlu Windows ve stávajících clusterech úloh. Nové clustery úloh vytvořené po aktualizaci hostitele AKS se budou lišit od stávajících clusterů úloh ve verzi operačního systému Windows a verze Kubernetes.

```powershell
Update-AksHci
```

Doporučujeme aktualizovat clustery úloh hned po aktualizaci clusteru pro správu, aby nedocházelo ke spouštění nepodporovaných verzí operačního systému Windows Server v clusterech Kubernetes s uzly Windows. Pokud jsou clustery úloh v nepodporované verzi, budou i nadále podporovány, ale nebudete moci vytvářet nové uzly. Pokud chcete aktualizovat svůj cluster úloh, přejděte na web [aktualizovat cluster úloh](create-kubernetes-cluster-powershell.md).

## <a name="step-2-prepare-your-machines-for-deployment"></a>Krok 2: Příprava počítačů na nasazení

Spuštěním kontrol na každém fyzickém uzlu zjistíte, jestli jsou splněné všechny požadavky, aby se nainstalovala služba Azure Kubernetes na Azure Stack HCL.
Otevřete PowerShell jako správce a spusťte následující příkaz.

```powershell
Initialize-AksHciNode
```

Po dokončení kontrol se zobrazí text "Hotovo" zobrazený zeleným textem.

## <a name="step-3-create-a-virtual-network"></a>Krok 3: vytvoření virtuální sítě

Pokud chcete získat názvy dostupných externích přepínačů, spusťte tento příkaz:

```powershell
Get-VMSwitch
```

Je nutné použít externí přepínač.

Ukázkový výstup:
```output
Name SwitchType NetAdapterInterfaceDescription
---- ---------- ------------------------------
extSwitch External Mellanox ConnectX-3 Pro Ethernet Adapter
```

Pokud chcete vytvořit virtuální síť pro uzly ve vašem nasazení, které se mají použít, vytvořte proměnnou prostředí pomocí příkazu PowerShellu [New-akshcinetworksetting](.\new-akshcinetworksetting.md) . Použije se později ke konfiguraci nasazení, které používá statickou IP adresu. Pokud chcete nakonfigurovat nasazení AKS pomocí DHCP, přejděte k příklady v [New-akshcinetworksetting](.\new-akshcinetworksetting.md) .

```powershell
#static IP
$vnet = New-AksHciNetworkSetting -vnetName "extSwitch" -k8sNodeIpPoolStart "172.16.10.0" -k8sNodeIpPoolEnd "172.16.10.255" -vipPoolStart "172.16.255.0" -vipPoolEnd
"172.16.255.254" -ipAddressPrefix "172.16.0.0/16" -gateway "172.16.0.1" -dnsServers "172.16.0.1"
```

> [!NOTE]
> Hodnoty uvedené v tomto ukázkovém příkazu se budou muset přizpůsobit pro vaše prostředí.

## <a name="step-4-configure-your-deployment"></a>Krok 4: konfigurace nasazení

Pomocí příkazu [set-akshciconfig](./set-akshciconfig.md) nastavte konfigurační nastavení pro hostitele služby Azure Kubernetes. **Pokud nasazujete v uzlu 2-4 Azure Stack clusteru HCI nebo v clusteru s podporou převzetí služeb při selhání s Windows serverem 2019 Datacenter, musíte zadat `imageDir` `cloudConfigLocation` parametry a.** Pokud chcete resetovat podrobnosti konfigurace, spusťte příkaz znovu s novými parametry.

Nakonfigurujte nasazení pomocí následujícího příkazu.

```powershell
Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16" 
```

> [!NOTE]
> Hodnota `-cloudservicecidr` zadaná v tomto ukázkovém příkazu se bude muset přizpůsobit pro vaše prostředí.

Použijte `-enableDiagnosticData` parametr pro vyjádření souhlasu s odesláním požadovaných diagnostických dat společnosti Microsoft. Pokud tento parametr nepoužijete, budete vyzváni k povolení diagnostických dat při `Set-AksHciConfig` spuštění. `Install-AksHci` selže, Pokud nepřijmete výzvu k zadání souhlasu, když `Set-AksHciConfig` ho prezentuje.

## <a name="step-5-start-a-new-deployment"></a>Krok 5: zahájení nového nasazení

Až nakonfigurujete nasazení, musíte ho spustit. Tím se nainstaluje služba Azure Kubernetes na Azure Stack agenti a služby HCI a hostitele služby Azure Kubernetes.

Nasazení zahájíte spuštěním následujícího příkazu.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Ověření nasazeného hostitele služby Azure Kubernetes

Pokud chcete zajistit, aby byl nasazený hostitel služby Azure Kubernetes, spusťte příkaz [Get-akshcicluster](./get-akshcicluster.md) . Po nasazení budete moci získat clustery s Kubernetes pomocí stejného příkazu.

```powershell
Get-AksHciCluster
```

**Výkonem**
```

Name            : clustergroup-management
Version         : v1.18.10
Control Planes  : 1
Linux Workers   : 0
Windows Workers : 0
Phase           : provisioned
Ready           : True
```

## <a name="step-6-access-your-clusters-using-kubectl"></a>Krok 6: přístup ke clusterům pomocí kubectl

Pokud chcete k hostiteli služby Azure Kubernetes přistupovat pomocí `kubectl` , spusťte příkaz [Get-akshcicredential](./get-akshcicredential.md) . Tato funkce použije zadaný soubor _kubeconfig_ clusteru jako výchozí soubor _kubeconfig_ pro `kubectl` . Tento příkaz můžete použít také k přístupu k dalším clusterům Kubernetes po jejich nasazení.

```powershell
Get-AksHciCredential -name clustergroup-management
```

## <a name="get-logs"></a>Získání protokolů

Pokud chcete získat protokoly ze všech lusků, spusťte příkaz [Get-akshcilogs](./get-akshcilogs.md) . Tento příkaz vytvoří výstupní složku zip s názvem `akshcilogs` v cestě `c:\%workingdirectory%\%AKS HCI release number%\%filename%` (například `c:\AksHci\0.9.6.0\akshcilogs.zip` ).

```powershell
Get-AksHciLogs
```

## <a name="update-to-the-latest-version-of-azure-kubernetes-service-on-azure-stack-hci"></a>Aktualizace na nejnovější verzi služby Azure Kubernetes ve Azure Stack HCI

Pokud chcete aktualizovat na nejnovější verzi služby Azure Kubernetes v Azure Stack HCI, spusťte příkaz [Update-akshci](./update-akshci.md) . Příkaz Update funguje pouze v případě, že jste nainstalovali verzi nástroje OCT nebo novější. Nebude fungovat pro vydání starší verze než v říjnu. Tento příkaz Update aktualizuje hostitele služby Azure Kubernetes a místní Microsoft provozovanou cloudovou platformu. Tento příkaz neupgraduje žádné existující clustery úloh. Nové clustery úloh vytvořené po aktualizaci hostitele AKS se budou lišit od stávajících clusterů úloh ve verzi operačního systému Windows a verze Kubernetes.

```powershell
Update-AksHci
```
   
Doporučujeme aktualizovat clustery úloh hned po aktualizaci clusteru pro správu, aby nedocházelo ke spouštění nepodporovaných verzí operačního systému Windows Server v clusterech Kubernetes s uzly Windows. Pokud jsou clustery úloh v nepodporované verzi, budou i nadále podporovány, ale nebudete moci vytvářet nové uzly. Pokud chcete aktualizovat svůj cluster úloh, přejděte na web [aktualizovat cluster úloh](create-kubernetes-cluster-powershell.md).

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

Po spuštění výše uvedeného příkazu můžete změnit nastavení konfigurace pomocí následujícího příkazu. Parametry zůstávají stejné, jak je popsáno v kroku 3. 

```powershell
Set-AksHciConfig -imageDir c:\clusterstorage\volume1\Images -cloudConfigLocation c:\clusterstorage\volume1\Config -vnet $vnet -enableDiagnosticData -cloudservicecidr "172.16.10.10/16" 
```

Po změně konfigurace na požadovaná nastavení spusťte následující příkaz, který přeinstaluje Azure Stack Kubernetes na Azure Stack HCI.

```powershell
Install-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Odebrat službu Azure Kubernetes v Azure Stack HCI


Pokud chcete odebrat službu Azure Kubernetes v Azure Stack HCI, spusťte následující příkaz. Tento příkaz odebere starou konfiguraci a po přeinstalaci bude nutné spustit `Set-AksHciConfig` znovu.

```powershell
Uninstall-AksHci
```

Pokud chcete zachovat starou konfiguraci, spusťte následující příkaz.

```powershell
Uninstall-AksHci -SkipConfigCleanup
```

## <a name="next-steps"></a>Další kroky

- [Vytvoření clusteru Kubernetes pro vaše aplikace](create-kubernetes-cluster-powershell.md)
