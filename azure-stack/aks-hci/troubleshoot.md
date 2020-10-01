---
title: Poradce při potížích
description: Průvodce odstraňováním potíží se službou Azure Kubernetes v Azure Stack HCI
author: davannaw-msft
ms.topic: how-to
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: e30d5ba784efc6453ce161bc2a87db7c728d3fce
ms.sourcegitcommit: 373e9e3e84eaa33331db9f78e52486fbb6beb907
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91592928"
---
# <a name="troubleshooting-azure-kubernetes-service-on-azure-stack-hci"></a>Řešení potíží se službou Azure Kubernetes v Azure Stack HCI

Když vytváříte nebo spravujete cluster Kubernetes pomocí služby Azure Kubernetes Service v Azure Stack HCI, může občas docházet k problémům. Níže jsou uvedené pokyny pro řešení potíží, které vám pomohou tyto problémy vyřešit. 

## <a name="troubleshooting-azure-stack-hci"></a>Řešení potíží Azure Stack HCI
Pokud chcete řešit potíže s vytvářením sestav ověření clusteru pro síť a QoS úložiště (Quality of Service) na serverech v Azure Stack clusteru HCI a ověřte, jestli jsou definovaná důležitá pravidla, přečtěte si téma [řešení potíží s vytvářením sestav ověření clusteru](/azure-stack/hci/manage/validate-qos).

Řešení potíží se zprostředkovatelem CredSSP najdete v tématu [řešení potíží se zprostředkovatelem CredSSP](/azure-stack/hci/manage/troubleshoot-credssp).

## <a name="troubleshooting-windows-admin-center"></a>Řešení potíží s centrem pro správu systému Windows
Tento produkt je aktuálně ve stavu Public Preview, což znamená, že stále probíhá vývoj. V současné době existuje několik problémů s rozšířením Azure Kubernetes Service Center pro správu Windows: 
* V současné době každý server v clusteru systému, který používáte k nastavení služby Azure Kubernetes, na Azure Stack HCI musí být důvěryhodný server. To znamená, že centrum pro správu systému Windows musí být schopné provádět operace CredSSP na každém serveru v clusteru, nikoli jenom v jednom nebo několika z nich. 
* Pokud narazíte na chybu `msft.sme.aks couldn't load` a zobrazí se chyba při načítání bloků dat selhala, použijte nejnovější verzi Microsoft Edge nebo Google Chrome a zkuste to znovu.
* Před spuštěním Průvodce nastavením hostitele služby Azure Kubernetes nebo Průvodce vytvořením clusteru Kubernetes byste se měli přihlásit k Azure prostřednictvím centra pro správu Windows. Během pracovního postupu může být vyžadováno opakované podepisování. Pokud máte v centru pro správu Windows problémy s přihlášením k Azure, zkuste se k účtu Azure přihlásit z jiného zdroje, jako je [Azure Portal](https://portal.azure.com/). Pokud i nadále dochází k potížím, Projděte si článek o [známých problémech centra pro správu systému Windows](/windows-server/manage/windows-admin-center/support/known-issues) , než se dostanete k podpoře.
* V aktuální iteraci služby Azure Kubernetes při nasazení Azure Stack HCI prostřednictvím centra pro správu Windows může vytvořit clustery Kubernetes v systému jenom uživatel, který je nastavený jako hostitel služby Azure Kubernetes. Pokud chcete tento problém obejít, zkopírujte `.wssd` složku z profilu uživatele, který nastavil hostitele služby Azure Kubernetes na profil uživatele, který bude spouštět nový cluster Kubernetes.
* Pokud se zobrazí chyba v průvodci o nesprávnou konfiguraci, proveďte operace vyčištění clusteru. To může zahrnovat odebrání `C:\Program Files\AksHci\mocctl.exe` souboru.
* Aby CredSSP mohl úspěšně fungovat v rámci Průvodce vytvořením clusteru, musí být nainstalováno centrum pro správu Windows a používat ho stejným účtem. Instalace s jedním účtem a jeho pokusem o jeho použití s jiným účtem bude mít za následek chyby.
* Během nasazování clusteru může dojít k potížím s helm.zip přenos souborů. To často vede k chybě, která říká, že cesta k souboru helm.zip neexistuje nebo není platná. Pokud chcete tento problém vyřešit, vraťte se zpátky a zkuste nasazení zopakovat.
* Pokud vaše nasazení přestane trvat delší dobu, můžete mít problémy CredSSP nebo připojení. Při řešení potíží s nasazením vyzkoušejte následující kroky: 
    1.  V počítači se spuštěným WAC spusťte v okně PowerShellu následující příkaz: 
    ```PowerShell
    Enter-PSSession <servername>
    ```
    2.  V případě úspěšného provedení tohoto příkazu se můžete připojit k serveru a nedochází k potížím s připojením.
    
    3.  Pokud máte problémy s CredSSP, spusťte tento příkaz pro otestování vztahu důvěryhodnosti mezi počítačem brány a cílovým počítačem: 
    ```PowerShell
    Enter-PSSession –ComputerName <server> –Credential company\administrator –Authentication CredSSP
    ``` 
    K otestování vztahu důvěryhodnosti při přístupu k místní bráně můžete také spustit následující příkaz: 
    ```PowerShell
    Enter-PSSession -computer localhost -credential (Get-Credential)
    ``` 
* Pokud používáte Azure ARC a máte několik ID tenantů, spusťte následující příkaz, abyste před nasazením určili požadovaného tenanta. V takovém případě může dojít k selhání nasazení.

```Azure CLI
az login –tenant <tenant>
```
* Pokud jste právě vytvořili nový účet Azure a přihlásili jste se k účtu na počítači brány, může docházet k potížím s registrací brány WAC v Azure. Pokud chcete tento problém zmírnit, přihlaste se k účtu Azure na jiné kartě nebo v okně prohlížeče a pak zaregistrujte bránu WAC do Azure.

### <a name="creating-windows-admin-center-logs"></a>Vytváření protokolů centra pro správu Windows
Při hlášení problémů v centru pro správu Windows je užitečné připojit protokoly, aby mohl vývojový tým diagnostikovat váš problém. Chyby v centru pro správu Windows se obecně nacházejí ve dvou formách: události, které se zobrazují v prohlížeči událostí na počítači, na kterém běží centrum pro správu Windows, nebo JavaScript, který se nachází v konzole prohlížeče. Pokud chcete shromažďovat protokoly pro centrum pro správu Windows, použijte `Get-SMEUILogs.ps1` skript uvedený v balíčku Public Preview. 
 
Chcete-li použít skript, spusťte následující příkaz v adresáři souboru, ve kterém je uložen váš skript: 
 
```PowerShell
./Get-SMEUILogs.ps1 -ComputerNames [comp1, comp2, etc.] -Destination [comp3] -HoursAgo [48] -NoCredentialPrompt
```
 
Příkaz má následující parametry:
 
* **-ComputerNames**: seznam počítačů, ze kterých chcete shromažďovat protokoly.
* **-Destination**: počítač, do kterého chcete protokoly agregovat.
* **-HoursAgo**: Tento parametr definuje počet hodin před modulem runtime skriptu, ze kterého chcete shromažďovat protokoly.
* **-NoCredentialPrompt**: Toto je přepínač pro vypnutí výzvy k zadání přihlašovacích údajů a používání výchozích přihlašovacích údajů ve vašem aktuálním prostředí.
 
Pokud máte potíže s spuštěním tohoto skriptu, můžete spustit následující příkaz pro zobrazení textu v nápovědě: 
 
```PowerShell
GetHelp .\Get-SMEUILogs.ps1 -Examples
```

## <a name="troubleshooting-windows-worker-nodes"></a>Řešení potíží s pracovními uzly Windows 
Chcete-li se přihlásit k pracovnímu uzlu systému Windows, nejprve získejte IP adresu vašeho uzlu spuštěním `kubectl get` a poznamenejte si `EXTERNAL-IP` hodnotu:

```PowerShell
kubectl get nodes -o wide
``` 
Pomocí SSH na uzel použijte `ssh Administrator@ip` . Po přihlášení k uzlu můžete spustit, `net user administrator *` aby se aktualizovalo heslo správce. 

## <a name="troubleshooting-linux-worker-nodes"></a>Řešení potíží s pracovními uzly Linux 
Pokud se chcete přihlásit k pracovnímu uzlu Linux, nejprve získejte IP adresu vašeho uzlu spuštěním `kubectl get` a poznamenejte si `EXTERNAL-IP` hodnotu:

```PowerShell
kubectl get nodes -o wide
``` 
Pomocí SSH na uzel použijte `ssh clouduser@ip` . 

## <a name="troubleshooting-azure-arc-for-kubernetes"></a>Řešení potíží s obloukem Azure ARC pro Kubernetes
Pokud chcete řešit některé běžné scénáře související s připojením, oprávněními a agenty ARC, přejděte na [Kubernetes řešení potíží s podporou Azure ARC](/azure/azure-arc/kubernetes/troubleshooting).

## <a name="next-steps"></a>Další kroky
Pokud během používání služby Azure Kubernetes ve službě Azure Stack HCI budete pokračovat v práci, zastavujte chyby prostřednictvím [GitHubu](https://aka.ms/aks-hci-issues).  
