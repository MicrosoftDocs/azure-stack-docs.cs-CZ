---
title: Připojení zařízení IoT Edge k IoT Hub v Azure Stackovém centru
description: Naučte se, jak připojit zařízení IoT Edge k IoT Hub v Azure Stackm centru.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/20/2020
ms.reviewer: dmolokanov
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 768952db6d93930f710dd80ea10e23ae6d8297a5
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920645"
---
# <a name="connect-an-iot-edge-device-to-iot-hub-on-azure-stack-hub"></a>Připojení zařízení IoT Edge k IoT Hub v Azure Stackovém centru

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

V tomto článku se dozvíte, jak připojit virtuální IoT Edge zařízení, které IoT Hub spuštěné v Azure Stack hub. K připojení fyzického zařízení k vašemu IoT Hub můžete použít stejný obecný proces.

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, dokončete následující předpoklady:

- Budete potřebovat účet, který bude mít přístup k [portálu pro uživatele centra Azure Stack](../user/azure-stack-use-portal.md), s alespoň [oprávněními Přispěvatel](../user/azure-stack-manage-permissions.md).

- Spolupracujte se správcem a [nainstalujte IoT Hub do poskytovatele prostředků Azure Stack hub](../operator/iot-hub-rp-overview.md). Postup instalace také pokryje vytvoření nabídky, která zahrnuje službu IoT Hub. 

- Jakmile je nabídka k dispozici, může správce vytvořit nebo aktualizovat předplatné centra Azure Stack, aby zahrnoval IoT Hub. Alternativně se můžete [přihlásit k odběru nové nabídky a vytvořit vlastní předplatné](azure-stack-subscribe-services.md).

- Je užitečné mít základní znalosti o šifrování veřejných klíčů (PKI). Konkrétně způsob, jakým se používají certifikační autorita (CA) a certifikáty x509 k sestavení řetězu důvěryhodnosti. Tento vztah důvěryhodnosti umožňuje síťovým uzlům zabezpečeně ověřovat a komunikovat mezi sebou, jako je například služba IoT Hub a IoT Edge zařízení. 

## <a name="overview"></a>Přehled

Tady je souhrn kroků, které jste dokončili, k připojení zařízení IoT Edge k IoT Hub v centru Azure Stack:

1. Vytvořte prostředky virtuálních počítačů s IoT Hub a Linux ve vaší instanci centra Azure Stack. Virtuální počítač se systémem Linux bude sloužit jako virtuální zařízení IoT Edge. 
2. Nakonfigurujte certifikáty vyžadované pro IoT Edge zařízení. Počet požadovaných kroků bude záviset na typu certifikační autority, která vystavila certifikát kořenové certifikační autority IoT Hub.
3. Nakonfigurujte software a služby vyžadované pro IoT Edge zařízení.
4. Otestujte zařízení IoT Edge, abyste se ujistili, že správně funguje a komunikuje s IoT Hub.

Před spuštěním skriptu v jednotlivých částech nezapomeňte aktualizovat zástupné symboly pro skripty tak, aby odpovídaly konfiguraci vašeho prostředí. Poznamenejte si hodnoty, které použijete, jak je budete potřebovat v pozdějších částech:

| Zástupný symbol | Příklad | Popis |
|-------------|---------|-------------|
| `<DEVICE-CA-CERT-NAME>` | `edged1cert`| Název, kterým udělíte certifikát certifikační autority IoT Edge zařízení. |
| `<IOT-HUB-HOSTNAME>`| `test-hub-1.mgmtiothub.region.mydomain.com`| Název hostitele IoT Hub. |
| `<IOT-HUB-ROOT-CA>`| `root.cer`| Název souboru, který udělíte exportované IoT Hub kořenové certifikační autoritě (Pokud se používá privátní certifikační autorita). |
| `<DATA-DIR>`| `/home/edgeadmin/edged1`| Úplná cesta k datovému adresáři, který vytvoříte na virtuálním počítači se systémem Linux. |
| `<USER-ACCOUNT>`| `edgeadmin`| Účet, pomocí kterého se přihlašujete k virtuálnímu počítači se systémem Linux. |

## <a name="create-azure-stack-hub-resources"></a>Vytvoření prostředků centra Azure Stack

Nejdřív vytvoříte potřebné prostředky centra Azure Stack, včetně IoT Hub a virtuálního počítače se systémem Linux. K vytvoření těchto prostředků použijete [uživatelský portál Azure Stack hub](../user/azure-stack-use-portal.md) .

### <a name="create-an-iot-hub-resource"></a>Vytvoření prostředku služby IoT Hub

1. Přihlaste se k [portálu User Portal služby Azure Stack hub](../user/azure-stack-use-portal.md) z počítače, který má přístup k vaší instanci centra Azure Stack.
2. Pokud jste ho ještě nevytvořili, postupujte podle pokynů v části [vytvoření IoT Hub](/azure/iot-hub/iot-hub-create-through-portal#create-an-iot-hub) tématu **vytvoření centra IoT pomocí Azure Portal** k vytvoření prostředku IoT Hub. 

   > [!IMPORTANT]
   > Pokud budete postupovat podle kroků v článku, ale nemusíte Azure Portal, použijte [uživatelský portál Azure Stack hub](../user/azure-stack-use-portal.md) . Všimněte si také, že některé snímky obrazovky a pokyny se mohou mírně lišit, protože ne všechny funkce Azure jsou k dispozici v centru Azure Stack. 

### <a name="deploy-and-configure-a-linux-vm"></a>Nasazení a konfigurace virtuálního počítače se systémem Linux 

V této části nasadíte nový virtuální počítač Linux, který bude sloužit jako zařízení s virtuálním IoT Edge:

1. Pomocí rychlého startu [vytvoření Azure Stack virtuálního počítače serveru se systémem Linux](azure-stack-quick-linux-portal.md) nainstalujte do své instance centra Azure Stack virtuální počítač Linux. Nezapomeňte povolit port **SSH (22)** na vlastnosti **Vybrat veřejné příchozí porty** , když se dostanete na stránku **Nastavení** . 

   > [!NOTE]
   > Stačí provést první 5 sekcí, a to až po **připojení k virtuálnímu počítači**, protože nebudete potřebovat webový server Nginx. 

2. Po vytvoření a připojení k virtuálnímu počítači pomocí výstupu vytvořte podadresář dat z domovského adresáře uživatelského účtu, například:

   ```bash
   mkdir edged1
   ```

3. Nástroj pro generování certifikátů nastavte pomocí jedné z následujících metod. Stáhne buď soubory z úložiště GitHub IoT Edge požadované později pro generování certifikátů zařízení:

   **Použít skript kudrlinkou:**
   ```bash
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Download certGen.sh script file, and openssl_root_ca.cnf config file
   curl -Lo certGen.sh https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/certGen.sh
   curl -Lo openssl_root_ca.cnf https://raw.githubusercontent.com/Azure/iotedge/master/tools/CACertificates/openssl_root_ca.cnf
   ```
   **Pomocí Gitu naklonujte úložiště:**
   
   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>
   
   # Clone the iotedge repo, which contains the certGen.sh script file and openssl_root_ca.cnf config file
   git clone https://github.com/Azure/iotedge.git
   
   # Navigate to the edge device data directory
   cd <DATA-DIR>
   
   # Copy certGen.sh and openssl_root_ca.cnf into your working directory
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/*.cnf .
   cp /home/<USER-ACCOUNT>/iotedge/tools/CACertificates/certGen.sh .
   ```

## <a name="configure-iot-edge-device-certificates"></a>Konfigurace IoT Edgech certifikátů zařízení

V této části dokončíte konfiguraci certifikátu virtuálního počítače, kterou vyžaduje zařízení pro virtuální IoT Edge. 

Vaše služba IoT Hub a zařízení IoT Edge jsou zabezpečeny pomocí certifikátů x509. Oba musí používat certifikát od kořenové certifikační autority vydaný stejnou certifikační autoritou. Kliknutím na příslušnou kartu dokončete konfiguraci certifikátu na základě typu kořenové certifikační autority používaného vaším IoT Hub.

# <a name="public-ca"></a>[Veřejná certifikační autorita](#tab/public-ca)

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

# <a name="private-ca"></a>[Privátní certifikační autorita](#tab/private-ca)

> [!IMPORTANT]
> Pokud používáte privátní certifikační autoritu, vyžaduje se k přenosu IoT Hub certifikátu kořenové certifikační autority do zařízení IoT Edge další kroky. Tento postup * proveďte **pouze** v případě, že používáte vlastní privátní certifikační autoritu pro generování certifikátů. Pokud IoT Hub certifikát kořenové certifikační autority vystavila Veřejná certifikační autorita, vyberte předchozí kartu "*Veřejná CA**". 

### <a name="export-the-root-ca-certificate-from-your-iot-hub"></a>Exportujte certifikát od kořenové certifikační autority z vašeho IoT Hub

Pomocí počítače s přístupem k rozbočovači Azure Stack exportujte certifikát IoT Hub kořenové certifikační autority ve formátu PEM. Následující příklad ukazuje, jak exportovat certifikát pomocí nástroje [Microsoft Edge](https://www.microsoft.com/edge) nebo [Google Chrome](https://www.google.com/chrome/index.html) : 

   1. Na stránce **přehled** IoT Hub použijte tlačítko **Kopírovat** napravo od vlastnosti **hostname** ke zkopírování IoT Hub názvu hostitele do schránky:  

      [![Název hostitele IoT Hub](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png)](media\iot-hub-connect-an-iot-edge-device\copy-iot-hub-host-name.png#lightbox)
   1. V prohlížeči Microsoft Edge nebo Google Chrome otevřete novou kartu, zadejte `https://` , vložte IoT Hub název hostitele zkopírovaný v předchozím kroku a stiskněte klávesu **ENTER**. 

   1. Po vrácení chybové zprávy vyberte ikonu zámku vlevo od panelu Adresa a pak vyberte **certifikát**.
      [![zabezpečené připojení certifikátu](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-connection.png#lightbox)

   1. Když se zobrazí certifikát SSL/TLS, vyberte kartu **cesta k certifikaci** . Pak vyberte certifikát nejvyšší úrovně v cestě a klikněte na tlačítko **Zobrazit certifikát** .  
      [![zabezpečené připojení certifikátu pro certifikát SSL](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl.png#lightbox) 

   1. Po zobrazení certifikátu kořenové certifikační autority vyberte kartu **Podrobnosti** a pak klikněte na tlačítko **Kopírovat do souboru...** .
      [![zabezpečené připojení certifikátu pro certifikát kořenové certifikační autority](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png)](media\iot-hub-connect-an-iot-edge-device\iot-hub-root-ca-certificate-ssl-root-ca.png#lightbox) 

   1. Po zobrazení **Průvodce exportem certifikátu** exportujte certifikát do souboru **formátu X. 509 s kódováním Base-64** , například **root. cer**. Tento soubor použijete v další části, proto ho exportujte do umístění, ke kterému je možné přistupovat, nebo zkopírovat do virtuálního počítače se systémem Linux.

   1. Po úspěšném dokončení exportu můžete zavřít kartu prohlížeče.

### <a name="install-the-iot-hub-root-ca-certificate-on-the-iot-edge-device"></a>Instalace certifikátu IoT Hub Root CA na zařízení IoT Edge

Nyní nainstalujte certifikát IoT Hub kořenové certifikační autority, který jste exportovali v předchozí části, do důvěryhodného úložiště zařízení IoT Edge. 

1. Přeneste soubor IoT Hub kořenové certifikační autority, který jste exportovali v předchozí části, do IoT Edge zařízení. Vzhledem k tomu, že jako zařízení IoT Edge používáme virtuální počítač Linux, budete ho muset zkopírovat do virtuálního počítače se systémem Linux. V závislosti na vašem prostředí zvažte použití poštovního výstupu s příkazem PSCP nebo program WinSCP ke zkopírování souboru do virtuálního počítače se systémem Linux.
2. Spusťte následující skript z relace výstupů pro virtuální počítače Linux, kde jste uložili soubor IoT Hub Root CA. Skript ověří úspěšnost připojení TLS a nainstaluje kořenovou certifikační autoritu do důvěryhodného úložiště zařízení IoT Edge:

   ```bash
   # Verify connection failed first
   # Expected response: Verify return code: 2 (unable to get issuer certificate)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   
   # Verify connection succeeded when root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443 -CAfile <IOT-HUB-ROOT-CA>
   
   # Install root CA in the trusted store on Edge device
   sudo cp <IOT-HUB-ROOT-CA> /usr/local/share/ca-certificates/
   sudo update-ca-certificates
   
   # Verify connection succeeded even when no root CA provided
   # Expected response: Verify return code: 0 (ok)
   openssl s_client -connect <IOT-HUB-HOSTNAME>:443
   ```

[!INCLUDE [common-cert-config](../includes/iot-hub-connect-an-iot-edge-device-cert-common.md)]

### <a name="append-the-iot-hub-root-ca-to-the-device-root-ca"></a>Připojit IoT Hub kořenovou certifikační autoritu ke kořenové certifikační autoritě zařízení

Nyní přidejte kořenovou certifikační AUTORITu IoT Hub, kterou jste exportovali a zkopírovali do IoT Edge zařízení, do kořenové certifikační autority zařízení, kterou jste vygenerovali dříve:

```bash
# Navigate to home directory
cd /home/<USER-ACCOUNT>

# Append IoT Hub root CA to the device root CA
cat <IOT-HUB-ROOT-CA> >> certs/azure-iot-test-only.root.ca.cert.pem
```
-----

## <a name="configure-iot-edge-device-software-and-services"></a>Konfigurace softwaru a služeb IoT Edge zařízení

V této části dokončíte IoT Hub a konfiguraci virtuálního počítače, kterou vyžaduje zařízení s virtuálním IoT Edge.

### <a name="install-iot-edge-and-container-runtimes-on-the-vm"></a>Instalace IoT Edge a modulu runtime kontejnerů na virtuálním počítači

1. Vraťte se do relace výstupů virtuálních počítačů a spusťte následující skript, který zaregistruje informační kanál úložiště klíčů a softwaru Microsoftu:

   ```bash
   # Navigate to the home directory
   cd /home/<USER-ACCOUNT>

   # Install the repository configuration. 
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   
   # Copy the generated list.
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   
   # Install Microsoft GPG public key.
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

2. Instalace modulu runtime kontejneru:

   ```bash
   sudo apt-get update
   sudo apt-get install moby-engine moby-cli
   ```

3. Nainstalujte démona zabezpečení Azure IoT Edge:

   ```bash
   sudo apt-get update
   sudo apt-get install iotedge
   ```

> [!NOTE]
> Můžete ignorovat **Důležité: aktualizujte si prosím konfigurační soubor** , který se teď aktualizuje, protože ho budete aktualizovat v pozdější části.

### <a name="create-an-iot-edge-device-in-iot-hub"></a>Vytvoření zařízení IoT Edge v IoT Hub

1. Vraťte se na [portál pro uživatele centra Azure Stack](../user/azure-stack-use-portal.md)a přejděte do služby **IoT Hub** .

2. Vyberte prostředek IoT Hub, vyberte stránku **IoT Edge** a **přidejte IoT Edge zařízení**.

   [![Prostředek IoT Hub](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\add-an-iot-edge-device.png#lightbox)

3. Na stránce **vytvořit zařízení** zadejte **ID zařízení**, například "edged1".
4. Po dokončení vyberte **Uložit**.

   [![IoT Edge – vytvoření zařízení](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png)](media\iot-hub-connect-an-iot-edge-device\create-iot-edge-device.png#lightbox)

5. Až se portál vrátí na stránku **IoT Edge** , vyberte nové zařízení ze seznamu zařízení.

   [![IoT Edge – zobrazení zařízení](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png)](media\iot-hub-connect-an-iot-edge-device\view-iot-edge-devices.png#lightbox)

6. Na stránce Podrobnosti o zařízení použijte tlačítko **Kopírovat** na pravé straně **primárního připojovacího řetězce** a zkopírujte řetězec do schránky. Připojovací řetězec použijete v další části.

   [![IoT Edge – podrobnosti o zařízení](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-device-details.png#lightbox)

### <a name="configure-the-virtual-iot-edge-device-on-the-vm"></a>Konfigurace zařízení virtuální IoT Edge na virtuálním počítači

1. Vraťte se do relace výstupů virtuálních počítačů. Pomocí bash otevřete konfigurační soubor v nano na virtuálním IoT Edgeovém zařízení:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

2. V části `provisioning` Komentář **# Ruční konfigurace pomocí připojovacího řetězce** v části **režim zřizování a nastavení** vyhledejte vlastnost v části Comment # Manual Provisioning. Aktualizujte připojovací řetězec zařízení IoT Edge tak, že nahradíte `<ADD DEVICE CONNECTION STRING HERE>` zástupný text připojovacím řetězcem, který jste zkopírovali do schránky v předchozí části:

   > [!NOTE]
   > Chcete-li vložit obsah schránky do nano, stiskněte a podržte klávesu **SHIFT** a klikněte pravým tlačítkem myši. Nebo stiskněte současně klávesy **SHIFT** a **INSERT** . Pokud operace vložení přesune kurzor na konec připojovacího řetězce vpravo, stiskněte klávesu **Home** a vraťte se na konec úplně vlevo.

   ```yaml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

3. `certificates`V části **nastavení certifikátu** vyhledejte a odkomentujte vlastnost. Odkomentujte a nahraďte identifikátory URI souborů cestami k třem certifikátům, které jste vytvořili dříve, například:

   ```yaml
   certificates:
     device_ca_cert: "<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem"
     device_ca_pk: "<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem"
     trusted_ca_certs: "<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem"
   ```

4. Po dokončení `provisioning` `certificates` by vlastnosti a měly vypadat podobně jako u následujících odpovědí:

   [![Nano Editor – vlastnost zřizování](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-connection-string.png#lightbox)

   [![Nano Editor – vlastnost certifikáty](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png)](media\iot-hub-connect-an-iot-edge-device\nano-edit-config-yml-certificates.png#lightbox)


5. Soubor uložte a zavřete stisknutím **kombinace kláves CTRL +**, a pak na  +   **Y** a pak **Zadejte**.

6. Po návratu do bash restartujte démona:
   
   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="verify-a-successful-installation"></a>Ověření úspěšné instalace

1. Ověřte stav procesu démona IoT Edge:

   ```bash
   systemctl status iotedge
   ```

2. Měla by se zobrazit, že služba IoT Edge je spuštěná a "aktivní", s odpovědí podobnou následující. Pokud ano, můžete přejít dopředu ke kroku #4.

   [![Služba IoT Edge byla úspěšně spuštěna.](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-running.png#lightbox)

3. Pokud se služba IoT Edge nezdařila:
   - Zobrazí se odpověď podobná "nezdařené", podobně jako v následujícím příkladu: [ ![ IoT Edge služba selhala](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-failed.png#lightbox) .   
   - Pokud chcete řešit potíže, můžete:
     - Projděte si protokoly démona:

         ```bash
         journalctl -u iotedge --no-pager --no-full
         ```
     - Spusťte nástroj pro řešení potíží, ve kterém zjistíte nejběžnější chyby konfigurace a sítě. V tomto příkladu `/etc/iotedge/config.yaml` je zjištěn poškozený soubor:

         ```bash
         sudo iotedge check
         ```

         [![IoT Edge kontroly služby](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png)](media\iot-hub-connect-an-iot-edge-device\iot-edge-service-check.png#lightbox)

         > [!NOTE]
         > `$edgeHub`Systémový modul je nepovinný a není nasazený na zařízení, dokud nasadíte první IoT Edge modul. V takovém případě `iotedge check` může vracet chybu oznamující, že se `$edgeHub` během kontroly připojení hostitele nemůže vytvořit vazba na porty. Tuto chybu lze ignorovat, pokud `$edgeHub` není vyžadováno v nasazení.           

   - Pokud zjistíte, že máte poškozenou. YML soubor jako v předchozím příkladu jednoduše:
      - Zopakováním kroků v části [Konfigurace virtuálního zařízení IoT Edge na virtuálním počítači](#configure-the-virtual-iot-edge-device-on-the-vm) opravte. Soubor YML
      - Opakujte postup v části [ověření úspěšné instalace](#verify-a-successful-installation).

4. Jakmile zjistíte, že se služba IoT Edge úspěšně spustila, uveďte seznam spuštěných modulů. Měla by se zobrazit služba `edgeAgent` se stavem `running` :

   ```bash
   sudo iotedge list
   ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud je už nebudete používat, vraťte se na [uživatelský portál centra Azure Stack](../user/azure-stack-use-portal.md) a odstraňte virtuální počítač a prostředky IoT Hub, které jste vytvořili dříve.

## <a name="next-steps"></a>Další kroky

Níže jsou uvedené dodatečné prostředky:

- [Instalace modulu runtime Azure IoT Edge v systémech Linux založených na distribuci Debian](/azure/iot-edge/how-to-install-iot-edge-linux)
- [Konfigurace zařízení IoT Edge tak, aby fungovalo jako transparentní brána](/azure/iot-edge/how-to-create-transparent-gateway)
- [Vytváření ukázkových certifikátů pro účely testování funkcí zařízení IoT Edge](/azure/iot-edge/how-to-create-test-certificates)