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
ms.openlocfilehash: ca8d81e9d0baf08adde257340b2a8ac3bffd6c36
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783190"
---
# <a name="how-to-use-an-ssh-public-key"></a>Jak použít veřejný klíč SSH

Budete muset vytvořit veřejných a privátních pár klíčů SSH používat otevřené připojení SSH z vývojového počítače a serveru virtuálního počítače ve službě Azure Stack, který je hostitelem webové aplikace. Tento článek vás provede kroky pro získání klíčů a použití klíče pro připojení k serveru. Můžete použít klienta SSH na serveru s Linuxem získat příkazový řádek bash nebo klientem SFTP pro přesun souborů do a ze serveru.

## <a name="create-an-ssh-public-key-on-windows"></a>Vytvoření veřejného klíče SSH ve Windows

V této části použijete generátor klíče PuTTY Chcete-li vytvořit veřejný klíč SSH a soukromého klíče pro použití při vytváření zabezpečené připojení do počítače s Linuxem v Azure stacku. PuTTY je bezplatný emulátor terminálu, který umožňuje připojení k serveru přes SSH a Telnet.

1. [Stáhněte a nainstalujte PuTTY pro váš počítač.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Otevřete PuTTY Key Generator.

1. Nastavte **parametry** k **RSA**.

1. Nastavte počet bitů v vygenerovaný klíč k `2048`.  

    ![Použití klienta PuTTY k vygenerování veřejný klíč SSH](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. Vyberte **generovat**. V **klíč** oblasti generovat některé náhodnost přesunutím kurzoru na prázdnou oblast.

1. Přidat **přístupové heslo klíče** a potvrďte ho **potvrdit** pole. Poznamenejte si heslo.
    ![Použití klienta PuTTY k vygenerování veřejný klíč SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Vyberte **uložit veřejný klíč** a uložte do umístění, kam budete mít přístup.

1. Vyberte **uložit privátní klíč** a uložte do umístění, kde lze k němu přístup a mějte na paměti, že patří s veřejným klíčem.

Veřejný klíč uložený v textovém souboru, který jste uložili. Pokud otevřete, bude obsahovat text, který bude vypadat takto:

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

Při použití veřejného klíče, zkopírujete a vložíte celý kontexty do textového pole jako hodnotu, když aplikace požádá o klíč.

## <a name="connect-with-ssh-using-putty"></a>Připojení přes SSH pomocí PuTTY

Pokud jste nainstalovali PuTTY, máte generátor klíče a klienta SSH. Otevřete klienta SSH PuTTY, nakonfigurujte hodnoty připojení a klíč SSH a pokud jste ve stejné síti jako Azure Stack, připojte se ke svému virtuálnímu počítači.

Než připojíte, budete potřebovat:
- PuTTY
- IP adresa a uživatelské jméno pro počítač s Linuxem ve vaší službě Azure Stack, která používá veřejného klíče SSH jako typ ověřování.
- Port 22, musí být otevřené pro počítač.
- Veřejný klíč SSH, který jste použili při vytváření počítače.
- Klientský počítač s PuTTY být ve stejné síti jako Azure Stack.

### <a name="connect-via-ssh-with-putty"></a>Připojení pomocí protokolu SSH pomocí PuTTy

1. Otevřete Putty.

    ![Použití klienta PuTTY k připojení](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Přidejte uživatelské jméno a veřejnou IP adresu počítače. Například `username@192.XXX.XXX.XX` pro **název hostitele**. 
3. Ověřit, zda **Port** `22` nastavena a **typ připojení** je nastavena na `SSH`.
4. Rozbalte **SSH** > **Auth** v **kategorie** stromu.

    ![Privátní klíč SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Vyberte **Procházet** a vyhledejte svůj soubor privátního klíče (filename.ppk) veřejné a soukromé dvojice klíčů.
6. Vyberte relaci ve stromové struktuře kategorie.

    ![Veřejný klíč SSH s privátním klíčem](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. Zadejte název pro relaci v rámci **uložit relací** a vyberte **Uložit**.
8. Vyberte název relace a **zatížení**.
9. Vyberte **Open** (Otevřít). Otevře se relace SSH.

## <a name="connect-with-sftp-with-filezilla"></a>Připojení pomocí protokolu SFTP pomocí Filezilly

Jako klienta FTP, který podporuje SFTP pro přesun souborů do a z počítač s Linuxem můžete použít Filezilly. Filezilly běží na Windows 10, Linuxu a macOS. Klient Filezilly podporuje FTP, ale také FTP přes protokol TLS (FTPS) a SFTP. Je open source software distribuován zdarma v rámci podmínek licence GNU General Public License.

### <a name="set-your-connection"></a>Nastavit připojení

1. [Stáhněte a nainstalujte Filezilly](https://filezilla-project.org/download.php).
1. Otevřete Filezilly.
1. Vyberte **souboru** > **správce webu**.

    ![Veřejný klíč SSH s privátním klíčem](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. Vyberte **SFTP - SSH File Transfer Protocol** pro **protokol**.
1. Přidejte veřejnou IP adresu pro váš počítač v **hostitele** pole.
1. Vyberte **normální** pro **přihlášení typu**.
1. Přidejte uživatelské jméno a heslo.
1. Vyberte **OK**.
1. Vyberte **upravit** > **nastavení**.

    ![Veřejný klíč SSH s privátním klíčem](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Rozbalte **připojení** v **vyberte stránku** stromu. Vyberte **SFTP**.
1. Vyberte **přidat soubor klíče** a přidejte svůj soubor privátního klíče, jako je například filename.ppk.
1. Vyberte **OK**.

### <a name="open-your-connection"></a>Otevřete připojení

1. Otevřete Filezilly.
1. Vyberte **souboru** > **správce webu**.
1. Vyberte název vašeho webu a vyberte **připojit**.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [vývoj pro Azure Stack](azure-stack-dev-start.md)