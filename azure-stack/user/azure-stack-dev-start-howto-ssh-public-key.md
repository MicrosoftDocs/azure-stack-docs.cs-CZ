---
title: Jak používat veřejný klíč SSH s Azure Stack hub
description: Jak používat veřejný klíč SSH
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 859fe3b64b2dc21a07df1431f2da5abade3894f0
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704212"
---
# <a name="use-an-ssh-public-key"></a>Použití veřejného klíče SSH

Pokud chcete použít otevřené připojení SSH z vývojového počítače k VIRTUÁLNÍmu počítači serveru ve vaší instanci centra Azure Stack, který hostuje vaši webovou aplikaci, možná budete muset vytvořit dvojici veřejného a privátního klíče Secure Shell (SSH). 

V tomto článku vytvoříte klíče a pak je použijete pro připojení k vašemu serveru. Klienta SSH můžete použít k získání výzvy bash na serveru Linux nebo k přesunutí souborů na server a ze serveru pomocí zabezpečeného klienta FTP (SFTP).

## <a name="create-an-ssh-public-key-on-windows"></a>Vytvoření veřejného klíče SSH ve Windows

V této části použijete generátor klíčů pro tvorbu a vytvoříte veřejný pár klíčů SSH a privátního klíče, který použijete při vytváření zabezpečeného připojení k počítačům se systémem Linux ve vaší instanci centra Azure Stack. K dispozici je bezplatný emulátor terminálu, který vám umožní připojit se k serveru přes SSH a Telnet.

1. [Stáhněte a nainstalujte si pro svůj počítač výstup výstupu.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Otevřít generátor klíčů pro výstup

    ![Generátor klíčů pro výstupy s prázdným polem klíče](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. V části **parametry**vyberte **RSA**.

1. Do pole **počet bitů v generovaném klíči** zadejte **2048**.  

1. Vyberte **Generovat**.

1. V oblasti **klíč** vygenerujte některé náhodné znaky přesunutím kurzoru na prázdnou oblast.

    ![Generátor klíčů pro výstupy s naplněný polem klíčů](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. Zadejte **klíčové heslo** a potvrďte ho v poli **Potvrdit heslo** . Poznamenejte si heslo pro pozdější použití.

1. Vyberte **Uložit veřejný klíč**a uložte ho do umístění, kde k němu máte přístup.

1. Vyberte **Uložit privátní klíč**a uložte ho do umístění, kde k němu máte přístup. Mějte na paměti, že patří k veřejnému klíči.

Veřejný klíč je uložený v textovém souboru, který jste uložili. Text vypadá následovně:

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

Když aplikace požádá o klíč, zkopírujete a vložíte celý obsah textového souboru.

## <a name="connect-with-ssh-by-using-putty"></a>Připojení pomocí SSH pomocí výstupu

Při instalaci výstupu obsahuje generátor klíčů pro výstup i klienta SSH. V této části otevřete klienta SSH, zadáte a nakonfigurujete hodnoty připojení a klíč SSH. Pokud jste ve stejné síti, jako je Azure Stack hub instance, připojíte se k VIRTUÁLNÍmu počítači.

Než se připojíte, budete potřebovat:
- PuTTY
- IP adresa a uživatelské jméno pro počítač se systémem Linux ve vaší instanci centra Azure Stack, který jako typ ověřování používá veřejný klíč SSH.
- Port 22, který se má pro počítač otevřít.
- Veřejný klíč SSH, který jste použili při vytváření počítače.
- Klientský počítač, který spouští výstup, se nachází ve stejné síti jako vaše instance centra Azure Stack.

1. Otevřete PuTTY.

    ![Podokno Konfigurace výstupu](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. Do pole **název hostitele (nebo IP adresa)** zadejte uživatelské jméno a veřejnou IP adresu počítače (například **username@192.XXX.XXX.XX** ). 
3. Ověřte, zda je port **22** a zda je **Typ připojení** **SSH**.
4. Ve stromové struktuře **kategorie** rozbalte položku **SSH** a **auth**.

    ![Konfigurační podokno pro výstupy do výstupu – privátní klíč SSH](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. Vedle pole **soubor privátního klíče pro ověřování** vyberte **Procházet**a vyhledejte soubor privátního klíče ( *\<filename >. ppk*) vašeho páru veřejného a privátního klíče.
6. Ve stromové struktuře **kategorie** vyberte možnost **relace**.

    ![Pole "uložené relace" v podokně Konfigurace výstupu](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. V části **uložené relace**zadejte název relace a pak vyberte **Uložit**.
8. V seznamu **uložené relace** vyberte název relace a pak vyberte **načíst**.
9. Vyberte **Open** (Otevřít). Otevře se relace SSH.

## <a name="connect-with-sftp-with-filezilla"></a>Připojení pomocí SFTP pomocí FileZilly

Chcete-li přesunout soubory do a z počítače se systémem Linux, můžete použít FileZilly klienta FTP, který podporuje zabezpečený protokol FTP (SFTP). FileZilly běží ve Windows 10, Linux a macOS. Klient FileZilly podporuje FTP, FTP přes TLS (FTPS) a SFTP. Je to open source software, který je distribuován zdarma za podmínek Obecné veřejné licence GNU.

### <a name="set-your-connection"></a>Nastavit připojení

1. [Stáhněte a nainstalujte FileZilly](https://filezilla-project.org/download.php).
1. Otevřete FileZilly.
1. Vyberte **soubor** > **Site Manager**.

    ![Podokno Site Manager FileZilly](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. V rozevíracím seznamu **protokol** vyberte **SFTP-SSH Protokol FTP (File Transfer Protocol)** .
1. Do pole **hostitel** zadejte veřejnou IP adresu pro váš počítač.
1. V poli **typ přihlášení** vyberte **normální**.
1. Zadejte své uživatelské jméno a heslo.
1. Vyberte **OK**.
1. Vyberte **Upravit** **Nastavení** > .

    ![Podokno nastavení FileZilly](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. Ve stromu pro **Výběr stránky** rozbalte položku **připojení**a pak vyberte **SFTP**.
1. Vyberte **Přidat soubor klíče**a pak zadejte svůj soubor privátního klíče (například *\<filename >. ppk*).
1. Vyberte **OK**.

### <a name="open-your-connection"></a>Otevřete připojení

1. Otevřete FileZilly.
1. Vyberte **soubor** > **Site Manager**.
1. Vyberte název vaší lokality a pak vyberte **připojit**.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nastavit vývojové prostředí v Azure Stack hub](azure-stack-dev-start.md).
