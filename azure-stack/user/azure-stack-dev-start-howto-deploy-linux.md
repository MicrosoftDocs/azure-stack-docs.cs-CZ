---
title: Nasazení virtuálního počítače s Linuxem do Azure stacku | Dokumentace Microsoftu
description: Nasazení aplikace do služby Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 7662b696ce25cdb70f34824984f4d1ce14d88c69
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482036"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack"></a>Nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack

Můžete vytvořit a nasadit základní virtuální počítač s Linuxem pomocí Ubunutu image na webu Marketplace k hostování webové aplikace vytvořené pomocí webové rozhraní. Tento virtuální počítač může hostovat webové aplikace s využitím:

- **Python**. Běžné architektury webových Python zahrnují Flask, Bottle a Django.
- **Go**. Běžné přejít architektury patří Revel, Martini, Gocraft/web a váhou. 
- **Ruby**. K zajištění Ruby webové aplikace můžete nastavit Ruby on Rails jako rozhraní. 
- **Java**. Java lze použít pro vývoj webových aplikací, které jsou odeslány na Apache Tomcat Server. Můžete nainstalovat Tomcat na Linuxu a potom nasazování souborů Java WAR přímo na server. 

Podle pokynů v tomto článku můžete začít pracovat se všemi webové aplikace, rozhraní a back endové technologie, která používá operační systém Linux. Azure Stack můžete potom použít ke správě vaší infrastruktury a nástrojů pro správu v rámci technologie pro zpracování úloh údržby pro vaši aplikaci.

## <a name="deploy-a-linux-vm-for-a-web-app"></a>Nasazení virtuálního počítače s Linuxem pro webovou aplikaci

Vytvoříte tajný klíč, použijte základní image virtuálního počítače s Linuxem, určit konkrétní atributy virtuálního počítače a pak vytvořte virtuální počítač. Po vytvoření virtuálního počítače, otevřete porty, které potřebujete pro práci s virtuálním Počítačem a pro virtuální počítač pro hostování vaší aplikace. Můžete také vytvořit název DNS. Nakonec se připojit k virtuálnímu počítači a aktualizovat počítače pomocí apt-get. Jakmile budete hotovi se jak tohoto článku, budete mít virtuální počítač ve vaší službě Azure Stack připraveni hostovat vaši webovou aplikaci.

Vpravo můžete přejít do pokynů nebo budete chtít, abyste měli jistotu, že máte všechno, co potřebujete na místě.

## <a name="prerequisites-and-considerations"></a>Požadavky a důležité informace

- Budete potřebovat předplatné Azure Stack.
- Vaše předplatné bude potřebovat přístup k **Ubuntu Server 16.04 LTS** bitové kopie. Můžete použít novější verzi image, ale tyto pokyny jsou vytvářeny s použitím 16.04 LTS v úvahu. Pokud není nutné tuto bitovou kopii, obraťte se na váš operátor cloudu k načtení image do služby Azure Stack marketplace.

<!-- Azure Stack specific considerations

### Authentication

Azure Stack works with two identity management services, Azure Active Directory (Azure AD) and Active Directory Federated Services (AD FS).  This section addresses how this procedure will work with either version.

### Connectivity

Azure Stack can be run in connected to completely disconnected scenarios. This section addresses considerations about the use case in relation to connectivity.

### Azure Stack Development Kit and Integrated Systems

While the two version of the product are the same product both version behave differently. Call out considerations about either version. 

### Azure Stack version

Place any version specific calls outs. The procedure will contain steps for the latest version. This section will contain call outs for previous version that are still supported. -->

## <a name="deploy-vm-using-the-portal"></a>Nasazení virtuálního počítače pomocí portálu

Vytvořte veřejný klíč SSH pro váš server pomocí aplikace, například Putty. Přístup k portálu Azure Stack, přidejte Ubuntu server. Nastavení sítě a záznam DNS. Připojení k serveru ji aktualizovat.

### <a name="create-your-vm"></a>Vytvoření virtuálního počítače

1. Vytvořte veřejný klíč SSH pro váš server. Další informace najdete v tématu [jak použít veřejný klíč SSH](azure-stack-dev-start-howto-ssh-public-key.md).
2. Otevřete na portálu Azure Stack.
3. Vyberte **vytvořit prostředek** > **Compute** > **Ubuntu Server 16.04 LTS**.

    ![Nasazení webové aplikace na Virtuálním počítači Azure Stack](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. V **vytvoření virtuálního počítače** okně pro **1. Konfigurace základního nastavení**:
    1. Typ **název vašeho virtuálního počítače**.
    1. Vyberte **typ disku virtuálního počítače**– buď **Premium SSD** nebo **standardní HDD**.
    1. Typ vaší **uživatelské jméno**.
    1. Vyberte **typ ověřování** jako **veřejného klíče SSH**.
    1. Načtěte veřejný klíč SSH, který jste vytvořili. Otevřete ho v textovém editoru a zkopírujte klíč a za do **veřejný klíč SSH** pole. Vložit text z `---- BEGIN SSH2 PUBLIC KEY ----` k `---- END SSH2 PUBLIC KEY ----`. Vložte celý blok textu do pole klíčů:
       ```text  
            ---- BEGIN SSH2 PUBLIC KEY ----
            Comment: "rsa-key-20190207"
            <Your key block>
            ---- END SSH2 PUBLIC KEY ----```
    1. Select your Azure Stack subscription.
    1. Create a new **Resource group** or use an existing depending on how you want to organize the resources for your app.
    1. Select your location. The ASDK is usually in a **local** region. The location will depend on your Azure Stack.
1. For **2. Size** type:
    - Select the size of data and RAM for your VM that is available in your Azure Stack.
    - You can either browse the list or filter for the size of your VM by **Compute type**, **CPUs**, and storage space.
    - Prices presented are estimates in your local currency that include only Azure infrastructure costs and any discounts for the subscription and location. The prices don't include any applicable software costs. Recommended sizes are determined by the publisher of the selected image based on hardware and software requirements.
    - Using a standard disk rather than a premium disk could impact operating system performance.

1. in **3. Configure optional** features type:
    1. For **High availability,** you can select an availability set. To provide redundancy to your application, you can group two or more virtual machines in an availability set. This configuration ensures that during a planned or unplanned maintenance event, at least one virtual machine will be available and meet the 99.95% Azure SLA. The availability set of a virtual machine can't be changed after it is created.
    1. For **Storage** select **Premium disks (SSD)** or **Standard disks (HDD)**. Premium disks (SSD) are backed by solid-state drives and offer consistent, low-latency performance. They provide the best balance between price and performance, and are ideal for I/O-intensive applications and production workloads. Standard disks (HDD) are backed by magnetic drives and are preferable for applications where data is accessed infrequently. Zone- redundant disks are backed by Zone redundant storage (ZRS) that replicates your data across multiple zones and are available even if a single zone is down. 
    1. You can select **Use managed disks**. Enable this feature to have Azure automatically manage the availability of disks to provide data redundancy and fault tolerance, without creating and managing storage accounts on your own. Managed disks may not be available in all regions. For more information, see [Introduction to Azure managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).
    1. Select **virtual network** to configure your network. Virtual networks are logically isolated from each other in Azure. You can configure their IP address ranges, subnets, route tables, gateways, and security settings, much like a traditional network in your data center. Virtual machines in the same virtual network can access each other by default. 
    1. Select **subnet** to configure your subnet. A subnet is a range of IP addresses in your virtual network, which can be used to isolate virtual machines from each other or from the Internet. 
    1. Select **Public IP address** to configure access to your VM or services running on your VM. Use a public IP address if you want to communicate with the virtual machine from outside the virtual network. 
    1. Select **Network Security Group**, **Basic, or **Advanced**. Set rules that allow or deny network traffic to the VM 
    1. Select **public inbound ports** to set access for common or custom protocols to your VM. The service specifies the destination protocol and port range for this rule. You can choose a predefined service, like RDP or SSH, or provide a custom port range.  
        For the  web server, you are going to want to HTTP (80), HTTPS (443), and SSH (22) open. If you plan on managing the machine with an RDP connection, open port 3389.
    1. Select **Extensions** if you would like to add Extension to your VM. Extensions add new features, like configuration management or antivirus protection, to your virtual machine using extensions. 
    1. Disable or enable **Monitoring**. Capture serial console output and screenshots of the virtual machine running on a host to help diagnose startup issues. 
    1. Select **diagnostics storage account** to specify the storage account holding your metrics. Metrics are written to a storage account so you can analyze them with your own tools. . 
    1. Select **OK**.
1. Review **4. Summary**:
    - The portal validates your settings.
    - You can download the Azure Resource Manager template for your VM if you would like to reuse your settings with an Azure Resource Manager workflow.
    - Press **OK** when the validation has passed. The deployment of the VM takes several minutes.

### Specify the open ports and DNS name

You will want to make your web app accessible to users on your network by opening the ports used to connect to the machine and adding a friendly DNS name such as `mywebapp.local.cloudapp.azurestack.external` that users can use in their web browsers.

#### Open inbound ports

You can modify the destination protocol and port range for predefined service, like RDP or SSH or provide a custom port range. For example, you may want to work with the port range of your web framework. GO, for instance, communicates on port 3000.

1. Open the Azure Stack portal for your tenant.
1. Find your VM. You may have pinned the VM to your dashboard, or you can search for the VM in the **Search resources** box.
1. Select **Networking** in your VM blade.
1. Select **Add inbound port** rule to open a port.
1. For Source, leave the default to **Any**.
1. For Source port range, leave the wildcard (*).
1. For Destination port range, add the port you would like to open, such as `3000`.
1. For **Protocol** leave **Any**.
1. For **Action** set to **Allow**.
1. For **Priority** leave for the default.
1. Type a **Name** and **Description** to help you remember why the port is open.
1. Select **Add**.

#### Add a DNS name for your server

In addition, you can create a DNS name for your server, and then users can connect to your web site using a URL.

1. Open the Azure Stack portal for your tenant.
1. Find your VM. You may have pinned the VM to your dashboard, or you can search for the VM in the **Search resources** box.
1. Select **Overview**.
1. Select **Configure** under VM.
1. Select **Dynamic** for **Assignment**.
1. Type the DNS name label such as `mywebapp` so that your full URL will be: `mywebapp.local.cloudapp.azurestack.external` (for an ASDK app).

### Connect via SSH to update your VM

1. On the same network as your Azure Stack, open your SSH client. For more information, see [How to use an SSH public key](azure-stack-dev-start-howto-ssh-public-key.md).
1. Type:
    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

<!--

## Deploy VM using the PowerShell

Include a sentence or two to explain only what is needed to complete the procedure.

1. Step one of the procedures.

    | Parameter | Example | Description |
    | --- | --- | --- |
    | item      | "dog"   | Describe what it is and where to find the information. |

2. Step two of the procedure

    ```PowerShell  
    verb-command -item "dog"
    ```

3. Step three of the procedures.

    ```PowerShell  
    verb-command -item "dog"
    ```

4. Step four of the procedures.

    ```PowerShell  
    verb-command -item "dog"
    ```

## Deploy VM using the CLI

Include a sentence or two to explain only what is needed to complete the procedure.

1. Step one of the procedures.

    | Parameter | Example | Description |
    | --- | --- | --- |
    | item      | "dog"   | Describe what it is and where to find the information. |

2. Step two of the procedure

    ```CLI  
    verb-command -item "dog"
    ```

3. Step three of the procedures.

    ```CLI  
    verb-command -item "dog"
    ```

4. Step four of the procedures.

    ```CLI  
    verb-command -item "dog"
    ```

-->

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md)