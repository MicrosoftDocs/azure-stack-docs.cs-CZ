---
title: Použití šablon Azure Resource Manageru ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití šablon Azure Resource Manageru ve službě Azure Stack pro zřízení prostředků.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 2022dbe5-47fd-457d-9af3-6c01688171d7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 11/14/2018
ms.openlocfilehash: fde2936f3b57fdf9fc6ddeda1786b9ffd5aa9fe0
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691518"
---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Použití šablon Azure Resource Manageru ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Šablony Azure Resource Manageru můžete použít k nasazení a zřiďte všechny prostředky pro vaši aplikaci v rámci jediné koordinované operace. Znovu nasadit šablony měnit prostředky ve skupině prostředků.

Tyto šablony se dá nasadit pomocí portálu Microsoft Azure Stack, Powershellu, příkazového řádku a sady Visual Studio.

Následující šablony rychlý Start jsou [k dispozici na Githubu](https://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-server-non-high-availability-deployment"></a>Nasazení serveru SharePoint (nasazení vysoké dostupnosti)

Pomocí prostředí PowerShell [Desired State Configuration](/powershell/dsc/overview/overview) rozšíření (DSC) [Vytvoření farmy serverů SharePoint Server 2013](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sharepoint-2013-non-ha) , který obsahuje následující zdroje:

* Virtuální síť
* 3 účty úložiště
* Dva nástroje pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014
* Jeden virtuální počítač nakonfigurovaný jako farmy služby SharePoint Server 2013 jeden počítač

## <a name="deploy-ad-non-high-availability-deployment"></a>Nasazení AD (bez vysoce – dostupnost – nasazení)

Pomocí rozšíření PowerShell DSC pro [vytvoření serveru řadiče domény AD](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/ad-non-ha) , který obsahuje následující zdroje:

* Virtuální síť
* Jeden účet úložiště
* Jeden nástroj pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou

## <a name="deploy-adsql-non-high-availability-deployment"></a>Nasazení AD/SQL (bez vysoce – dostupnost – nasazení)

Pomocí rozšíření PowerShell DSC pro [vytvořit samostatný server SQL Server 2014](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2014-non-ha) , který obsahuje následující zdroje:

* Virtuální síť
* Dva účty úložiště
* Jeden nástroj pro vyrovnávání zatížení externí
* Jeden virtuální počítač (VM) nakonfigurovaný jako řadič domény v nové doménové struktuře s jednou doménou
* Jeden virtuální počítač nakonfigurovaný jako samostatný server SQL Server 2014

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server

Pomocí rozšíření PowerShell DSC ke konfiguraci existujícího virtuálního počítače místní Configuration Manageru (LCM) a zaregistrovat do Azure Automation DSC o přijetí změn Server účtů.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Vytvoření virtuálního počítače z uživatelské image

[Vytvoření virtuálního počítače z vlastní uživatelská image](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-create-from-customimage). Tato šablona také nasadí virtuální sítě (pomocí DNS), veřejné IP adresy a síťové rozhraní.

## <a name="basic-virtual-machine"></a>Základní virtuální počítač

[Nasazení virtuálního počítače s Windows](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-simple-windows-vm) , který zahrnuje virtuální síť (pomocí DNS), veřejné IP adresy a síťové rozhraní.

## <a name="cancel-a-running-template-deployment"></a>Zrušit běžící šablona nasazení

Chcete-li zrušit spuštěné nasazení šablony, použijte [Stop-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/stop-azurermresourcegroupdeployment) PowerShell [rutiny](/powershell/developer/cmdlet/cmdlet-overview).

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí portálu](azure-stack-deploy-template-portal.md)
* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
* [Přehled Azure Resource Manageru](/azure/azure-resource-manager/resource-group-overview)
