---
title: Azure Stack ovládací prvky zabezpečení centra
titleSuffix: Azure Stack Hub
description: Seznamte se s stav a ovládacími prvky zabezpečení použitými pro centrum Azure Stack.
author: JustinHall
ms.topic: article
ms.date: 06/10/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 04/07/2020
ms.openlocfilehash: 2f276149e3998e5483ae4289ae6793d4b3ea86df
ms.sourcegitcommit: 373e9e3e84eaa33331db9f78e52486fbb6beb907
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91592871"
---
# <a name="azure-stack-hub-infrastructure-security-controls"></a>Ovládací prvky zabezpečení infrastruktury centra Azure Stack

Mezi hlavní faktory ovlivňující výběr hybridních cloudů patří aspekty zabezpečení a požadavky na dodržování předpisů. Azure Stack centrum je navrženo pro tyto scénáře. Tento článek vysvětluje ovládací prvky zabezpečení, které jsou na místě pro centrum Azure Stack.

Dvě vrstvy zabezpečení stav koexistovat v Azure Stack hub. První vrstvou je Azure Stack infrastruktura centra, která zahrnuje hardwarové součásti až do Azure Resource Manager. První vrstva zahrnuje správce a uživatelské portály. Druhá vrstva se skládá z úloh vytvořených, nasazených a spravovaných klienty. Druhá vrstva zahrnuje položky, jako jsou virtuální počítače a App Services weby.

## <a name="security-approach"></a>Bezpečnostní přístup

Stav zabezpečení pro centrum Azure Stack je navržená tak, aby se zabránilo moderním hrozbám a byla vytvořená tak, aby splňovala požadavky na hlavní standardy dodržování předpisů. V důsledku toho je stav zabezpečení infrastruktury centra Azure Stack postavená na dvou pilířích:

- **Předpokládat porušení**  
    Počínaje předpokladem, že již došlo k porušení systému, se zaměřte na *zjištění a omezení dopadu porušení a* pokusu o předcházení útokům.

- **Zpřísněno standardně**  
    Vzhledem k tomu, že infrastruktura běží na dobře definovaném hardwaru a softwaru, Azure Stack centrum *povoluje, konfiguruje a ověřuje všechny funkce zabezpečení* ve výchozím nastavení.

Vzhledem k tomu, že centrum Azure Stack se doručuje jako integrovaný systém, je stav zabezpečení infrastruktury centra Azure Stack definované Microsoftem. Stejně jako v Azure jsou klienti zodpovědní za definování stav zabezpečení svých úloh klientů. Tento dokument poskytuje základní znalosti o stav zabezpečení infrastruktury centra Azure Stack.

## <a name="data-at-rest-encryption"></a>Šifrování dat v klidovém umístění

Veškerá infrastruktura centra Azure Stack a data tenanta jsou v klidovém stavu zašifrovaná pomocí nástroje BitLocker. Toto šifrování chrání před fyzickou ztrátou nebo krádeží komponent úložiště centra Azure Stack. Další informace najdete v tématu [šifrování dat v klidovém umístění v Azure Stackovém centru](azure-stack-security-bitlocker.md).

## <a name="data-in-transit-encryption"></a>Data při přenosu – šifrování

Komponenty infrastruktury centra Azure Stack komunikují pomocí kanálů šifrovaných pomocí TLS 1,2. Šifrovací certifikáty jsou samy spravované infrastrukturou.

Všechny koncové body externí infrastruktury, jako jsou koncové body REST nebo portál centra Azure Stack, podporují protokol TLS 1,2 pro zabezpečenou komunikaci. Pro tyto koncové body musí být k dispozici šifrovací certifikáty, buď od třetí strany, nebo z certifikační autority rozlehlé sítě.

I když se certifikáty podepsané svým držitelem dají použít pro tyto externí koncové body, Microsoft je při jejich používání důrazně doporučuje.
Další informace o tom, jak vynutilit TLS 1,2 na vnějších koncových bodech centra Azure Stack, najdete v tématu [Konfigurace řízení Azure Stack zabezpečení centra](azure-stack-security-configuration.md).

## <a name="secret-management"></a>Správa tajných kódů

Infrastruktura centra Azure Stack používá k fungování velké množství tajných kódů, jako jsou hesla a certifikáty. Většina hesel přidružených k interním účtům služeb je automaticky otočená každých 24 hodin, protože [seskupují účty spravované služby (gMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview), což je typ účtu domény, který je spravovaný přímo interním řadičem domény.

Infrastruktura centra Azure Stack používá pro všechny své interní certifikáty 4096 klíčů RSA. Pro externí koncové body lze také použít stejné certifikáty klíčů. Další informace o tajných klíčích a rotaci certifikátů najdete [v tématu otočení tajných kódů v centru Azure Stack](azure-stack-rotate-secrets.md).

## <a name="windows-defender-application-control"></a>Řízení aplikací programu Windows Defender

Azure Stack Hub využívá nejnovější funkce zabezpečení Windows Serveru. Jedním z nich je ovládací prvek aplikace v programu Windows Defender (WDAC, dříve označovaný jako integrita kódu), který poskytuje filtrování spustitelných souborů a zajišťuje, že v infrastruktuře centra Azure Stack běží pouze ověřený kód.

Autorizovaný kód je podepsaný Microsoftem nebo partnerem OEM. Podepsaný autorizovaný kód je zahrnutý v seznamu povoleného softwaru zadaného v zásadě definované Microsoftem. Jinými slovy, lze spustit pouze software, který byl schválen ke spuštění v infrastruktuře centra Azure Stack. Jakýkoli pokus o spuštění neautorizovaného kódu se zablokuje a vygeneruje se upozornění. Azure Stack Hub vynucuje integritu kódu v uživatelském režimu (UMCI) i integritu kódu na úrovni hypervisoru (HVCI).

Zásady WDAC také zabrání spuštění agentů nebo softwaru třetích stran v infrastruktuře centra Azure Stack.
Další informace o WDAC najdete v tématu [řízení aplikací v programu Windows Defender a ochrana integrity kódu na základě virtualizace](/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control).

## <a name="credential-guard"></a>Ochrana přihlašovacích údajů Credential Guard

Další funkcí zabezpečení Windows serveru v centru Azure Stack je ochrana Credential Guard v programu Windows Defender, která se používá k ochraně přihlašovacích údajů infrastruktury centra Azure Stack z útoků pass-the-hash a Pass-The-Ticket.

## <a name="antimalware"></a>antimalware

Každá součást centra Azure Stack (hostitelé Hyper-V a virtuální počítače) je chráněná pomocí antivirové ochrany v programu Windows Defender.

V propojených scénářích se definice antivirového programu a aktualizace stroje používají několikrát denně. V odpojených scénářích se antimalwarové aktualizace používají jako součást měsíčních aktualizací centra Azure Stack. V případě, že se v odpojených scénářích vyžaduje častější aktualizace definic programu Windows Defender, Azure Stack centrum taky podporuje import aktualizací v programu Windows Defender. Další informace najdete v tématu [aktualizace antivirové ochrany v programu Windows Defender v centru Azure Stack](azure-stack-security-av.md).

## <a name="secure-boot"></a>Zabezpečené spouštění

Centrum Azure Stack vynutilo zabezpečené spouštění na všech hostitelích Hyper-V a virtuálních počítačích infrastruktury. 

## <a name="constrained-administration-model"></a>Model omezené správy

Správa v centru Azure Stack se řídí třemi vstupními body, z nichž každý má určitý účel:

- [Portál](azure-stack-manage-portals.md) pro správu nabízí možnosti každodenních operací správy a kliknutí na něj.
- Azure Resource Manager zveřejňuje všechny operace správy portálu pro správu prostřednictvím REST API používaného prostředím PowerShell a Azure CLI.
- Pro konkrétní operace nízké úrovně (například scénáře integrace Datacenter nebo podpory) zpřístupňuje služba Azure Stack hub koncový bod PowerShellu s názvem [privilegovaného koncového bodu](azure-stack-privileged-endpoint.md). Tento koncový bod zpřístupňuje jenom povolenou sadu rutin a je silně auditovaný.

## <a name="network-controls"></a>Správa sítě

Infrastruktura centra Azure Stack přichází s více vrstvami seznam Access Control sítě (ACL). Seznamy řízení přístupu brání neoprávněnému přístupu k součástem infrastruktury a omezují komunikaci infrastruktury jenom na cesty, které jsou pro její fungování nutné.

Seznamy ACL sítě se vysazují ve třech vrstvách:

- Vrstva 1: horní části přepínačů stojanu
- Vrstva 2: softwarově definovaná síť
- Vrstva 3: brány firewall pro operační systémy hostitele a virtuálního počítače

## <a name="regulatory-compliance"></a>Dodržování legislativní předpisů

Centrum Azure Stack prošlo formálním posouzením schopností, které nezávisle na třetí straně auditně nezávisle na sobě. V důsledku toho je k dispozici dokumentace k tomu, jak infrastruktura centra Azure Stack splňuje příslušné ovládací prvky z několika hlavních standardů dodržování předpisů. Tato dokumentace není certifikací centra Azure Stack, protože standardy zahrnují několik ovládacích prvků souvisejících s pracovníky a procesy. Zákazníci můžou místo toho použít tuto dokumentaci k tomu, abyste mohli začít se svým certifikačním procesem.

Posouzení zahrnuje následující standardy:

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) adresuje odvětví platební karty.
- Aplikace [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) je komplexní mapování mezi několika standardy, včetně FedRAMP střední, ISO27001, HIPAA, HiTRUST, ITAR, NIST SP800-53 a dalších.
- [FedRAMP vysoká](https://www.fedramp.gov/fedramp-releases-high-baseline/) pro zákazníky ze státní správy.

Dokumentaci k dodržování předpisů najdete na [portálu Microsoft Trust Service](https://aka.ms/azurestackcompliance). Příručky dodržování předpisů jsou chráněné prostředky a vyžadují, abyste se přihlásili pomocí svých přihlašovacích údajů ke cloudové službě Azure.

## <a name="next-steps"></a>Další kroky

- [Konfigurace ovládacích prvků zabezpečení centra Azure Stack](azure-stack-security-configuration.md)
- [Naučte se, jak tyto tajné klíče otočit v centru Azure Stack.](azure-stack-rotate-secrets.md)
- [Dokumenty PCI-DSS a CSA-CCM pro Azure Stack hub](https://aka.ms/azurestackcompliance)
