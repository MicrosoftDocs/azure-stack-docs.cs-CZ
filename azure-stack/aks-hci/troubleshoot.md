---
title: Řešení potíží s AKS
description: Tento článek poskytuje informace o řešení potíží s Azure Kubernetes Service (AKS) na Azure Stack HCI.
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 26edd1f52b5a3d695fa70493606c1e2438feda78
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557085"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Řešení potíží se službou Azure Kubernetes v Azure Stack HCI

Když vytváříte nebo spravujete cluster Kubernetes pomocí služby Azure Kubernetes v Azure Stack HCI, může občas docházet k problémům. Tento článek popisuje pokyny pro řešení potíží, které vám pomohou tyto problémy vyřešit.

## <a name="troubleshooting-azure-stack-hci"></a>Řešení potíží Azure Stack HCI
Řešení potíží s vytvářením sestav ověření clusteru pro síťové služby a QoS úložiště (Quality of Service) v rámci serverů v Azure Stack clusteru HCI a ověření, že jsou definována důležitá pravidla, najdete v tématu [řešení potíží s vytvářením sestav ověření clusteru](../hci/manage/validate-qos.md).

Další informace o řešení potíží s zprostředkovatelem CredSSP najdete v tématu [řešení potíží se zprostředkovatelem CredSSP](../hci/manage/troubleshoot-credssp.md).

## <a name="troubleshooting-windows-admin-center"></a>Řešení potíží s centrem pro správu systému Windows
Tento produkt je ve verzi Public Preview, což znamená, že je stále ve vývoji. V současnosti dochází k nějakým problémům s rozšířením Azure Kubernetes Service Center pro správu Windows: 
* V současné době každý server v clusteru systému, který používáte k nastavení služby Azure Kubernetes v Azure Stack HCI, musí být důvěryhodný server. Proto centrum pro správu systému Windows musí být schopné spouštět operace CredSSP na každém serveru v clusteru, nikoli jenom na jednom nebo několika z nich. 
* Pokud se zobrazí chyba, která říká `msft.sme.aks couldn't load` , že se chyba při načítání bloků dat nezdařila, použijte nejnovější verzi Microsoft Edge nebo Google Chrome a zkuste to znovu.
* Než spustíte Průvodce nastavením hostitele služby Azure Kubernetes nebo Průvodce vytvořením clusteru Kubernetes, měli byste se přihlásit k Azure prostřednictvím centra pro správu Windows. Možná se budete muset přihlásit znovu během pracovního postupu. Pokud máte potíže s přihlášením k Azure prostřednictvím centra pro správu Windows, zkuste se přihlásit ke svému účtu Azure z jiného zdroje, jako je [Azure Portal](https://portal.azure.com/). Pokud budete mít i nadále problémy, podívejte se na článek o [známých problémech centra pro správu systému Windows](/windows-server/manage/windows-admin-center/support/known-issues) předtím, než se obrátíte na podporu.
* V aktuální iteraci služby Azure Kubernetes při nasazení Azure Stack HCI prostřednictvím centra pro správu Windows může vytvořit clustery Kubernetes v systému jenom uživatel, který je nastavený jako hostitel služby Azure Kubernetes. Pokud chcete tento problém obejít, zkopírujte složku. WSSD z profilu uživatele, který hostuje službu Azure Kubernetes, do profilu uživatele, který bude vytvářet nový cluster Kubernetes.
* Pokud se zobrazí chyba v průvodci o nesprávnou konfiguraci, proveďte operace vyčištění clusteru. Tyto operace mohou zahrnovat odebrání souboru C:\Program Files\AksHci\mocctl.exe.
* V případě úspěšného fungování zprostředkovatele CredSSP v Průvodci vytvořením clusteru musí být nainstalováno centrum pro správu systému Windows a používáno stejným účtem. Pokud nainstalujete centrum pro správu Windows s jedním účtem a zkusíte ho použít s jiným účtem, zobrazí se chyby.
* Během nasazování clusteru můžete narazit na problém s helm.zip přenos souborů. Tento problém často způsobuje chybu, která říká, že cesta k souboru helm.zip neexistuje nebo není platná. Pokud chcete tento problém vyřešit, zkuste nasazení zopakovat.
* Pokud vaše nasazení přestane trvat delší dobu, můžete mít problémy se zprostředkovatelem CredSSP nebo s připojením. Při řešení potíží s nasazením zkuste použít tento postup: 
    1.  V počítači, na kterém běží centrum pro správu Windows, spusťte v okně PowerShellu následující příkaz: 
          ```PowerShell
          Enter-PSSession <servername>
          ```
    2.  V případě úspěšného provedení tohoto příkazu se můžete připojit k serveru a nedochází k žádnému problému s připojením.
    
    3.  Pokud máte problémy s CredSSP, spusťte tento příkaz pro otestování vztahu důvěryhodnosti mezi počítačem brány a cílovým počítačem: 
          ```PowerShell
          Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
          ``` 
        K otestování vztahu důvěryhodnosti při přístupu k místní bráně můžete také spustit následující příkaz: 
          ```PowerShell
          Enter-PSSession -computer localhost -credential (Get-Credential)
          ``` 
* Pokud používáte Azure ARC a máte několik ID tenantů, spusťte následující příkaz, abyste před nasazením určili požadovaného tenanta. Pokud to neuděláte, může dojít k selhání nasazení.

   ```Azure CLI
   az login –tenant <tenant>
   ```
* Pokud jste právě vytvořili nový účet Azure a ještě jste se k účtu přihlásili na počítači brány, může docházet k potížím při registraci brány centra pro správu Windows pomocí Azure. Pokud chcete tento problém zmírnit, přihlaste se ke svému účtu Azure na jiné kartě nebo v okně prohlížeče a pak zaregistrujte bránu centra pro správu Windows do Azure.

### <a name="creating-windows-admin-center-logs"></a>Vytváření protokolů centra pro správu Windows
Když nahlásíte problémy s centrem pro správu systému Windows, je vhodné připojit protokoly, aby mohl vývojový tým diagnostikovat váš problém. Chyby v centru pro správu Windows se obecně přidávají v jednom ze dvou forem: 
- Události, které se zobrazí v prohlížeči událostí na počítači, na kterém běží centrum pro správu Windows 
- JavaScriptové problémy, které jsou Surface v konzole prohlížeče 

Pokud chcete shromažďovat protokoly pro centrum pro správu Windows, použijte skript Get-SMEUILogs.ps1, který je k dispozici v balíčku Public Preview. 
 
Chcete-li použít skript, spusťte tento příkaz ve složce, ve které je uložen skript: 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
Příkaz má následující parametry:
 
* `-ComputerNames`: Seznam počítačů, ze kterých chcete shromažďovat protokoly.
* `-Destination`: Počítač, do kterého chcete protokoly agregovat.
* `-HoursAgo`: Čas spuštění shromažďování protokolů vyjádřený v hodinách před časem spuštění skriptu.
* `-NoCredentialPrompt`: Přepínač pro vypnutí výzvy k zadání přihlašovacích údajů a používání výchozích přihlašovacích údajů ve vašem aktuálním prostředí.
 
Pokud máte potíže s spuštěním tohoto skriptu, můžete spustit následující příkaz pro zobrazení textu v nápovědě: 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Řešení potíží s pracovními uzly Windows 
Pokud se chcete přihlásit k pracovnímu uzlu Windows pomocí SSH, nejdřív Získejte IP adresu vašeho uzlu spuštěním `kubectl get` a zachyťte `EXTERNAL-IP` hodnotu.

[!NOTE] 
Musíte předat správné umístění k privátnímu klíči SSH. Následující příklad používá výchozí umístění%systemdrive%\akshci \. SSH \ akshci_rsa, ale pokud jste si vyžádali jinou cestu zadáním `-sshPublicKey` parametru pro, může být nutné toto umístění změnit `Set-AksHciConfig` .

Chcete-li získat IP adresu uzlu Windows Worker:  

```
kubectl --kubeconfig=yourkubeconfig get nodes -o wide
```  

Použijte `ssh Administrator@ip` pro SSH v systému Windows:  

```
ssh -i $env:SYSTEMDRIVE\AksHci\.ssh\akshci_rsa administrator@<IP Address of the Node>
```
  
Po přihlášení k uzlu přes SSH můžete spustit `net user administrator *` , aby se aktualizovalo heslo správce. 


## <a name="troubleshooting-linux-worker-nodes"></a>Řešení potíží s pracovními uzly Linux 
Pokud se chcete přihlásit k pracovnímu uzlu Linux pomocí protokolu SSH, nejprve získejte IP adresu vašeho uzlu spuštěním `kubectl get` a zachyťte `EXTERNAL-IP` hodnotu.

[!NOTE]
Musíte předat správné umístění k privátnímu klíči SSH. Následující příklad používá výchozí umístění%systemdrive%\akshci \. SSH \ akshci_rsa, ale pokud jste si vyžádali jinou cestu zadáním `-sshPublicKey` parametru pro, může být nutné toto umístění změnit `Set-AksHciConfig` .

Získání IP adresy uzlu Linux Worker:  

```
kubectl --kubeconfig=yourkubeconfig get nodes -o wide
```  

Použijte `ssh clouduser@ip` k SSH v systému pro uzel Linux: 

```
ssh -i $env:SYSTEMDRIVE\AksHci\.ssh\akshci_rsa clouduser@<IP Address of the Node>
```  

Po přihlášení k uzlu přes SSH můžete spustit `net user administrator *` , aby se aktualizovalo heslo správce. 

## <a name="troubleshooting-azure-arc-kubernetes"></a>Řešení potíží s Kubernetes ARC Azure
Další informace o řešení běžných scénářů týkajících se připojení, oprávnění a agentů ARC najdete v tématu [věnovaném řešení potíží s Kubernetes ARC Azure](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>Další kroky
Pokud budete při používání služby Azure Kubernetes v Azure Stack HCI nadále pracovat s problémy, můžete zaprotokolovat chyby prostřednictvím [GitHubu](https://aka.ms/aks-hci-issues).
