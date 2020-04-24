---
title: Použití šablon Azure Resource Manager v centru Azure Stack
description: Naučte se používat šablony Azure Resource Manager v centru Azure Stack k zřizování prostředků.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 11/14/2019
ms.openlocfilehash: c41989d662122dafb5d535c356aeb7d7e48ed4aa
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704416"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack-hub"></a>Použití šablon Azure Resource Manager v centru Azure Stack

Šablony Azure Resource Manager můžete použít k nasazení a zřízení všech prostředků aplikace v rámci jediné koordinované operace. Šablony můžete také znovu nasadit, abyste provedli změny prostředků ve skupině prostředků.

Tyto šablony se dají nasadit pomocí portálu centra Microsoft Azure Stack, PowerShellu, příkazového řádku a sady Visual Studio.

Následující šablony pro rychlý Start jsou [k dispozici na GitHubu](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Nasazení serveru SharePoint Server (nasazení bez vysoké dostupnosti)

Pomocí rozšíření [Konfigurace požadovaného stavu](/powershell/scripting/dsc/overview/overview) prostředí PowerShell [vytvořte farmu serveru SharePoint 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) , která obsahuje následující zdroje:

* Virtuální síť
* Tři účty úložiště
* Dva externí nástroje pro vyrovnávání zatížení
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény pro novou doménovou strukturu s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014
* Jeden virtuální počítač nakonfigurovaný jako jeden počítač SharePoint Server 2013 farma

## <a name="deploy-ad-non-high-availability-deployment"></a>Nasazení AD (bez vysoké dostupnosti – nasazení)

Použijte rozšíření PowerShell DSC k [vytvoření serveru řadiče domény služby AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) , který obsahuje následující prostředky:

* Virtuální síť
* Jeden účet úložiště
* Jeden externí nástroj pro vyrovnávání zatížení
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény pro novou doménovou strukturu s jednou doménou

## <a name="deploy-adsql-non-high-availability-deployment"></a>Nasazení AD/SQL (nasazení bez vysoké dostupnosti)

Použijte rozšíření PowerShell DSC k vytvoření samostatného [serveru SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) , který obsahuje následující zdroje informací:

* Virtuální síť
* Dva účty úložiště
* Jeden externí nástroj pro vyrovnávání zatížení
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény pro novou doménovou strukturu s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-rozšíření-Azure-Automation-Pull-Server

Pomocí rozšíření PowerShell DSC nakonfigurujte existující místní Configuration Manager virtuálního počítače (LCM) a zaregistrujte ho na serveru vyžádané replikace DSC Azure Automation účtu.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Vytvoření virtuálního počítače z uživatelské image

[Vytvořte virtuální počítač z vlastní image uživatele](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage). Tato šablona také nasadí virtuální síť (s DNS), veřejnou IP adresu a síťové rozhraní.

## <a name="basic-virtual-machine"></a>Základní virtuální počítač

[Nasaďte virtuální počítač s Windows](https://aka.ms/aa6zdzx) , který obsahuje virtuální síť (s DNS), veřejnou IP adresu a síťové rozhraní.

## <a name="cancel-a-running-template-deployment"></a>Zrušení běžícího nasazení šablony

Pokud chcete zrušit nasazení spuštěné šablony, použijte [rutinu](/powershell/scripting/developer/cmdlet/cmdlet-overview)PowerShellu [stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) .

## <a name="next-steps"></a>Další kroky

* [Nasazení šablon pomocí portálu](azure-stack-deploy-template-portal.md)
* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
* [Přehled Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
