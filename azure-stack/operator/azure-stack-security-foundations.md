---
title: Azure Stack kontroly zabezpečení
titleSuffix: Azure Stack
description: Seznamte se s stav a ovládacími prvky zabezpečení použitými pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 4050a33be2436b919ffe4b4668b38a595523bd0d
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780792"
---
# <a name="azure-stack-infrastructure-security-controls"></a>Azure Stack kontroly zabezpečení infrastruktury

*Platí pro: Azure Stack integrovaných systémů*

Mezi hlavní faktory ovlivňující výběr hybridních cloudů patří aspekty zabezpečení a požadavky na dodržování předpisů. Pro tyto scénáře je určená služby Azure Stack. Tento článek vysvětluje ovládací prvky zabezpečení, které jsou na místě Azure Stack.

Dvě vrstvy zabezpečení stav koexistovat v Azure Stack. První vrstvou je Azure Stack infrastruktura, která zahrnuje hardwarové součásti až do Azure Resource Manager. První vrstva zahrnuje správce a portál tenanta. Druhá vrstva se skládá z úloh vytvořených, nasazených a spravovaných klienty. Druhá vrstva zahrnuje položky, jako jsou virtuální počítače a App Services weby.

## <a name="security-approach"></a>Bezpečnostní přístup

Stav zabezpečení pro Azure Stack je navržená tak, aby se zabránilo moderním hrozbám a byla vytvořená tak, aby splňovala požadavky na hlavní standardy dodržování předpisů. V důsledku toho je stav zabezpečení infrastruktury Azure Stack postaven na dvou pilířích:

- **Předpokládat porušení**  
    Počínaje předpokladem, že již došlo k porušení systému, se zaměřte na *zjištění a omezení dopadu porušení a* pokusu o předcházení útokům.

- **Zpřísněno standardně**  
    Vzhledem k tomu, že infrastruktura běží na dobře definovaném hardwaru a softwaru, Azure Stack ve výchozím nastavení *povolí, nakonfiguruje a ověří všechny funkce zabezpečení* .

Vzhledem k tomu, že Azure Stack se doručuje jako integrovaný systém, je stav zabezpečení infrastruktury Azure Stack definována společností Microsoft. Stejně jako v Azure jsou klienti zodpovědní za definování stav zabezpečení svých úloh klientů. Tento dokument poskytuje základní znalosti o stavi zabezpečení infrastruktury Azure Stack.

## <a name="data-at-rest-encryption"></a>Šifrování dat v klidovém umístění

Všechna Azure Stacková data infrastruktury a klientů jsou v klidovém stavu zašifrovaná pomocí nástroje BitLocker. Toto šifrování chrání před fyzickou ztrátou nebo krádeží Azure Stackch součástí úložiště. Další informace najdete v tématu šifrování neaktivních [dat v Azure Stack](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Data při přenosu – šifrování

Komponenty infrastruktury Azure Stack komunikují pomocí kanálů šifrovaných pomocí TLS 1,2. Šifrovací certifikáty jsou samy spravované infrastrukturou.

Všechny koncové body externí infrastruktury, jako jsou koncové body REST nebo portál Azure Stack, podporují protokol TLS 1,2 pro zabezpečenou komunikaci. Pro tyto koncové body musí být k dispozici šifrovací certifikáty, buď od třetí strany, nebo z certifikační autority rozlehlé sítě.

I když se certifikáty podepsané svým držitelem dají použít pro tyto externí koncové body, Microsoft je při jejich používání důrazně doporučuje.

## <a name="secret-management"></a>Správa tajných klíčů

Azure Stack infrastruktura používá k fungování velké množství tajných kódů, jako jsou hesla. Většina z nich se často automaticky otáčí, protože seskupují účty spravované služby (gMSA), které se v každé 24 hodinách střídají.

Zbývající tajné kódy, které nejsou gMSA, je možné ručně otočit pomocí skriptu v privilegovaném koncovém bodu.

## <a name="code-integrity"></a>Integrita kódu

Azure Stack využívá nejnovější funkce zabezpečení systému Windows Server 2016. Jedním z nich je ochrana zařízení v programu Windows Defender, která poskytuje seznam povolených aplikací a zajišťuje, aby se v Azure Stack infrastruktuře spouštěl jenom autorizovaný kód.

Autorizovaný kód je podepsaný Microsoftem nebo partnerem OEM. Podepsaný autorizovaný kód je zahrnutý v seznamu povoleného softwaru zadaného v zásadě definované Microsoftem. Jinými slovy, lze spustit pouze software, který byl schválen pro spuštění v infrastruktuře Azure Stack. Všechny pokusy o spuštění neoprávněného kódu jsou blokované a vygeneruje se audit.

Zásady ochrany zařízení také zabrání spuštění agentů nebo softwaru třetích stran v infrastruktuře Azure Stack.

## <a name="credential-guard"></a>Credential Guard

Další funkcí zabezpečení Windows serveru 2016 v systému Azure Stack je ochrana Credential Guard v programu Windows Defender, která se používá k ochraně Azure Stackch přihlašovacích údajů infrastruktury z útoků pass-the-hash a Pass-The-Ticket.

## <a name="antimalware"></a>Antimalware

Všechny součásti v Azure Stack (hostitelé a virtuální počítače Hyper-V) jsou chráněni pomocí antivirové ochrany v programu Windows Defender.

V propojených scénářích se definice antivirového programu a aktualizace stroje používají několikrát denně. V odpojených scénářích se antimalwarové aktualizace používají jako součást měsíčních aktualizací Azure Stack. Další informace najdete v tématu [aktualizace antivirové ochrany v programu Windows Defender na Azure Stack](azure-stack-security-av.md).

## <a name="constrained-administration-model"></a>Model omezené správy

Správa v Azure Stack se řídí třemi vstupními body, z nichž každý má určitý účel:

- [Portál](azure-stack-manage-portals.md) pro správu nabízí možnosti každodenních operací správy a kliknutí na něj.
- Azure Resource Manager zveřejňuje všechny operace správy portálu pro správu prostřednictvím REST API používaného prostředím PowerShell a Azure CLI.
- Pro konkrétní operace nízké úrovně (například scénáře integrace Datacenter nebo podpory) Azure Stack zveřejňuje koncový bod PowerShellu s názvem [privilegovaný koncový bod](azure-stack-privileged-endpoint.md). Tento koncový bod zpřístupňuje jenom seznam povolených rutin a je silně auditovaný.

## <a name="network-controls"></a>Ovládací prvky sítě

Infrastruktura Azure Stack přichází s více vrstvami seznam Access Control sítě (ACL). Seznamy řízení přístupu brání neoprávněnému přístupu k součástem infrastruktury a omezují komunikaci infrastruktury jenom na cesty, které jsou pro její fungování nutné.

Seznamy ACL sítě se vysazují ve třech vrstvách:

- Vrstva 1: horní části přepínačů stojanu
- Vrstva 2: softwarově definovaná síť
- Vrstva 3: brány firewall pro operační systémy hostitele a virtuálního počítače

## <a name="regulatory-compliance"></a>Dodržování právních předpisů

Azure Stack prošla formálním posouzením interního auditu nezávislého na třetích stran. V důsledku toho je k dispozici dokumentace k tomu, jak Azure Stack infrastruktura splňuje příslušné ovládací prvky z několika hlavních standardů dodržování předpisů. Dokumentace není certifikace Azure Stack, protože standardy zahrnují několik ovládacích prvků souvisejících s pracovníky a procesy. Zákazníci můžou místo toho použít tuto dokumentaci k tomu, abyste mohli začít se svým certifikačním procesem.

Posouzení zahrnuje následující standardy:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) adresuje odvětví platební karty.
- Aplikace [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) je komplexní mapování mezi několika standardy, včetně FedRAMP střední, ISO27001, HIPAA, HiTRUST, ITAR, NIST SP800-53 a dalších.
- [FedRAMP vysoká](https://www.fedramp.gov/fedramp-releases-high-baseline/) pro zákazníky ze státní správy.

Dokumentaci k dodržování předpisů najdete na [portálu Microsoft Trust Service](https://servicetrust.microsoft.com/ViewPage/Blueprint). Příručky dodržování předpisů jsou chráněné prostředky a vyžadují, abyste se přihlásili pomocí svých přihlašovacích údajů ke cloudové službě Azure.

## <a name="next-steps"></a>Další kroky

- [Konfigurace ovládacích prvků zabezpečení Azure Stack](azure-stack-security-configuration.md)
- [Informace o obměně tajných klíčů ve službě Azure Stack](azure-stack-rotate-secrets.md)
- [Dokumenty PCI-DSS a CSA-CCM pro Azure Stack](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Dokumenty DoD a NIST pro Azure Stack](https://servicetrust.microsoft.com/ViewPage/Blueprint)
