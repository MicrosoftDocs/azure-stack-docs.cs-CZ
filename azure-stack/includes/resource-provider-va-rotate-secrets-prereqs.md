---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 294f0f5b1f6ac45d53f5d3eebc222b3edba23776
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "93049760"
---
> [!NOTE]
> Rotace tajných kódů pro poskytovatele prostředků s hodnotou Add je aktuálně podporována pouze prostřednictvím PowerShellu. 

Stejně jako u infrastruktury centra Azure Stack je třeba přidat poskytovatele prostředků s použitím interních i externích tajných kódů. Tajné klíče mohou mít několik forem, včetně hesel a šifrovacích klíčů spravovaných certifikáty x509. Jako operátor zodpovídáte za:

- Poskytování aktualizovaných externích tajných kódů, jako je nový certifikát TLS, který se používá k zabezpečení koncových bodů poskytovatele prostředků.
- Pravidelná Správa rotace tajného kódu poskytovatele prostředků

Při přípravě na proces otáčení:

1. Před získáním/obnovením certifikátu x509 zkontrolujte [požadavky na certifikát infrastruktury veřejných klíčů (PKI) Azure Stack centra](../operator/azure-stack-pki-certs.md#certificate-requirements) pro důležité informace, včetně podrobností o požadovaném formátu PFX. Přečtěte si také požadavky uvedené v [části volitelné certifikáty PaaS](../operator/azure-stack-pki-certs.md#optional-paas-certificates)pro konkrétního poskytovatele prostředků s hodnotou přidat.

2. Pokud jste to ještě neudělali, před pokračováním [nainstalujte PowerShell AZ Module pro Azure Stack hub](../operator/powershell-install-az-module.md) . Pro otočení tajného kódu centra Azure Stack se vyžaduje verze 2.0.2-Preview nebo novější. Další informace najdete v tématu [migrace z AzureRM na Azure PowerShell AZ in Azure Stack hub](../operator/migrate-azurerm-az.md).
