---
title: Postup nasazení F5 ve dvou instancích centra Azure Stack | Microsoft Docs
description: Naučte se nasazovat F5 v rámci dvou Azure Stackch instancí centra.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 360bd0fee975aaa1eb7f7dd02948493862f6105d
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75815387"
---
# <a name="how-to-deploy-f5-across-two-azure-stack-hub-instances"></a>Postup nasazení F5 v rámci dvou instancí centra Azure Stack

Tento článek vás provede nastavením externího nástroje pro vyrovnávání zatížení ve dvou Azure Stack hub prostředích. Tuto konfiguraci můžete použít ke správě různých úloh. V tomto článku nasadíte F5 jako globální řešení vyrovnávání zatížení ve dvou nezávislých instancích centra Azure Stack. Také nasadíte webovou aplikaci s vyrovnáváním zatížení běžící na serveru NGINX napříč vašimi dvěma instancemi. Budou spuštěny za dvojicí s vysokou dostupností a převzetím služeb při selhání ve virtuálním zařízení F5.

Šablony Azure Resource Manager můžete najít v úložišti GitHubu [F5-azurestack-GSLB](https://github.com/Mikej81/f5-azurestack-gslb) .

## <a name="overview-of-load-balancing-with-f5"></a>Přehled vyrovnávání zatížení pomocí F5

Hardware F5, nástroj pro vyrovnávání zatížení, může být mimo Azure Stack centra a v rámci datového centra, které je hostitelem centra Azure Stack. Centrum Azure Stack nemá nativní schopnost vyrovnávat zatížení úloh ve dvou různých nasazeních centra Azure Stack. Virtuální edice F5's BIG-IP (VE verzi) běží na obou platformách. Tato sada podporuje paritu mezi architekturami centra Azure a Azure Stack prostřednictvím replikace podpůrných aplikačních služeb. Můžete vyvíjet aplikaci v jednom prostředí a přesunout ji do jiného. Můžete také zrcadlit celé prostředí Azure Stack připravené pro produkční prostředí, včetně stejných konfigurací, zásad a aplikačních služeb pro velké objemy IP adres. Přístup eliminuje nutnost dlouhé hodin refaktoringu a testování aplikací a umožňuje vám začít s psaním kódu.

Zabezpečení aplikací a jejich dat často se týká vývojářů, kteří přesouvá aplikace do veřejného cloudu. To nemusí být případ. Aplikaci můžete vytvořit v prostředí Azure Stack hub, zatímco architekt zabezpečení nakonfiguruje potřebná nastavení na firewallu webových aplikací F5's (WAF). Celý zásobník se dá v Azure Stackovém centru replikovat s vědomím, že aplikace bude chráněná stejným oborem, který špičkové WAF. Se stejnými zásadami a RuleSets nebudou žádné bezpečnostní zneužitelných ani ohrožení zabezpečení, které by jinak bylo možné vygenerovat pomocí různých WAF.

Centrum Azure Stack má samostatné tržiště z Azure. Přidávají se jenom určité položky. V takovém případě, pokud chcete vytvořit novou skupinu prostředků na každém z Azure Stack Center a nasadit virtuální zařízení F5, které už je dostupné. Odtud uvidíte, že se bude vyžadovat, aby byla k dispozici **Veřejná IP** adresa, která umožňuje připojení k síti mezi Azure Stackmi instancemi hub. V podstatě se jedná o obě ostrovy a **Veřejná IP adresa** jim umožní komunikovat v obou umístěních.

## <a name="prerequisites-for-big-ip-ve"></a>Předpoklady pro BIG-IP VE

-  Stáhněte si soubor **F5 Big-IP ve všech (BYOL, 2 Boot Locations)** do každého portálu Azure Stack hub Marketplace. Pokud je nemáte k dispozici na portálu, obraťte se na svého operátora cloudu.

-  Šablonu Azure Resource Manager můžete najít v následujícím úložišti GitHub: https://github.com/Mikej81/f5-azurestack-gslb.

## <a name="deploy-f5-big-ip-ve-on-each-instance"></a>Nasazení F5 BIG-IP v každé instanci

Nasaďte do Azure Stack a instance centra a instanci B.

1. Přihlaste se k portálu Azure Stack User Portal.

2. Vyberte **+ vytvořit prostředek**.

3. Na webu Marketplace zadejte `F5`.

4. Vyberte **F5 Big-IP ve – All (BYOL, 2 spouštěcí umístění)** .

    ![](./media/network-howto-f5/image1.png)

5. V dolní části Další stránky vyberte **vytvořit**.

    ![](./media/network-howto-f5/image2.png)

6. Vytvořte novou skupinu prostředků s názvem **F5-GSLB**.

7. Použijte následující hodnoty jako příklad k dokončení nasazení:

    ![](./media/network-howto-f5/image3.png)

8. Ověřte, že se nasazení úspěšně dokončilo.

    ![](./media/network-howto-f5/image4.png)

    > [!Note]  
    > Každé nasazení ve velkém protokolu IP by mělo trvat přibližně 20 minut.

## <a name="configure-big-ip-appliances"></a>Konfigurace zařízení se BIG-IP

Postupujte podle těchto kroků pro Azure Stack hub a i B.

1. Přihlaste se k portálu Azure Stackho centra uživatelů v instanci centra Azure Stack A Zkontrolujte prostředky vytvořené z nasazení šablony BIG-IP.

    ![](./media/network-howto-f5/image18.png)

2. Postupujte podle pokynů na adrese F5 pro [položky konfigurace Big-IP](https://clouddocs.f5.com/training/community/dns/html/class1/class1.html). 

3. Nakonfigurujte seznam adres IP s velkým rozsahem IP adres, který bude naslouchat na obou zařízeních nasazených do Azure Stack hub instance a a B. Pokyny najdete v tématu [Konfigurace GTM Big-IP](https://techdocs.f5.com/kb/en-us/products/big-ip_gtm/manuals/product/gtm-concepts-11-5-0/4.html).


4. Ověří převzetí služeb při selhání zařízení se BIG-IP. V testovacím systému nakonfigurujte servery DNS tak, aby používaly následující:
    - Instance centra Azure Stack A = `f5stack1-ext` veřejné IP adresy
    - Azure Stack hub instance B = `f5stack1-ext` veřejnou IP adresou

5. Přejděte do `www.contoso.com` a v prohlížeči se načte výchozí stránka NGINX.

## <a name="create-a-dns-sync-group"></a>Vytvoření skupiny synchronizace DNS

1. Povolte vytvoření vztahu důvěryhodnosti u kořenového účtu. Postupujte podle pokynů v části [Změna hesel účtů údržby systému (11. x-15. x)](https://support.f5.com/csp/article/K13121). Po nastavení vztahu důvěryhodnosti (Výměna certifikátů) zakažte kořenový účet.

1. Přihlaste se ke službě BIG-IP a vytvořte skupinu synchronizace DNS. Pokyny najdete v tématu [Vytvoření skupiny synchronizace služby DNS s velkým objemem IP adres](https://f5-dns-automation-demo-12-1-x.readthedocs.io/en/latest/lab2/sync-group.html).

    > [!Note]  
    > Místní IP adresu zařízení BIP-IP najdete ve skupině prostředků **F5-GSLB** . Síťové rozhraní je "f5stack1-ext" a Vy se chcete připojit k veřejné nebo soukromé IP adrese (v závislosti na přístupu).

    ![](./media/network-howto-f5/image5.png)
          
    ![](./media/network-howto-f5/image6.png)

1. Vyberte novou skupinu prostředků **F5-GSLB** a vyberte virtuální počítač **F5stack1** v části **Nastavení** vyberte **síť**.

## <a name="post-install-configurations"></a>Konfigurace po instalaci

Po instalaci nástroje je potřeba nakonfigurovat skupin zabezpečení sítě centra pro Azure Stack a uzamknout zdrojové IP adresy.

1. Po navázání vztahu důvěryhodnosti zakažte port 22.

2. Když je systém online, zablokuje se zdrojové skupin zabezpečení sítě. Správa NSG by měla být pevně nastavená na zdroj správy, externí (4353/TCP) NSG by se měla uzamknout k druhé instanci pro synchronizaci. 443 by měl být zamčený, dokud nebudou nasazené aplikace s virtuálními servery.

3. GTM_DNS pravidlo je nastavené tak, aby povolovalo provoz na portu 53 (DNS), a překladač BIG-IP začne pracovat jednou. Naslouchací procesy jsou vytvořeny.

    ![](./media/network-howto-f5/image7.png)

4. Nasaďte základní úlohu webové aplikace v prostředí Azure Stack hub, abyste vyrovnali zatížení za velkou IP adresou. Příklad použití serveru NGNIX najdete v části [nasazení Nginx a Nginx plus v Docker](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/).

    > [!Note]  
    > Nasaďte instanci NGNIX do centra Azure Stack a a Azure Stack Hub B.

5. Po nasazení NGINX v kontejneru Docker na virtuálním počítači s Ubuntu v rámci každé instance centra Azure Stack ověřte, že se můžete dostat k výchozí webové stránce na serverech.

    ![](./media/network-howto-f5/image8.png)

6. Přihlaste se k rozhraní pro správu zařízení BIG-IP. V tomto příkladu použijte veřejnou IP adresu **F5-stack1-EXT** .

    ![](./media/network-howto-f5/image9.png)

7. Publikujte přístup k NGINX prostřednictvím BIG-IP.
    
    -  V této úloze nakonfigurujete službu BIG-IP s virtuálním serverem a fondem tak, aby umožňovala příchozí internetový přístup k aplikaci WordPress. Nejdřív je potřeba určit privátní IP adresu pro instanci NGINX.

8. Přihlaste se k portálu pro uživatele centra Azure Stack. 

9. Vyberte své síťové rozhraní NGINX.

    ![](./media/network-howto-f5/image10.png)

10. V konzole BIG-IP otevřete **> fondy místních přenosů > fond** a vyberte **+** . Nakonfigurujte fond pomocí hodnot v tabulce. Ponechte všechna ostatní pole na výchozí hodnoty.

    ![](./media/network-howto-f5/image11.png)
    
    | Klíč | Hodnota |
    | --- | --- |
    | Name (Název) | NGINX_Pool |
    | Monitor stavu | HTTPS |
    | Název uzlu | NGINX |
    | Adresa | \<privátní IP adresu NGINX > |
    | Port služby | 443 |

11. Vyberte **dokončeno**. Pokud je stav fondu správně nakonfigurovaný, je zelený.

    ![](./media/network-howto-f5/image12.png)

    Teď je potřeba nakonfigurovat virtuální server. Abyste to mohli udělat, musíte nejdřív najít soukromou IP adresu vaší aplikace F5 BIG-IP.

12. V konzole BIG-IP navštivte **síť > osobní** IP adresy a poznamenejte si IP adresu.

    ![](./media/network-howto-f5/image13.png)

13. Vytvořte virtuální server tak, že na **místní provoz** přesunete > **virtuální servery** > **seznam virtuálních serverů** a vyberete **+** . Nakonfigurujte fond pomocí hodnot v tabulce. Ponechte všechna ostatní pole na výchozí hodnoty.

    | Klíč | Hodnota |
    | --- | --- |
    |Name (Název) | NGINX |
    |Cílová adresa | \<IP adresu > pro velké IP adresy |
    |Port služby | 443 |
    |Profil SSL (klient) | clientssl |
    |Překlad zdrojového adres | Automatická mapa |
        
    ![](./media/network-howto-f5/image14.png)

    ![](./media/network-howto-f5/image15.png)

14. Právě jste dokončili konfiguraci BIG-IP pro aplikaci NGINX. Chcete-li ověřit správné fungování, přejděte na lokalitu a ověřte statistiku F5.

15. Otevřete prohlížeč `https://<F5-public-VIP-IP>` a ujistěte se, že se zobrazí vaše výchozí stránka NGINX.

    ![](./media/network-howto-f5/image16.png)

16. Teď zkontrolujte statistiku svého virtuálního serveru a ověřte tok přenosů, a to tak, že přejdete na **statistika > statistiku modulu > místní provoz**.

17. V části **typ statistiky**vyberte **virtuální servery**.

    ![](./media/network-howto-f5/image17.png)


## <a name="for-more-information"></a>Další informace

Několik referenčních článků o použití F5 najdete v těchto článcích:

- [Služby dostupnosti datových center využívající službu DNS s velkým objemem IP adres](https://clouddocs.f5.com/training/community/dns/html/class3/class3.html)
- [Nasazení systému BIG-IP s aplikacemi HTTP](https://www.f5.com/content/dam/f5/corp/global/pdf/deployment-guides/iapp-http-dg.pdf)
- [Vytvoření sítě WAN pro GSLB](https://clouddocs.f5.com/training/community/big-iq-cloud-edition/html/class10/module2/lab1.html)

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md) 
