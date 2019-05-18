---
title: Jak použít veřejný klíč SSH pomocí služby Azure Stack | Dokumentace Microsoftu
description: Jak použít veřejný klíč SSH
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: c0b55579c5103c7bb1073546243dbfcc0b700b4a
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838394"
---
# <a name="use-an-ssh-public-key"></a>Použít veřejný klíč SSH

Používat otevřené připojení SSH z vývojového počítače k serveru virtuálního počítače ve vaší instanci služby Azure Stack, který je hostitelem webové aplikace, můžete potřebovat k vytvoření Secure Shell (SSH) pár veřejného a privátního klíče. 

V tomto článku vytvoříte klíče a budete je moct používat pro připojení k serveru. Klient SSH slouží k získání příkazový řádek bash na serveru s Linuxem nebo používat klienta Secure FTP (SFTP) přesunout soubory do a ze serveru.

## <a name="create-an-ssh-public-key-on-windows"></a>Vytvoření veřejného klíče SSH ve Windows

V této části použijete k vytvoření soukromého klíče pro použití při vytváření zabezpečené připojení do počítače s Linuxem ve vaší instanci služby Azure Stack a veřejný klíč SSH PuTTY Key Generator. PuTTY je bezplatný emulátor terminálu, který umožňuje připojení k serveru přes SSH a Telnet.

1. [Stáhněte a nainstalujte PuTTY pro váš počítač.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Otevřete PuTTY Key Generator.

    ![PuTTY Key generátor prázdné pole klíče](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. V části **parametry**vyberte **RSA**.

1. V **počet bitů v vygenerovaný klíč** zadejte **2048**.  

1. Vyberte **generovat**.

1. V **klíč** oblasti, vytvořte několik náhodných znaků tím, že přesunutím ukazatele myši na prázdnou oblast.

    ![PuTTY generátor klíč vyplněný pole klíče](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Zadejte **přístupové heslo klíče** a potvrďte ho **Potvrdit heslo** pole. Poznámka: heslo pro pozdější použití.

1. Vyberte **uložit veřejný klíč**a uložte ho do umístění, kam budete mít přístup.

1. Vyberte **uložit privátní klíč**a uložte ho do umístění, kam budete mít přístup. Mějte na paměti, že patří s veřejným klíčem.

Veřejný klíč uložený v textovém souboru, který jste uložili. Text bude vypadat nějak takto:

```text  
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "rsa-key-20190330"
THISISANEXAMPLEDONOTUSE AAAAB3NzaC1yc2EAAAABJQAAAQEAthW2CinpqhXq
9uSa8/lSH7tLelMXnFljSrJIcpxp3MlHlYVbjHHoKfpvQek8DwKdOUcFIEzuStfT
Z8eUI1s5ZXkACudML68qQT8R0cmcFBGNY20K9ZMz/kZkCEbN80DJ+UnWgjdXKLvD
Dwl9aQwNc7W/WCuZtWPazee95PzAShPefGZ87Jp0OCxKaGYZ7UXMrCethwfVumvU
aj+aPsSThXncgVQUhSf/1IoRtnGOiZoktVvt0TIlhxDrHKHU/aZueaFXYqpxDLIs
BvpmONCSR3YnyUtgWV27N6zC7U1OBdmv7TN6M7g01uOYQKI/GQ==
---- END SSH2 PUBLIC KEY ----
```

Pokud aplikace požaduje klíč, zkopírujte a vložte celý obsah textového souboru.

## <a name="connect-with-ssh-by-using-putty"></a>Připojení přes SSH pomocí PuTTY

Když instalujete klienta PuTTY, máte generátor klíče PuTTY a klienta SSH. V této části otevřete klienta SSH, PuTTY a nakonfigurujte hodnoty připojení a klíč SSH. Pokud jste ve stejné síti jako vaše instance služby Azure Stack, připojíte se k virtuálnímu počítači.

Než připojíte, budete potřebovat:
- PuTTY
- IP adresa a uživatelské jméno pro počítač s Linuxem v Azure stacku instanci, která používá veřejný klíč SSH jako typ ověřování.
- Port 22 otevřen pro tento počítač.
- Veřejný klíč SSH, který jste použili, když jste vytvořili na počítači.
- Klientský počítač, na kterém běží PuTTY být ve stejné síti jako vaše instance služby Azure Stack.

1. Otevřete PuTTY.

    ![V podokně Konfigurace PuTTY](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. V **název hostitele (nebo IP adresa)** zadejte uživatelské jméno a veřejnou IP adresu počítače (například **username@192.XXX.XXX.XX**). 
3. Ověřit, zda **Port** je **22** a **typ připojení** je **SSH**.
4. V **kategorie** stromu, rozbalte **SSH** a **Auth**.

    ![V podokně Konfigurace PuTTY - SSH privátní klíč](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Vedle položky **soubor privátního klíče pro ověřování** vyberte **Procházet**a pak vyhledejte soubor privátního klíče (*\<název souboru > .ppk*) vaše veřejných a soukromého klíče.
6. V **kategorie** stromu, vyberte **relace**.

    ![Do pole "Uložit relací" podokně Konfigurace PuTTY](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. V části **uložit relací**, zadejte název pro relaci a pak vyberte **Uložit**.
8. V **uložit relací** vyberte název relace a pak vyberte **zatížení**.
9. Vyberte **Open** (Otevřít). Otevře relaci SSH.

## <a name="connect-with-sftp-with-filezilla"></a>Připojení pomocí protokolu SFTP pomocí Filezilly

Přesunout soubory do a z počítač s Linuxem, můžete použít Filezilly, klient FTP, který podporuje zabezpečení FTP (SFTP). Filezilly běží na Windows 10, Linuxu a macOS. Klient Filezilly podporuje FTP, FTP přes protokol TLS (FTPS) a SFTP. Je open source softwaru, který je distribuován podle podmínek licence GNU General Public License bezplatně.

### <a name="set-your-connection"></a>Nastavit připojení

1. [Stáhněte a nainstalujte Filezilly](https://filezilla-project.org/download.php).
1. Otevřete Filezilly.
1. Vyberte **souboru** > **správce webu**.

    ![V podokně Správce Filezilly webu](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. V **protokol** rozevíracího seznamu vyberte **SFTP - SSH File Transfer Protocol**.
1. V **hostitele** zadejte veřejnou IP adresu pro váš počítač.
1. V **typ přihlášení** vyberte **normální**.
1. Zadejte svoje uživatelské jméno a heslo.
1. Vyberte **OK**.
1. Vyberte **upravit** > **nastavení**.

    ![V podokně nastavení Filezilly](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. V **vyberte stránku** stromu, rozbalte **připojení**a pak vyberte **SFTP**.
1. Vyberte **přidat soubor klíče**a pak zadejte svůj soubor privátního klíče (například  *\<název souboru > .ppk*).
1. Vyberte **OK**.

### <a name="open-your-connection"></a>Otevřete připojení

1. Otevřete Filezilly.
1. Vyberte **souboru** > **správce webu**.
1. Vyberte název vašeho webu a pak vyberte **připojit**.

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nastavení vývojového prostředí ve službě Azure Stack](azure-stack-dev-start.md).
