---
title: Známé problémy pro službu Azure Kubernetes v Azure Stack HCI
description: Známé problémy pro službu Azure Kubernetes v Azure Stack HCI
author: abha
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 36c2d735f3652f4f195b4b9e1dda30fe8bce858c
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96557000"
---
# <a name="known-issues-for-azure-kubernetes-service-on-azure-stack-hci-public-preview"></a>Známé problémy služby Azure Kubernetes v Azure Stack HCI Public Preview
Tento článek popisuje známé problémy s verzí Public Preview služby Azure Kubernetes v Azure Stack HCL.

## <a name="recovering-from-a-failed-aks-on-azure-stack-hci-deployment"></a>Obnovování z neúspěšného AKS nasazení v Azure Stack HCI
Pokud máte problémy s nasazením nebo chcete resetovat nasazení, ujistěte se, že jste před spuštěním Uninstall-AksHci z okna pro správu prostředí PowerShell zavřeli všechny instance centra pro správu systému Windows připojené ke službě Azure Kubernetes na Azure Stack HCL.

## <a name="when-using-kubectl-to-delete-a-node-the-associated-vm-might-not-be-deleted"></a>Při použití kubectl k odstranění uzlu se nemusí přidružený virtuální počítač odstranit.
Tento problém budete splňovat, pokud budete postupovat podle těchto kroků:
* Vytvoření clusteru Kubernetes
* Škálovat cluster na více než dva uzly
* Pro odstranění uzlu použijte kubectl odstranit uzel <název uzlu> 
* Spusťte kubectl Get Nodes. Odebraný uzel není uveden ve výstupu.
* Otevření okna Správce PowerShellu
* Spusťte příkaz Get-VM. Odebraný uzel je stále uveden na seznamu

To vede k systému, který nerozpozná, že uzel chybí, a nový uzel se netočí. Tato akce bude opravena v budoucí verzi.

## <a name="time-synchronization-must-be-configured-across-all-physical-cluster-nodes-and-in-hyper-v"></a>Synchronizace času musí být nakonfigurovaná napříč všemi fyzickými uzly clusteru a Hyper-V.
Aby ověřování gMSA a AD fungovalo, zajistěte, aby byly uzly clusteru Azure Stack HCI nakonfigurované pro synchronizaci času s řadičem domény nebo jiným zdrojem času a aby byla technologie Hyper-V nakonfigurovaná tak, aby synchronizoval čas na všechny virtuální počítače.

## <a name="special-active-directory-permissions-are-needed-for-domain-joined-azure-stack-hci-nodes"></a>Pro připojené k doméně Azure Stackch uzlů HCI se vyžadují speciální oprávnění služby Active Directory. 
Uživatelé, kteří nasazují a konfigurují službu Azure Kubernetes v Azure Stack HCI, musí mít oprávnění "úplné řízení" pro vytváření objektů služby AD v kontejneru služby Active Directory, ve kterém jsou vytvořeny objekty serveru a služby. 

## <a name="get-akshcilogs-command-may-fail"></a>Get-AksHciLogs příkaz se nemusí zdařit.
U velkých clusterů může příkaz Get-AksHciLogs vyvolat výjimku, selže při vytváření výčtu uzlů nebo negeneruje výstupní soubor c:\wssd\wssdlogs.zip.
Důvodem je to, že příkaz prostředí PowerShell pro zip soubor `Compress-Archive` má omezení velikosti výstupního souboru na 2 GB. Tento problém bude opraven v budoucí verzi.

## <a name="azure-kubernetes-service-powershell-deployment-doesnt-check-for-available-memory-before-creating-a-new-target-cluster"></a>Nasazení PowerShellu služby Azure Kubernetes nepřed vytvořením nového cílového clusteru zkontroluje dostupnou paměť.
Příkazy Aks-Hci PowerShellu neověřují dostupnou paměť na hostitelském serveru před vytvořením uzlů Kubernetes. To může vést k vyčerpání paměti a virtuálním počítačům, aby se nespouštěly. Tato chyba se momentálně nezpracovává a nasazení přestane reagovat bez jasné chybové zprávy.
Pokud máte nasazení, které přestane reagovat, otevřete `Eventviewer` a vyhledejte chybové zprávy související s technologií Hyper-V, což znamená, že pro spuštění virtuálního počítače není dostatek paměti.
Tento problém bude opraven v budoucí verzi.

## <a name="azure-kubernetes-service-deployment-fails-on-an-azure-stack-hci-configured-with-static-ips-vlans-sdn-or-proxies"></a>Nasazení služby Azure Kubernetes se nepovede na Azure Stack HCI nakonfigurované se statickými IP adresami, sítěmi VLAN, SDN nebo proxy.
Při nasazování služby Azure Kubernetes v clusteru Azure Stack HCI, který obsahuje statické IP adresy, sítě VLAN, SDN nebo proxy, se nasazení nepovede při vytváření clusteru. Tento problém bude opraven v budoucí verzi.

## <a name="ipv6-must-be-disabled-in-the-hosting-environment"></a>V hostitelském prostředí musí být zakázaný protokol IPv6.
Pokud jsou adresy IPv4 i IPv6 vázané na fyzickou síťovou kartu, `cloudagent` Služba pro Clustering používá IPv6 adresu pro komunikaci. Ostatní komponenty v rámci architektury nasazení používají pouze protokol IPv4. Výsledkem bude, že se centrum pro správu systému Windows nebude moci připojit ke clusteru a při pokusu o připojení k počítači ohlásí selhání vzdálené komunikace.
Alternativní řešení: na fyzických síťových adaptérech zakažte protokol IPv6.
Tento problém bude opraven v budoucí verzi.

## <a name="moving-virtual-machines-between-azure-stack-hci-cluster-nodes-quickly-leads-to-vm-startup-failures"></a>Přesun virtuálních počítačů mezi uzly clusteru Azure Stack HCI rychle vede k selháním při spuštění virtuálních počítačů.
Když pomocí nástroje pro správu clusteru přesunete virtuální počítač z jednoho uzlu (Node A) do jiného uzlu (uzel B) v clusteru Azure Stack HCI, nemusí se virtuální počítač na novém uzlu spustit. Po přesunutí virtuálního počítače zpátky na původní uzel se nepodaří spustit taky.
K tomuto problému dochází, protože logika pro vyčištění první migrace se spouští asynchronně. Výsledkem je, že logika "umístění virtuálního počítače služby Azure Kubernetes" vyhledá virtuální počítač v původním Hyper-V na uzlu A a odstraní ho místo zrušení registrace.
Řešení: Ujistěte se, že se virtuální počítač úspěšně začal na novém uzlu, a potom ho přemístěte zpátky na původní uzel.
Tento problém bude opraven v budoucí verzi.

## <a name="load-balancer-in-azure-kubernetes-service-requires-dhcp-reservation"></a>Load Balancer ve službě Azure Kubernetes vyžaduje rezervaci DHCP.
Řešení vyrovnávání zatížení ve službě Azure Kubernetes v Azure Stack HCI používá protokol DHCP k přiřazování IP adres koncovým bodům služby. Pokud se IP adresa pro koncový bod služby změní kvůli restartování služby, zapůjčení DHCP vyprší kvůli krátké době vypršení platnosti. Služba proto nebude přístupná, protože IP adresa v konfiguraci Kubernetes se liší od toho, co je na koncovém bodu. To může způsobit, že cluster Kubernetes nebude k dispozici.
Pokud se chcete tomuto problému vyhnout, použijte pro koncové body služby Vyrovnávání zatížení fond adres MAC a pro každou adresu MAC ve fondu rezervujte konkrétní IP adresy.
Tento problém bude opraven v budoucí verzi.

## <a name="cannot-deploy-azure-kubernetes-service-to-an-environment-that-has-separate-storage-and-compute-clusters"></a>Nejde nasadit službu Azure Kubernetes do prostředí, které má samostatné úložiště a výpočetní clustery.
Centrum pro správu Windows nebude nasazovat službu Azure Kubernetes do prostředí se samostatným úložištěm a výpočetním clusterem, protože očekává, že výpočetní prostředky a prostředky úložiště budou poskytovány stejným clusterem. Ve většině případů nenalezne CSV vystavené výpočetním clusterem a zamítne pokračovat v nasazení.
Tento problém bude opraven v budoucí verzi.

## <a name="windows-admin-center-only-supports-azure-kubernetes-service-for-azure-stack-hci-in-desktop-mode"></a>Centrum pro správu systému Windows podporuje službu Azure Kubernetes jenom pro Azure Stack HCI v desktopovém režimu.
Ve verzi Preview se všechny funkce služby Azure Kubernetes pro Azure Stack HCL podporují jenom v režimu desktopového centra pro správu systému Windows. Brána centra pro správu systému Windows musí být nainstalovaná na počítači s Windows 10. Další informace o možnostech instalace centra pro správu systému Windows najdete v [dokumentaci centra pro správu systému Windows](/windows-server/manage/windows-admin-center/plan/installation-options). Další scénáře budou podporovány v budoucí verzi.

## <a name="azure-kubernetes-service-host-setup-fails-in-windows-admin-center-if-reboots-are-required"></a>Pokud se vyžaduje restartování, instalace hostitele služby Azure Kubernetes se v centru pro správu systému Windows nezdařila.
Průvodce instalací hostitele služby Azure Kubernetes selže, pokud jeden nebo více serverů, které používáte, se musí restartovat, aby se nainstalovaly role, jako je PowerShell nebo Hyper-V. Aktuálním řešením je ukončit průvodce a potom to zkusit znovu ve stejném systému, až se servery vrátí do režimu online. Tento problém bude opraven v budoucí verzi.

## <a name="azure-registration-step-in-azure-kubernetes-service-host-setup-asks-to-try-again"></a>Krok registrace Azure v nastavení hostitele služby Azure Kubernetes se požádá o akci znovu.
Při použití centra pro správu Windows k nastavení hostitele služby Azure Kubernetes se může zobrazit výzva, abyste se mohli pokusit zkusit znovu po zadání požadovaných informací na stránce pro registraci Azure. Aby bylo možné pokračovat v tomto kroku, možná budete muset znovu přihlásit k Azure v bráně centra pro správu systému Windows. Tento problém bude opraven v budoucí verzi.

## <a name="windows-admin-center-doesnt-have-an-arc-offboarding-experience"></a>Centrum pro správu Windows nemá prostředí pro odpojování ARC.
Centrum pro správu Windows v současné době nemá proces, který by odpojení cluster z ARC Azure. Chcete-li odstranit aganets ARC v clusteru, který byl zničen, přejděte do skupiny prostředků v clusteru Azure Portal a ručně odstraňte obsah ARC. Chcete-li odstranit agenty ARC v clusteru, který je stále v provozu, by uživatelé měli spustit následující příkaz:
```PowerShell
az connectedk8s delete
```

## <a name="when-setting-up-an-azure-kubernetes-service-host-using-windows-admin-center-setup-may-fail-if-file-explorer-is-open"></a>Při nastavování hostitele služby Azure Kubernetes pomocí centra pro správu Windows může selhat instalace, pokud je Průzkumník souborů otevřený.
Pokud je v adresáři **C:\Program Files\AksHci** otevřený Průzkumník souborů a když se přihlásíte ke kroku "zkontrolovat + vytvořit", může se při vytváření selhat chyba "proces nezískal přístup k souboru" C:\Program Files\AksHci\wssdcloudagent.exe ". Důvodem je to, že je používán jiným procesem. Chcete-li se této chybě vyhnout, ukončete Průzkumníka souborů nebo přejděte do jiného adresáře a teprve potom se vraťte do tohoto kroku. 