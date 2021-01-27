---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 11/20/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 6d4bb587ad28fb26adf60689989d0e1858519cfe
ms.sourcegitcommit: 0765de47f4a73e09192d34739e40c750b6e7abaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920641"
---
### <a name="create-a-device-root-ca-certificate"></a>Vytvoření certifikátu kořenové certifikační autority zařízení

Vytvoření certifikátu kořenové certifikační autority zařízení a souborů klíčů potřebných pro vaše zařízení: 

1. Vraťte se do příkazového řádku bash ve vaší relaci pro výstup.
2. Přejděte do datového adresáře, do kterého jste umístili skripty pro generování certifikátů v předchozí části.
3. Spusťte následující příkaz:

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

4. Certifikát kořenové certifikační autority zařízení je uložený v následujícím souboru: `<DATA-DIR>/certs/azure-iot-test-only.root.ca.cert.pem` .

### <a name="create-the-iot-edge-device-ca-certificate"></a>Vytvoření certifikátu certifikační autority IoT Edge zařízení

Produkční IoT Edge zařízení potřebují certifikát certifikační autority zařízení, na který se odkazuje v souboru config. yaml. Certifikát certifikační autority zařízení zodpovídá za vytváření certifikátů pro moduly běžící na zařízení. Je taky potřeba pro scénáře bran, protože zařízení IoT Edge používá certifikát certifikační autority zařízení k ověření identity pro zařízení s přístupem pro příjem dat.

Vytvoření souborů certifikátů certifikační autority IoT Edge zařízení:

1. Vraťte se do příkazového řádku bash ve vaší relaci pro výstup.
2. Spusťte následující skript, který vytvoří několik souborů certifikátů a klíčů certifikační autority pro zařízení: 

   ```bash
   # Navigate to data directory
   cd <DATA-DIR>
   
   # Generate IoT Edge device CA certificate 
   ./certGen.sh create_edge_device_ca_certificate <DEVICE-CA-CERT-NAME>
   ```

3.  Následující soubory dvojice certifikátů a klíčů jsou odkazovány později v souboru config. yaml:

    `<DATA-DIR>/certs/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>-full-chain.cert.pem`  
    `<DATA-DIR>/private/iot-edge-device-ca-<DEVICE-CA-CERT-NAME>.key.pem`


