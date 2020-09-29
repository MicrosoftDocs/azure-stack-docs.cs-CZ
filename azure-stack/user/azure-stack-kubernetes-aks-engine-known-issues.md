---
title: Známé problémy s modulem AKS v centru Azure Stack
description: Přečtěte si o známých problémech s použitím modulu AKS v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 09/11/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/11/2020
ms.openlocfilehash: 685cf02aed8e6e485d596531c37653f496a4bc5f
ms.sourcegitcommit: a845ae0d3794b5d845b2ae712baa7e38f3011a7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2020
ms.locfileid: "90045274"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>Známé problémy s modulem AKS v centru Azure Stack

Toto téma popisuje známé problémy modulu AKS v centru Azure Stack.

## <a name="disk-detach-operation-fails-in-aks-engine-0550"></a>Operace odpojení disku v 0.55.0 modulu AKS se nezdařila.

- **Platí pro**: centra Azure Stack (aktualizace 2005), 0.55.0 Engine AKS
- **Popis**: Pokud se pokusíte odstranit nasazení, které obsahuje svazky trvalého uložení, operace odstranění spustí sérii chyb připojení a odpojení. Příčinou je chyba poskytovatele cloudu AKS Engine v 0.55.0. Poskytovatel cloudu volá Azure Resource Manager pomocí novější verze rozhraní API, než Azure Resource Manager aktuálně podporuje v Azure Stackm centru (aktualizace 2005).
- **Náprava**: můžete najít podrobnosti a kroky pro zmírnění rizik v [úložišti GitHub AKS engine (problém 3817)](https://github.com/Azure/aks-engine/issues/3817#issuecomment-691329443). Upgradujte hned po novém sestavení modulu AKS a k dispozici odpovídající image.
- **Výskyt**: při odstraňování nasazení, které obsahuje svazky trvalého uložení.

## <a name="upgrade-issues-in-aks-engine-0510"></a>Problémy s upgradem v AKS Engine 0.51.0

* Během upgradu (upgrade AKS) clusteru Kubernetes z verze 1.15. x na 1.16. x, upgrade následujících komponent Kubernetes vyžaduje další ruční kroky: **Kube-proxy**, **Azure-CNI-networkmonitor**, **CSI-tajné – Store**, **Kubernetes-řídicí panel**. Níže najdete popis toho, co můžete sledovat, a jak tyto problémy obejít.

  * V propojených prostředích není zřejmé, že se jedná o tento problém, protože v clusteru nejsou žádné známky, že se ohrožené součásti neupgradují. Vše se zdá, že funguje podle očekávání.
  <!-- * In disconnected environments, you can see this problem when you run a query for the system pods status and see that the pods for the components mentioned below are not in "Ready" state: -->

    ```bash  
    kubectl get pods -n kube-system
    ```

  * Pokud chcete tento problém vyřešit u každé z těchto součástí, spusťte příkaz ve sloupci alternativní řešení v následující tabulce.

    |Název součásti |Alternativní řešení |Ovlivněné scénáře|
    |---------------|-----------|------------------|
    |**Kube – proxy**     | `kubectl delete ds kube-proxy -n kube-system` |Připojeno, odpojeno |
    |**Azure – CNI – networkmonitor**   | `kubectl delete ds azure-cni-networkmonitor -n kube-system`   | Připojeno, odpojeno |
    |**CSI – tajné klíče – úložiště**  |`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/secrets-store-csi-driver.yaml`<br>`kubectl delete ds csi-secrets-store -n kube-system` | Propojení |
    |**Kubernetes – řídicí panel** |Na každém hlavním uzlu spusťte následující příkaz:<br>`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/kubernetes-dashboard.yaml` |Propojení |

* Kubernetes 1,17 není v této verzi podporován. I když existují žádosti o přijetí změn GitHubu (PR) s odkazem na 1,17, není tato podpora podporovaná.

## <a name="aks-engine-get-versions-command-limitations"></a>omezení příkazů AKS-Engine Get-Versions

Výstup příkazu **AKS-Engine** se `get-versions` týká pouze globálního Azure místo centra Azure Stack. Další informace o různých cestách upgradu najdete v tématu [Postup upgradu na novější verzi Kubernetes](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version).

## <a name="next-steps"></a>Další kroky

[AKS Engine v centru Azure Stack – přehled](azure-stack-kubernetes-aks-engine-overview.md)
