---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 900d7ac882a37e229bec6dc916653cf55992cccb
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "93049755"
---
Pak vytvořte nebo obnovte certifikát TLS pro zabezpečení hodnoty přidat koncové body poskytovatele prostředků:

1. Proveďte kroky v části [generování žádostí o podepsání certifikátu](../operator/azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal) pro poskytovatele prostředků pro obnovení certifikátu. Tady můžete pomocí nástroje pro kontrolu připravenosti centra Azure Stack vytvořit CSR. Nezapomeňte spustit správnou rutinu pro poskytovatele prostředků v kroku "generování žádostí o certifikát pro jiné služby centra Azure Stack". Například `New-AzsHubEventHubsCertificateSigningRequest` se používá pro Event Hubs. Po dokončení odešlete vygenerovanou adresu. Soubor požadavku pro certifikační autoritu (CA) pro nový certifikát.

2. Až obdržíte soubor certifikátu od certifikační autority, proveďte kroky v části [Příprava certifikátů pro nasazení nebo otočení](../operator/azure-stack-prepare-pki-certs.md). Znovu použijete nástroj pro kontrolu připravenosti ke zpracování souboru vráceného z certifikační autority.

3. Nakonec proveďte kroky v části [ověření certifikátů PKI Azure Stack hub](../operator/azure-stack-validate-pki-certs.md). K provádění ověřovacích testů na novém certifikátu budete používat nástroj pro kontrolu připravenosti.


