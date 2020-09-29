---
title: Použití Azure Backup k zálohování serverů Windows
description: Tento článek poskytuje pokyny pro použití Azure Backup v centru pro správu Windows k zálohování serverů Windows.
author: thomasmaurer
ms.author: thmaure
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: a4668e4d78a2ea3fb9f94913863d3471313034e5
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947499"
---
# <a name="use-azure-backup-to-back-up-windows-servers"></a>Použití Azure Backup k zálohování serverů Windows

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Centrum pro správu systému Windows zjednodušuje zálohování serverů Windows na Microsoft Azure. Proces také chrání před náhodnými nebo škodlivými odstraněními, poškozením a dokonce i ransomwarem. K automatizaci instalačního programu připojíte centrum pro správu Windows k Azure, abyste mohli používat službu Azure Backup.

V tomto článku se dozvíte, jak nakonfigurovat Azure Backup a vytvořit zásadu zálohování pro serverové svazky a stav systému Windows z centra pro správu systému Windows. Doprovodné materiály jsou určené pro zálohování úloh běžících na virtuálních počítačích v Azure Stack operační systém HCI do Azure.

Další informace o integraci Azure s centrem pro správu Windows najdete v tématu [propojení Windows serveru s Azure Hybrid Services](/windows-server/manage/windows-admin-center/azure/).

## <a name="how-azure-backup-works-with-windows-admin-center"></a>Jak Azure Backup funguje s centrem pro správu Windows
**Azure Backup** je služba, kterou můžete použít k zálohování, ochraně a obnovení dat v Azure. Azure Backup nahrazuje buď stávající místní nebo místní zálohovací řešení, s cloudovým řešením, které je spolehlivé, bezpečné a nákladově konkurenční.

Další informace najdete v tématu [co je služba Azure Backup?](/azure/backup/backup-overview)

Azure Backup nabízí několik komponent, které můžete stáhnout a nasadit na vhodném počítači, na serveru, nebo v cloudu. Nasazená komponenta nebo agent závisí na tom, co chcete chránit. Všechny součásti Azure Backup můžou zálohovat data do trezoru Recovery Services v Azure bez ohledu na to, jestli chráníte data v místním prostředí nebo v Azure.

Azure Backup integrace s centrem pro správu Windows je ideální pro zálohování svazků a stavu systému Windows pro místní nebo virtuální servery Windows. Komplexní proces zálohuje souborové servery, řadiče domény a webové servery služby IIS.

Přístup k Azure Backup v centru pro správu Windows prostřednictvím nástroje **Backup** . Možnosti nastavení, správy a monitorování **zálohovacího** nástroje umožňují rychle spustit zálohování serverů, provádět operace zálohování a obnovení a monitorovat celkový stav zálohování serverů Windows.

## <a name="prerequisites"></a>Požadavky
Pro použití Azure Backup jsou vyžadovány následující předpoklady:
- Účet Azure s aspoň jedním aktivním předplatným
- Internetový přístup k Azure pro cílové servery Windows
- Připojení z brány centra pro správu Windows do Azure

    Další informace najdete v tématu [Konfigurace integrace Azure](/windows-server/manage/windows-admin-center/azure/azure-integration).

## <a name="getting-started-with-azure-backup"></a>Začínáme s Azure Backup
Při prvním výběru **zálohovacího** nástroje v centru pro správu Windows k navázání připojení k serveru Azure se zobrazí stránka **Vítejte v Azure Backup** . Vyberte **nastavit Azure Backup** a spusťte tak průvodce instalací Azure Backup. V následujících částech najdete podrobný popis kroků v průvodci.

Pokud je pro připojení k serveru už Azure Backup nakonfigurovaná, výběr nástroje pro **zálohování** otevře **řídicí panel zálohování**. Informace o operacích a úlohách, které můžete provádět z řídicího panelu, najdete v části věnované [správě a monitorování](#management-and-monitoring) .

### <a name="step-1-log-on-to-the-microsoft-azure-portal"></a>Krok 1: přihlášení k portál Microsoft Azure
Přihlaste se ke svému účtu Azure.

> [!NOTE]
> Pokud jste už připojili bránu centra pro správu Windows k Azure, měli byste se k portálu automaticky přihlásit. Vyberte možnost **Odhlásit** se a potom se přihlaste jako jiný uživatel.

### <a name="step-2-set-up-azure-backup"></a>Krok 2: nastavení Azure Backup
Pro Azure Backup vyberte následující nastavení:
- **ID předplatného:** Předplatné Azure, které chcete použít k zálohování Windows serveru do Azure. Vytvoří se všechny prostředky Azure, jako je skupina prostředků Azure a Recovery Services trezor, a přidruží se k vybranému předplatnému.
- **Trezor:** Recovery Services trezor je umístění, kam se budou ukládat zálohy serverů. Můžete použít buď existující trezor, nebo centrum pro správu systému Windows. vytvoří se nový účet.  
- **Skupina prostředků:** Skupina prostředků Azure je kontejner pro kolekci prostředků. Trezor Recovery Services je buď vytvořený, nebo obsažený v zadané skupině prostředků. Můžete použít buď existující skupinu prostředků, nebo centrum pro správu systému Windows. vytvoří se nová skupina prostředků.
- **Umístění:** Oblast Azure, ve které se vytvoří trezor služby Recovery Services. Doporučujeme vybrat oblast Azure, která je nejblíže systému Windows Server, který zálohujete.

### <a name="step-3-select-backup-items-and-schedule"></a>Krok 3: vyberte zálohované položky a plán
1. Vyberte položky, které chcete zálohovat ze serveru. Centrum pro správu systému Windows umožňuje zvolit kombinaci svazků a stavu systému Windows a poskytuje odhadovanou velikost dat, která jste vybrali k zálohování.

    > [!NOTE]
    > První záloha je úplná záloha všech vybraných dat. Následná zálohování jsou přírůstková a přenášejí pouze změny dat, ke kterým došlo od předchozí zálohy.

1. Vyberte si z několika přednastavených **plánů zálohování** stav systému Windows a svazky.

### <a name="step-4-enter-an-encryption-passphrase"></a>Krok 4: zadejte šifrovací heslo
1. Zadejte **Šifrovací heslo** , které má minimálně 16 znaků. Azure Backup zabezpečení zálohovaných dat pomocí šifrovacího hesla, které vytvoříte a spravujete. K obnovení dat z Azure Backup je vyžadováno šifrovací heslo.

    > [!NOTE]
    > Uložte si přístupové heslo do zabezpečeného umístění mimo pracoviště, jako je třeba jiný server nebo [Azure Key Vault](/azure/key-vault/quick-create-portal). Microsoft toto heslo neukládá a nemůže ho načíst nebo resetovat, pokud dojde ke ztrátě nebo zapomenutí hesla.

1. Zkontrolujte všechna nastavení na stránce průvodce a pak vyberte **použít**.

Centrum pro správu systému Windows pak provede následující operace:
1. Vytvoří skupinu prostředků Azure, pokud ještě neexistuje.
1. Vytvoří úložiště Azure Recovery Services, jak je zadané.
1. Nainstaluje a zaregistruje agenta Microsoft Azure Recovery Services v trezoru.
1. Vytvoří plán zálohování a uchovávání podle vybraných možností přidružených k Windows serveru.

## <a name="management-and-monitoring"></a>Správa a monitorování
Po úspěšném nastavení Azure Backup se při otevření **zálohovacího** nástroje pro existující připojení k serveru zobrazí **řídicí panel zálohování** . Z řídicího panelu můžete provádět následující úlohy:
- **Přístup k trezoru v Azure:** Na kartě **Přehled** vyberte  **Recovery Services trezor** pro přístup k trezoru, kde můžete provádět mnoho operací správy. Další informace najdete v tématu [monitorování a Správa trezorů Recovery Services](/azure/backup/backup-azure-manage-windows-server).
- **Proveďte zálohu ad hoc:** Pokud chcete provést zálohování ad hoc, vyberte **Zálohovat nyní** . 
- **Monitorování úloh a konfigurace oznámení výstrah:** Přejděte na kartu **úlohy** a sledujte průběžné nebo minulé úlohy a [nakonfigurujte oznámení o výstrahách](/azure/backup/backup-azure-manage-windows-server#configuring-notifications-for-alerts) pro příjem e-mailů na neúspěšných úlohách a další výstrahy zálohování.
- **Zobrazit body obnovení a obnovit data:** Vyberte kartu **body obnovení** pro zobrazení bodů obnovení a vyberte možnost **obnovit data** pro kroky, jak obnovit data z Azure.

## <a name="next-steps"></a>Další kroky
Další informace najdete v tématu také:
- [Ochrana Azure Stack virtuálních počítačů HCI pomocí Azure Site Recovery](./azure-site-recovery.md)
