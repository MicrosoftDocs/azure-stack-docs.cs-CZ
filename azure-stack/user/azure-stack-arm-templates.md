---
title: Použití šablon Azure Resource Manager v Azure Stack | Microsoft Docs
description: Naučte se používat šablony Azure Resource Manager v Azure Stack ke zřízení prostředků.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: df7a6e7c42a8863117faaccfd4108c4ffd806a5a
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993762"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Použití šablon Azure Resource Manager v Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Šablony Azure Resource Manager můžete použít k nasazení a zřízení všech prostředků aplikace v rámci jediné koordinované operace. Šablony můžete také znovu nasadit, abyste provedli změny prostředků ve skupině prostředků.

Tyto šablony se dají nasadit pomocí portálu Microsoft Azure Stack, PowerShellu, příkazového řádku a sady Visual Studio.

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

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Pomocí rozšíření PowerShell DSC nakonfigurujte existující místní Configuration Manager virtuálního počítače (LCM) a zaregistrujte ho na serveru vyžádané replikace DSC Azure Automation účtu.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Vytvoření virtuálního počítače z uživatelské image

[Vytvořte virtuální počítač z vlastní image uživatele](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage). Tato šablona také nasadí virtuální síť (s DNS), veřejnou IP adresu a síťové rozhraní.

## <a name="basic-virtual-machine"></a>Základní virtuální počítač

[Nasaďte virtuální počítač s Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) , který obsahuje virtuální síť (s DNS), veřejnou IP adresu a síťové rozhraní.

## <a name="cancel-a-running-template-deployment"></a>Zrušení běžícího nasazení šablony

Pokud chcete zrušit nasazení spuštěné šablony, použijte [rutinu](/powershell/scripting/developer/cmdlet/cmdlet-overview)PowerShellu [stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) .

## <a name="next-steps"></a>Další kroky

* [Nasazení šablon pomocí portálu](azure-stack-deploy-template-portal.md)
* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
* [Přehled Azure Resource Manageru](/azure/azure-resource-manager/resource-group-overview)
