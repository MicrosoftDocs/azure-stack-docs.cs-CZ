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
ms.openlocfilehash: dbf6083ff81d045d92d488eda5cfab757093bb7e
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782887"
---
# <a name="how-to-deploy-a-java-web-app-to-a-vm-in-azure-stack"></a>Jak nasadit webové aplikace v Javě do virtuálního počítače ve službě Azure Stack

Můžete vytvořit virtuální počítač pro hostování vaší aplikace webového Pythonu ve službě Azure Stack. Tento článek ukazuje postup popsaný v části Nastavení serveru, konfigurace serveru pro hostování vaší webové aplikace v Pythonu a potom nasazení vaší aplikace.

Tento článek provede procesem instalace a konfigurace serveru Apache Tomcat na virtuálním počítači Linux ve službě Azure Stack a potom načtením souboru prostředků (WAR) Web v jazyce Java aplikace do serveru. Soubor WAR se používá k distribuci kolekce soubory JAR komprimovaný soubor, který obsahuje prostředky v Javě jako jsou třídy, text, obrázky, XML a HTML a další prostředky, které používají k doručování webových aplikací.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Nastavte svůj virtuální počítač nastavit ve službě Azure Stack. Postupujte podle kroků v [nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack](azure-stack-dev-start-howto-deploy-linux.md).

2. V okně sítě virtuálních počítačů Ujistěte se, že jsou k dispozici následující porty:

    | Port | Protocol | Popis |
    | --- | --- | --- |
    | 80 | HTTP | Protokol HTTP (Hypertext Transfer) je protokol použitý k doručování webových stránek ze serverů. Klienti se připojují přes protokol HTTP s názvem DNS nebo IP adresu. |
    | 443 | HTTPS | Protokol zabezpečení HTTPS (Hypertext Transfer) je zabezpečený verzi protokolu HTTP, který vyžaduje certifikát zabezpečení a umožňuje šifrovaného přenosu informací.  |
    | 22 | SSH | Secure Shell (SSH) je protokol šifrovaných sítí pro zabezpečenou komunikaci. Toto připojení použijete s klientem SSH ke konfiguraci virtuálního počítače a nasazení aplikace. |
    | 3389 | Protokol RDP | Volitelné. Remote Desktop Protocol umožňuje připojení ke vzdálené ploše použít grafické uživatelské rozhraní vašeho počítače.   |
    | 8080 | Vlastní | Výchozí port pro službu Apache Tomcat je 8080. Pro produkční server budete chtít směrovat provoz přes 80 a 443. |

## <a name="install-java"></a>Nainstalovat Java

1. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
2. Na řádku prostředí bash ve virtuálním počítači zadejte následující příkazy:

    ```bash  
        sudo apt-get install default-jdk
    ```

3. Ověření instalace. Stále připojeni k virtuálnímu počítači v relaci SSH, zadejte následující příkazy:

    ```bash  
        java -version
    ```

## <a name="install-and-configure-tomcat"></a>Instalace a konfigurace Tomcat

1. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).

2. Vytvoření uživatele Tomcat.
    - Nejprve vytvořte novou skupinu Tomcat:
        ```bash  
            sudo groupadd tomcat
        ```
     
    - Za druhé vytvořte uživatele Tomcat. Přidání tohoto uživatele do skupiny tomcat s domovskému adresáři `/opt/tomcat`. Tomcat nasadí do tohoto adresáře:
        ```bash  
            sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
        ```

3. Nainstalujte Tomcat.
    - Nejprve získejte adresu URL pro cíl pro nejnovější verzi Tomcat 8 ze stránky pro stažení Tomcat 8 na:  [http://tomcat.apache.org/download-80.cgi](http://tomcat.apache.org/download-80.cgi)
    - Druhý, curl uživatele Chcete-li stáhnout nejnovější verzi pomocí odkazu.

        ```bash  
            cd /tmp 
            curl -O <URL for the tar for the latest version of Tomcat 8>
        ```

    - Tomcat na třetí, nainstalujte `/opt/tomcat` adresáře. Vytvořte složku.  Archiv otevřete:

        ```bash  
            sudo mkdir /opt/tomcat
            sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
            sudo chown -R tomcat webapps/ work/ temp/ logs/
        ```

4. Aktualizujte oprávnění pro Tomcat.

    ```bash  
        sudo chgrp -R tomcat /opt/tomcat
        sudo chmod -R g+r conf
        sudo chmod g+x conf
    ```

5. Vytvoření `systemd` souboru služby. tak, aby Tomcat může běžet jako služba.

    - Tomcat je potřeba vědět, kam jste nainstalovali Java. Tato cesta je obvykle označuje jako **JAVA_HOME**. Vyhledejte umístění spuštěním:

        ```bash  
            sudo update-java-alternatives -l
        ```

        Vznikne přibližně takto:

        ```Text  
            Output
            java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64
        ```

        **JAVA_HOME** hodnotu proměnné lze zkonstruovat trvá cestu z výstupu a přidáním `/jre`. Například použití výše uvedeném příkladu: `/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre`

    - K vytvoření souboru definice služby systemd použijte hodnotu z vašeho serveru.

        ```bash  
            sudo nano /etc/systemd/system/tomcat.service
        ```

    - Vložte následující obsah do souboru služby. Změnit hodnotu **JAVA_HOME** Pokud nezbytné tak, aby odpovídala hodnotě jste našli v systému. Můžete také upravit nastavení přidělení paměti, které jsou určené v CATALINA_OPTS:

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

    - Uložte soubor a zavřete ho.
    - Znovu načte démona systemd tak, aby věděl o souboru služby:

        ```bash  
            sudo systemctl daemon-reload
        ```

    - Spustit službu Tomcat 

        ```bash  
            sudo systemctl start tomcat
        ```

    - Ověřte, zda je spuštěna bez chyb tak, že zadáte:

        ```bash  
            ssudo systemctl status tomcat
        ```

6. Ověřte Tomcat server. Tomcat používá port 8080 tak, aby přijímal konvenční požadavky. Povolení provozu na daný port spuštěním následujícího příkazu:

    ```bash  
        sudo ufw allow 8080
    ```

    Pokud jste nepřidali **příchozí pravidla portů** pro virtuální počítač Azure Stack, pak ty nyní přidat. Další informace najdete v tématu [vytvoření virtuálního počítače](#create-a-vm).

7. Otevřete prohlížeč ve stejné síti jako služba Azure Stack a otevřete váš server: `yourmachine.local.cloudapp.azurestack.external:8080`

    ![Apache Tomcat v Azure stacku virtuálního počítače](media/azure-stack-dev-start-howto-vm-java/apache-tomcat.png)

    Načtení stránky Apache Tomcat na vašem serveru. Dále je nutné nakonfigurovat server k tomu, abyste pro přístup k stav serveru, aplikace správce a správce hostitele.

8. Povolení souboru definice služby, který Tomcat automaticky spustí po restartování serveru:

    ```bash  
        sudo systemctl enable tomcat
    ```

9. Konfigurace serveru Tomcat, aby bylo možné přistupovat k rozhraní webové správy. Upravit `tomcat-users.xml` a definovat role a uživatele tak, aby se můžete přihlásit. Definování uživatelského přístupu k `manager-gui` a `admin-gui`.

    ```bash  
        sudo nano /opt/tomcat/conf/tomcat-users.xml
    ```

    - Přidejte následující prvky převedete do každodenního `<tomcat-users>` části:

    ```XML  
        <role rolename="tomcat"/>
        <user username="<username>" password="<password>" roles="tomcat,manager-gui,admin-gui"/>
    ```

    - Konečný soubor může například vypadat přibližně jako:

    ```XML  
        <tomcat-users xmlns="http://tomcat.apache.org/xml"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://tomcat.apache.org/xml tomcat-users.xsd"
              version="1.0">
         <role rolename="tomcat"/>
        <user username="tomcatuser" password="changemepassword" roles="tomcat,manager-gui,admin-gui"/>
        </tomcat-users>
    ```

    - Uložte soubor a zavřete ho.


10. Tomcat omezuje přístup **správce** a **správce hostitele** aplikace k připojení ze serveru. Vzhledem k tomu, že instalujete Tomcat na virtuálním počítači ve službě Azure Stack, můžete k odebrání tohoto omezení. Omezení podle IP adresy na tyto aplikace, změnit úpravou odpovídající `context.xml` soubory.

    - Aktualizace `context.xml` aplikace správce:

        ```bash  
            sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
        ```

    - Okomentujte omezení IP adresy umožňují připojení z libovolného místa, nebo přidat IP adresu počítače, který používáte pro připojení k Tomcat.

        ```XML  
        <Context antiResourceLocking="false" privileged="true" >
          <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
                 allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
        </Context>
        ```

    - Uložte soubor a zavřete ho.

    - Aktualizovat `context.xml` aplikace Správce hostitele s podobné aktualizace:

        ```bash  
            sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml
        ```

    - Uložte soubor a zavřete ho.

11. Restartujte službu Tomcat aktualizace serveru se změnami:

    ```bash  
        sudo systemctl restart tomcat
    ```

12. Otevřete prohlížeč ve stejné síti jako služba Azure Stack a otevřete váš server: `yourmachine.local.cloudapp.azurestack.external:8080`.

    - Vyberte stav serveru a zkontrolujte stav serveru Tomcat a ověřte, že máte přístup.
    - Přihlaste se pomocí svých přihlašovacích údajů Tomcat.

![Apache Tomcat v Azure stacku virtuálního počítače](media/azure-stack-dev-start-howto-vm-java/apache-tomcat-management-app.png)

## <a name="create-an-app"></a>Vytvoření nové aplikace

Je potřeba vytvořit soubor WAR nasadit Tomcat. Pokud chcete jenom zkontrolovat vaše prostředí, najdete příklad War na serveru Apache TomCat: [ukázkovou aplikaci](https://tomcat.apache.org/tomcat-6.0-doc/appdev/sample/).

Pokyny týkající se vývoje aplikací v Javě v Azure najdete v tématu [sestavování a nasazování aplikací v Javě v Azure](https://azure.microsoft.com/develop/java/).

## <a name="deploy-and-run-the-app"></a>Nasazení a spuštění aplikace

1. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači. Pokyny najdete v tématu [připojit přes SSH pomocí PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty).
1. Zastavte službu Tomcat aktualizace serveru s vaším balíčkem aplikací:

    ```bash  
        sudo systemctl stop tomcat
    ```

2.  Aby mohla zapisovat do složky, webové aplikace, přidejte do skupiny tomcat vašich uživatelů serveru FTP. Uživatel FTP je uživatel vaší definovat při vytváření virtuálního počítače ve službě Azure Stack.

    ```bash  
        sudo usermod -a -G tomcat <VM-user>
    ```

3. Připojte se ke svému virtuálnímu počítači pomocí Filezilly zrušte složce webové aplikace a pak načíst nové nebo aktualizované WAR. Pokyny k používání FileZila najdete v tématu [připojit pomocí protokolu SFTP pomocí Filezilly](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-sftp-with-filezilla).
    - Vymazat `TOMCAT_HOME/webapps`.
    - Přidat vaše WAR do ` TOMCAT_HOME/webapps`, například `/opt/tomcat/webapps/`.

4.  Tomcat automaticky rozbalí a nasadí aplikaci. Lze ji zobrazit s názvem DNS, který jste vytvořili dříve. Příklad:

    ```HTTP  
       http://yourmachine.local.cloudapp.azurestack.external:8080/sample

## Next steps

- Learn more about how to [Develop for Azure Stack](azure-stack-dev-start.md)
- Learn about [common deployments for Azure Stack as IaaS](azure-stack-dev-start-deploy-app.md).
- To learn the Java programming language and find additional resources for Java, see [Java.com](https://www.java.com).