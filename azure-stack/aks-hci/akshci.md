---
title: Modul prostředí PowerShell pro AksHci
description: Naučte se používat příkazy modulu AksHci ke správě AKS na Azure Stack HCI.
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 8d6b3410fb6fe6a00d602eeda97e172f1bac4062
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873663"
---
# <a name="akshci"></a>AksHci 

Příkazy pro interakci se službou Azure Kubernetes v Azure Stack HCI

## <a name="akshci-cmdlets"></a>Rutiny AksHci

|         |            |
| ------- | ---------- |
| [Get-akshcicluster](get-akshcicluster.md) | Vypíše nasazené clustery včetně hostitele služby Azure Kubernetes. |
| [Get-akshciclusterupgrades](get-akshciclusterupgrades.md) | Získejte dostupné upgrady pro cluster služby Azure Kubernetes. |
| [Get-akshciconfig](get-akshciconfig.md) | Vypíše aktuální nastavení konfigurace pro hostitele služby Azure Kubernetes. |
| [Get-akshcicredential](get-akshcicredential.md) | Přístup ke clusteru pomocí kubectl. |
| [Get-akshcieventlog](get-akshcieventlog.md) | Načte všechny protokoly událostí z modulu PowerShellu AKS HCI. |
| [Get-akshcikubernetesversion](get-akshcikubernetesversion.md) | Vypíše dostupné verze pro vytvoření spravovaného clusteru Kubernetes. |
| [Get-akshcilogs](get-akshcilogs.md) | Vytvořte složku zip s protokoly ze všech lusků. |
| [Get-akshciupdates](get-akshciupdates.md) | Vypíše dostupné aktualizace pro službu Azure Kubernetes v Azure Stack HCI. |
| [Get-akshciversion](get-akshciversion.md) | Získejte aktuální verzi služby Azure Kubernetes Service na Azure Stack HCL. |
| [Get-akshcivmsize](get-akshcivmsize.md) | Vypíše podporované velikosti virtuálních počítačů. |
| [inicializovat – akshcinode](initialize-akshcinode.md) | Spuštěním kontrol na každém fyzickém uzlu zjistíte, jestli jsou splněné všechny požadavky k instalaci služby Azure Kubernetes na Azure Stack HCL. |
| [Install-akshci](install-akshci.md) | Nainstalujte službu Azure Kubernetes na Azure Stack agenti, služby a hostitele HCI. |
| [Install-akshciadauth](install-akshciadauth.md) | Nainstalujte ověřování služby Active Directory. |
| [Install-akshciarconboarding](install-akshciarconboarding.md) | Stáhněte a nainstalujte kubectl a nástroj příkazového řádku Kubernetes. |
| [New-akshcicluster](new-akshcicluster.md) | Vytvořte nový spravovaný cluster Kubernetes. |
| [New-akshcinetworksetting](new-akshcinetworksetting.md) | Vytvořte objekt pro novou virtuální síť. |
| [Remove-akshcicluster](remove-akshcicluster.md) | Odstraní spravovaný cluster Kubernetes. |
| [restart – akshci](restart-akshci.md) | Restartujte službu Azure Kubernetes na Azure Stack HCI a odeberte všechny nasazené clustery Kubernetes. |
| [Set-akshciclusternodecount](set-akshciclusternodecount.md) | Škálujte počet uzlů řídicí plochy nebo uzlů pracovních uzlů v clusteru. |
| [Set-akshciconfig](Sset-akshciconfig.md) | Nastaví nebo aktualizuje nastavení konfigurace pro hostitele služby Azure Kubernetes. |
| [Odinstalace – akshci](uninstall-akshci.md) | Odeberte službu Azure Kubernetes v Azure Stack HCL. |
| [Odinstalace – akshciadauth](uninstall-akshciadauth.md) | Odeberte ověřování služby Active Directory. |
| [Update – akshci](update-akshci.md) | Aktualizujte hostitele služby Azure Kubernetes na nejnovější verzi Kubernetes. |
| [Update – akshcicluster](update-akshcicluster.md) | Aktualizujte spravovaný cluster Kubernetes na novější verzi Kubernetes nebo operačního systému. |

