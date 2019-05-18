---
title: Nasazení Java WAR do virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení Java WAR do virtuálního počítače ve službě Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 1738d106a0688518f7a739d3fb02ec1b16c2b8b9
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838370"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Nasazení webové aplikace v Javě do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač (VM) k hostování vaší webové aplikace v Pythonu ve službě Azure Stack. V tomto článku instalace a konfigurace serveru Apache Tomcat na virtuálním počítači Linux ve službě Azure Stack. Pak načíst soubor Java webové aplikace prostředků (WAR) do serveru. Soubor WAR se používá k distribuci kolekce souborů archivu (JAR), Java, komprimované soubory, které obsahují prostředky v Javě jako jsou třídy, text, obrázky, XML a HTML a další prostředky, které se používají k doručování webových aplikací.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavení virtuálního počítače ve službě Azure Stack podle pokynů v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně sítě virtuálních počítačů Ujistěte se, že jsou dostupné následující porty:

    | Port | Protocol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol, který slouží k doručování webových stránkách od serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresu. |
    | 443 | HTTPS | Protokol zabezpečení HTTPS (Hypertext Transfer) je zabezpečený verzi protokolu HTTP, který vyžaduje certifikát zabezpečení a umožňuje šifrovaného přenosu informací. |
    | 22 | SSH | Secure Shell (SSH) je protokol šifrovaných sítí pro zabezpečenou komunikaci. Pomocí tohoto připojení klienta SSH pro konfiguraci virtuálního počítače a nasaďte aplikaci. |
    | 3389 | Protokol RDP | Volitelné. Protokol RDP (Remote Desktop) umožňuje připojení ke vzdálené ploše na pomocí grafického uživatelského rozhraní na svém počítači.   |
    | 8080 | Vlastní | Výchozí port pro službu Apache Tomcat. Pro produkční server směrovat provoz přes 80 a 443. |

## <a name="install-java"></a>Nainstalovat Java

1. Připojení k vašemu virtuálnímu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

2. Na příkazovém řádku bash ve virtuálním počítači spusťte následující příkaz:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Ověření instalace. Stále připojeni k virtuálnímu počítači v relaci SSH, spusťte následující příkaz:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Instalace a konfigurace Tomcat

1. Připojení k vašemu virtuálnímu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Vytvoření uživatele Tomcat následujícím způsobem:

    a. Vytvořte novou skupinu Tomcat spuštěním následujícího příkazu:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Vytvoření nového uživatele Tomcat. Přidání tohoto uživatele do skupiny Tomcat s domovskému adresáři */opt/tomcat*. Tomcat je nasadit do tohoto adresáře:

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. Tomcat nainstalujte následujícím způsobem:

    a. Získat adresu URL pro cíl pro nejnovější verzi Tomcat 8 z [stránku pro stažení Tomcat 8](http://tomcat.apache.org/download-80.cgi).

    b. Používáme nástroj cURL k stažení nejnovější verze pomocí odkazu. Spusťte následující příkazy:

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. Tomcat na instalaci */opt/tomcat* adresáře. Vytvořte složku a pak otevřete archivu:

    ```bash  
        sudo mkdir /opt/tomcat
        sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
        sudo chown -R tomcat webapps/ work/ temp/ logs/
    ```

1. Aktualizujte oprávnění pro Tomcat spuštěním následujících příkazů:

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

1. Vytvoření *systemd* služby souboru, tak, aby Tomcat může běžet jako služba.

   a. Tomcat je potřeba vědět, kam jste nainstalovali Java. Tato cesta je obvykle označuje jako *JAVA_HOME*. Vyhledejte umístění spuštěním:

    ```bash  
        sudo update-java-alternatives -l
    ```

    To vytváří přibližně takto:

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    Můžete vytvořit *JAVA_HOME* hodnotu proměnné s ohledem cestě z výstupu a přidáním */jre*. Jako příklad použijeme předchozí příklad, */usr/lib/jvm/java-1.8.0-openjdk-amd64/jre*.

    b. Použijte hodnotu z vašeho serveru k vytvoření souboru definice služby systemd:

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. Vložte následující obsah do souboru služby. Změnit hodnotu *JAVA_HOME*, pokud je to nezbytné, aby odpovídala hodnotě nalezen ve vašem systému. Můžete také upravit nastavení přidělení paměti, které jsou určené v CATALINA_OPTS:

    ```Text  
        [Unit]
        Description=Apache Tomcat Web Application Container
        After=network.target
        
        [Service]
        Type=forking
        
        Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
        Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
        Environment=CATALINA_HOME=/opt/tomcat
        Environment=CATALINA_BASE=/opt/tomcat
        Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
        Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'
        
        ExecStart=/opt/tomcat/bin/startup.sh
        ExecStop=/opt/tomcat/bin/shutdown.sh
    
        User=tomcat
        Group=tomcat
        UMask=0007
        RestartSec=10
        Restart=always
        
        [Install]
        WantedBy=multi-user.target
    ```

    d. Uložte soubor a zavřete ho.

    e. Znovu načte démona systemd tak, aby věděl o souboru služby:

    ```bash  
        sudo systemctl daemon-reload
    ```

    f. Spusťte službu Tomcat: 

    ```bash  
        sudo systemctl start tomcat
    ```

    g. Ověřte, zda je spuštěna bez chyb tak, že zadáte:

    ```bash  
        sudo systemctl status tomcat
    ```

1. Ověřte Tomcat server. Tomcat používá port 8080 tak, aby přijímal konvenční požadavky. Povolení provozu na daný port spuštěním následujícího příkazu:

    ```bash  
        sudo ufw allow 8080
    ```

    Pokud jste nepřidali *příchozí pravidla portů* pro váš virtuální počítač Azure Stack je nyní přidat. Další informace najdete v tématu [vytvoření virtuálního počítače](#create-a-vm).

1. Otevřete prohlížeč ve stejné síti jako služba Azure Stack a pak otevřete váš server *yourmachine.local.cloudapp.azurestack.external:8080*.

    ![Apache Tomcat v Azure stacku virtuálního počítače](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Načtení stránky Apache Tomcat na vašem serveru. Dále je nutné nakonfigurovat server k tomu, abyste pro přístup k stav serveru, aplikace správce a správce hostitele.

1. Povolení souboru definice služby, který Tomcat automaticky spustí po restartování serveru:

    ```bash  
        sudo systemctl enable tomcat
    ```

1. Sami povolit přístup k rozhraní webové správy, konfigurace serveru Tomcat. 

   a. Upravit *tomcat users.xml* souborů a definování role a uživatele tak, aby se můžete přihlásit. Definování uživatelského přístupu k `manager-gui` a `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. Přidejte následující prvky, které mají `<tomcat-users>` části:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    Konečný soubor může například vypadat přibližně jako:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
        <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    c. Uložte soubor a zavřete ho.

1. Tomcat omezuje přístup *správce* a *správce hostitele* aplikace k připojení ze serveru. Vzhledem k tomu, že instalujete Tomcat na virtuálním počítači ve službě Azure Stack, budete chtít odebrat toto omezení. Omezení podle IP adresy na tyto aplikace změnit úpravou odpovídající *context.xml* soubory.

    a. Aktualizace *context.xml* v aplikaci správce:

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. Okomentujte omezení IP adresy umožňují připojení z libovolného místa, nebo přidat IP adresu počítače, který používáte pro připojení k Tomcat.

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. Uložte soubor a zavřete ho.

    d. Aktualizovat *context.xml* aplikace Správce hostitele s podobné aktualizace:

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    e. Uložte soubor a zavřete ho.

1. Aktualizace serveru se změnami, restartujte službu Tomcat:

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Otevřete v prohlížeči ve stejné síti jako služba Azure Stack a pak otevřete serveru: *yourmachine.local.cloudapp.azurestack.external:8080*.

    a. Chcete-li zkontrolovat stav serveru Tomcat a ověřte, že máte přístup, vyberte **stav serveru**.

    b. Přihlaste se pomocí svých přihlašovacích údajů Tomcat.

    ![Apache Tomcat v Azure stacku virtuálního počítače](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Vytvoření nové aplikace

Bude potřeba vytvořit soubor WAR nasadit Tomcat. Pokud chcete jenom zkontrolovat vaše prostředí, najdete příklad WAR na [serveru Apache Tomcat](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Pokyny týkající se vývoje aplikací v Javě v Azure najdete v tématu [sestavování a nasazování aplikací v Javě v Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Připojení k vašemu virtuálnímu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Pokud chcete aktualizovat server s balíčkem aplikace, zastavte službu Tomcat:

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Aby bylo možné zapisovat do složky webapps, přidejte do skupiny Tomcat vašich uživatelů serveru FTP. Uživatel FTP je uživatel, kterého můžete definovat při vytváření virtuálního počítače ve službě Azure Stack.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. Vymazat složce webové aplikace a pak načíst nové nebo aktualizované WAR, připojte se ke svému virtuálnímu počítači pomocí Filezilly. Pokyny najdete v tématu [připojit pomocí protokolu SFTP pomocí Filezilly](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    a. Vymazat *TOMCAT_HOME/webapps*.

    b. Přidat vaše WAR do *TOMCAT_HOME/webapps* (například */opt/tomcat/webapps/*).

1.  Tomcat automaticky rozbalí a nasadí aplikaci. Můžete ho zobrazit pomocí názvu DNS, který jste vytvořili dříve. Příklad:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [develop for Azure Stack](azure-stack-dev-start.md).
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).
- To learn the Java programming language and find additional resources for Java, see [Java.com](https://www.java.com).
