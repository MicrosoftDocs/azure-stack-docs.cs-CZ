---
title: Nasazení aplikace Java WAR na virtuální počítač v centru Azure Stack | Microsoft Docs
description: Nasaďte Java WAR do virtuálního počítače v Azure Stackovém centru.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 7d5ed74f3b62f3cdb926fcf321a7502840e5f453
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536245"
---
# <a name="deploy-a-java-web-app-to-a-vm-in-azure-stack-hub"></a>Nasazení webové aplikace v jazyce Java do virtuálního počítače v Azure Stackovém centru

Můžete vytvořit virtuální počítač, který bude hostovat webovou aplikaci v Pythonu v centru Azure Stack. V tomto článku nainstalujete a nakonfigurujete server Apache Tomcat na virtuálním počítači se systémem Linux v centru Azure Stack. Poté načtete soubor prostředků webové aplikace Java (WAR) do serveru aplikace. Soubor WAR se používá k distribuci kolekce souborů archivu Java (JAR), komprimovaných souborů obsahujících prostředky Java, jako jsou třídy, text, obrázky, XML a další prostředky, které se používají k doručování webové aplikace.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač v centru Azure Stack podle pokynů v tématu [nasazení virtuálního počítače se systémem Linux pro hostování webové aplikace v centru Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V podokně síť virtuálních počítačů se ujistěte, že jsou dostupné tyto porty:

    | Port | Protocol (Protokol) | Popis |
    | --- | --- | --- |
    | 80 | HTTP | HTTP (Hypertext Transfer Protocol) je protokol, který se používá k doručování webových stránek ze serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresou. |
    | 443 | HTTPS | Protokol HTTPS (Hypertext Transfer Protocol Secure) je zabezpečená verze protokolu HTTP, která vyžaduje certifikát zabezpečení a umožňuje šifrovaný přenos informací. |
    | 22 | SSH | Secure Shell (SSH) je zašifrovaný síťový protokol pro zabezpečenou komunikaci. Pomocí tohoto připojení s klientem SSH nakonfigurujete virtuální počítač a nasadíte aplikaci. |
    | 3389 | Protokol RDP | Nepovinný parametr. Protokol RDP (Remote Desktop Protocol) (RDP) umožňuje připojení ke vzdálené ploše pro použití grafického uživatelského rozhraní na vašem počítači.   |
    | 8080 | Vlastní | Výchozí port pro službu Apache Tomcat V případě provozního serveru směrujete provoz mezi 80 a 443. |

## <a name="install-java"></a>Nainstalovat Java

1. Připojte se k VIRTUÁLNÍmu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojení přes SSH pomocí výstupu](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

2. Na příkazovém řádku bash na svém VIRTUÁLNÍm počítači spusťte následující příkaz:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Ověřte instalaci. K VIRTUÁLNÍmu počítači se v relaci SSH pořád připojíte spuštěním následujícího příkazu:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Instalace a konfigurace Tomcat

1. Připojte se k VIRTUÁLNÍmu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojení přes SSH pomocí výstupu](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Pomocí následujícího postupu vytvořte uživatele Tomcat:

    a. Vytvořte novou skupinu Tomcat spuštěním následujícího příkazu:

    ```bash  
        sudo groupadd tomcat
    ```
     
    b. Vytvořte nového uživatele Tomcat. Přidejte tohoto uživatele do skupiny Tomcat pomocí domovského adresáře */opt/Tomcat*. Tomcat nasadíte do tohoto adresáře:

    ```bash  
        sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
    ```

1. Tomcat nainstalujte pomocí následujícího postupu:

    a. Získejte adresu URL pro tar pro nejnovější verzi Tomcat 8 ze [stránky pro stažení Tomcat 8](http://tomcat.apache.org/download-80.cgi).

    b. Pomocí kudrlinkou si můžete stáhnout nejnovější verzi pomocí odkazu. Spusťte následující příkazy:

    ```bash  
        cd /tmp 
        curl -O <URL for the tar for the latest version of Tomcat 8>
    ```

    c. Nainstalujte Tomcat do adresáře */opt/Tomcat* . Vytvořte složku a pak otevřete Archiv:

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

1. Vytvořte soubor se *systémovou* službou, abyste mohli spustit Tomcat jako službu.

   a. Tomcat potřebuje, abyste věděli, kde jste nainstalovali Java. Tato cesta se běžně označuje jako *JAVA_HOME*. Najděte umístění spuštěním:

    ```bash  
        sudo update-java-alternatives -l
    ```

    To vytváří něco podobného jako následující:

    ```Text  
        Output
        java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
    ```

    Hodnotu proměnné *JAVA_HOME* můžete vytvořit tak, že převezmete cestu z výstupu a přidáte */JRE*. Například v předchozím příkladu, */usr/lib/JVM/Java-1.8.0-OpenJDK-amd64/JRE*.

    b. K vytvoření souboru systémové služby použijte hodnotu ze serveru:

    ```bash  
        sudo nano /etc/systemd/system/tomcat.service
    ```

    c. Do souboru služby vložte následující obsah. V případě potřeby upravte hodnotu *JAVA_HOME*tak, aby odpovídala hodnotě, kterou jste v systému našli. Můžete také změnit nastavení přidělení paměti, která jsou určena v CATALINA_OPTS:

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

    e. Znovu načíst systémový démon, který zná informace o souboru služby:

    ```bash  
        sudo systemctl daemon-reload
    ```

    f. Spusťte službu Tomcat: 

    ```bash  
        sudo systemctl start tomcat
    ```

    g. Zadáním těchto akcí ověřte, zda bylo spuštěno bez chyb.

    ```bash  
        sudo systemctl status tomcat
    ```

1. Ověřte server Tomcat. Tomcat používá port 8080 k přijetí konvenčních požadavků. Spuštěním následujícího příkazu povolte provoz na tento port:

    ```bash  
        sudo ufw allow 8080
    ```

    Pokud jste nepřidali *pravidla portů pro příchozí spojení* pro virtuální počítač centra Azure Stack, přidejte je nyní. Další informace najdete v tématu [Vytvoření virtuálního počítače](#create-a-vm).

1. Otevřete prohlížeč ve stejné síti jako centrum Azure Stack a pak otevřete Server, *yourmachine. Local. cloudapp. azurestack. external: 8080*.

    ![Apache Tomcat na virtuálním počítači centra Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Načte se stránka Apache Tomcat na vašem serveru. Dále nakonfigurujete server, který vám umožní přístup k stavu serveru, aplikaci správce a správci hostitele.

1. Povolte soubor služby tak, aby se Tomcat automaticky spouštěla po restartování serveru:

    ```bash  
        sudo systemctl enable tomcat
    ```

1. Pokud chcete umožnění přístupu k rozhraní webové správy, nakonfigurujte server Tomcat. 

   a. Upravte soubor *Tomcat-Users. XML* a definujte roli a uživatele, abyste se mohli přihlásit. Zadejte uživatele pro přístup k `manager-gui` a `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

   b. Do části `<tomcat-users>` přidejte následující prvky:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    Konečný soubor může například vypadat přibližně takto:

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

1. Tomcat omezuje přístup k aplikacím *správce* a *hosta* do připojení přicházejících ze serveru. Vzhledem k tomu, že instalujete Tomcat na virtuální počítač v centru Azure Stack, budete chtít toto omezení odebrat. Upravte omezení IP adres v těchto aplikacích úpravou příslušných souborů *Context. XML* .

    a. Aktualizace *Context. XML* v aplikaci Správce:

    ```bash  
        sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
    ```

    b. Odkomentujte omezení IP adresy a umožněte připojení odkudkoli nebo přidejte IP adresu počítače, který používáte pro připojení k Tomcat.

    ```XML  
    <Context antiResourceLocking="false" privileged="true" >
        <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
    </Context>
    ```

    c. Uložte soubor a zavřete ho.

    d. Aktualizace *Context. XML* aplikace Správce hostitele s podobnou aktualizací:

    ```bash  
        sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
    ```

    e. Uložte soubor a zavřete ho.

1. Chcete-li aktualizovat server se změnami, restartujte službu Tomcat:

    ```bash  
        sudo systemctl restart tomcat
    ```

1. Otevřete prohlížeč ve stejné síti jako centrum Azure Stack a pak otevřete Server: *yourmachine. Local. cloudapp. azurestack. external: 8080*.

    a. Chcete-li zkontrolovat stav serveru Tomcat a ověřit, zda máte přístup, vyberte možnost **stav serveru**.

    b. Přihlaste se pomocí přihlašovacích údajů Tomcat.

    ![Apache Tomcat na virtuálním počítači centra Azure Stack](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Vytvoření aplikace

Pro nasazení do Tomcat budete muset vytvořit WAR. Pokud chcete pouze ověřit prostředí, najdete příklad WAR na [webu Apache Tomcat](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Pokyny k vývoji aplikací v jazyce Java v Azure najdete v tématu [sestavování a nasazování aplikací v jazyce Java v Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Připojte se k VIRTUÁLNÍmu počítači pomocí klienta SSH. Pokyny najdete v tématu [připojení přes SSH pomocí výstupu](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty).

1. Chcete-li aktualizovat server s balíčkem aplikace, zastavte službu Tomcat:

    ```bash  
        sudo systemctl stop tomcat
    ```

1. Aby bylo možné zapisovat do složky webapps, přidejte uživatele FTP do skupiny Tomcat. Uživatel FTP je uživatelem, kterého definujete při vytváření virtuálního počítače v centru Azure Stack.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

1. Pokud chcete vymazat složku webapps a pak načíst nový nebo aktualizovaný WAR, připojte se k VIRTUÁLNÍmu počítači pomocí FileZilly. Pokyny najdete v tématu [připojení pomocí protokolu SFTP s FileZilly](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).

    a. Zrušte zaškrtnutí *TOMCAT_HOME/webapps*.

    b. Přidejte svůj WAR do *TOMCAT_HOME/webapps* (například */opt/Tomcat/webapps/* ).

1.  Tomcat automaticky rozbalí a nasadí aplikaci. Můžete ji zobrazit pomocí názvu DNS, který jste vytvořili dříve. Například:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample
    ```
    
## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [vývoji centra Azure Stack](azure-stack-dev-start.md).
- Přečtěte si o [běžných nasazeních centra Azure Stack jako IaaS](azure-stack-dev-start-deploy-app.md).
- Informace o programovacím jazyce Java a vyhledání dalších prostředků pro jazyk Java najdete v tématu [Java.com](https://www.java.com).
