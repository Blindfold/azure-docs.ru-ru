---
title: "Поставщики услуг подключения и расположения Azure ExpressRoute | Документация Майкрософт"
description: "В этой статье приведена подробная информация о расположениях, где предлагаются услуги, и способах подключения к регионам Azure. Эта таблица отсортирована по поставщику услуг подключения."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 6f4e6629b6058eec292f13e236eba8a91679d5b4
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---
# <a name="expressroute-partners-and-peering-locations"></a>Партнеры и одноранговые расположения ExpressRoute

> [!div class="op_single_selector"]
> * [Расположения по поставщикам](expressroute-locations.md)
> * [Поставщики по расположению](expressroute-locations-providers.md)


В данной статье приведены таблицы со сведениями о поставщиках услуг подключения ExpressRoute, географическом покрытии ExpressRoute, облачных службах Майкрософт, поддерживаемых через ExpressRoute, и системных интеграторах ExpressRoute.

## <a name="partners"></a>Поставщики услуг подключения ExpressRoute
ExpressRoute поддерживается во всех регионах и расположениях Azure. На следующей карте обозначены регионы Azure и расположения ExpressRoute. Расположения ExpressRoute соответствуют тем территориям, где Майкрософт взаимодействует с несколькими одноранговыми поставщиками услуг.

![Карта расположения][0]

Вы сможете получить доступ ко всем службам Azure во всех регионах соответствующего геополитического региона, если вы подключены по меньшей мере к одному расположению ExpressRoute в этом регионе.

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>Регионы Azure с расположениями ExpressRoute в пределах геополитических регионов
В следующей таблице сопоставлены регионы Azure с расположениями ExpressRoute в пределах геополитических регионов.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- |
| **Северная Америка** |Восточная часть США, западная часть США, восточная часть США 2, западная часть США 2, центральная часть США, юго-центральная часть США, северо-центральная часть США, западно-центральная часть США, центральная часть Канады, восточная часть Канады |Атланта, Чикаго, Даллас, Денвер+, Лас-Вегас, Лос-Анджелес, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия), Монреаль, Квебек, Торонто |
| **Северная Америка** |Южная часть Бразилии |Сан-Паулу |
| **Европа** |Северная Европа, Западная Европа, запад Соединенного Королевства, юг Соединенного Королевства |Амстердам, Дублин, Лондон, Ньюпорт (Уэльс), Париж |
| **Азия** |Восточная Азия, Юго-Восточная Азия |Гонконг, Сингапур |
| **Япония** |Западная Япония, Восточная Япония |Осака, Токио |
| **Австралия** |Восточная Австралия, Юго-Восточная Австралия |Мельбурн, Сидней |
| **Индия** |Западная Индия, Центральная Индия, Южная Индия |Ченнаи, Мумбаи |
| **Южная Корея** |Центральная Корея, Южная Корея |Пусан, Сеул |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Регионы и геополитические границы для национальных облаков
В таблице ниже содержатся сведения о регионах и геополитических границах для национальных облаков.

| **Геополитический регион** | **Регионы Azure** | **Расположения ExpressRoute** |
| --- | --- | --- | --- |
| **Облако правительства США** |Айова (для обслуживания государственных организаций США), Виргиния (для обслуживания государственных организаций США), центральная часть США (US DoD), восточная часть США (US DoD)  |Чикаго, Даллас, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **Китай** |Северный Китай, Восточный Китай |Пекин, Шанхай |
| **Германия** |Центральная Германия, восточная Германия |Берлин, Франкфурт |

В стандартном номере SKU ExpressRoute подключение между геополитическими регионами не поддерживается. Для поддержки глобальных подключений необходимо включить надстройку ExpressRoute класса "Премиум". Подключение к национальным облачным средам не поддерживается. При необходимости вы можете работать с поставщиками услуг подключения.

## <a name="locations"></a>Расположения поставщиков услуг подключения

В следующей таблице показаны расположения по поставщикам услуг. См. дополнительные сведения о [службах доступных поставщиков по расположению](expressroute-locations-providers.md#locations).


### <a name="production-azure"></a>Рабочая среда Azure
| **Поставщик услуг** | **Microsoft Azure** | **Office 365 и CRM Online** | **Расположения** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **Airtel** | Скоро | Скоро | Ченнаи, Мумбаи |
| **[Aryaka Networks](http://www.aryaka.com/)** |Поддерживаются |Поддерживаются |Амстердам, Даллас, Гонконг, Кремниевая долина, Сингапур, Токио, Вашингтон (округ Колумбия) |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Даллас, Лондон, Кремниевая долина, Сингапур, Сидней, Вашингтон (округ Колумбия) |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Поддерживаются |Поддерживаются |Монреаль, Торонто |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Скоро |Скоро |Кремниевая долина |
| **China Telecom Global** |Поддерживаются |Не поддерживается |Гонконг |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Поддерживаются |Поддерживаются |Даллас, Монреаль, Торонто |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживаются |Поддерживаются |Амстердам, Дублин, Лондон, Токио |
| **Comcast** |Поддерживаются |Поддерживаются |Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **Console**| Поддерживаются | Поддерживаются |Кремниевая долина, Торонто |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Поддерживаются |Поддерживаются |Лос-Анджелес, Нью-Йорк |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Поддерживаются |Амстердам, Атланта, Чикаго, Даллас, Гонконг, Лондон, Лос-Анджелес, Мельбурн, Нью-Йорк, Осака, Париж+, Сан-Пауло, Сиэтл, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия), Торонто |
| **euNetworks** |Поддерживаются |Поддерживаются |Амстердам |
| **GÉANT** |Поддерживаются |Поддерживаются |Амстердам |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Поддерживаются| Поддерживаются | Ченнай |
| **[InterCloud](https://www.intercloud.com/)** |Поддерживаются |Поддерживаются |Амстердам, Лондон, Сингапур, Вашингтон (округ Колумбия) |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Поддерживаются |Поддерживаются |Осака, Токио |
| **Internet Solutions - Cloud Connect** |Поддерживаются |Поддерживаются |Амстердам, Лондон |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Поддерживаются |Поддерживаются |Амстердам, Лондон, Париж |
| **Jisc** |Поддерживаются |Поддерживаются |Лондон |
| **KINX** |Поддерживаются |Поддерживаются |Сеул |
| **[KPN](http://www.kpn.com/cloudconnect)** | Поддерживаются | Поддерживаются | Амстердам | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Даллас, Лас-Вегас+, Лондон, Сиэтл, Кремниевая долина, Сингапур, Вашингтон (округ Колумбия) |
| **LG CNS** |Поддерживаются |Поддерживаются |Пусан |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются |Поддерживаются |Даллас, Гонконг, Лас-Вегас, Лос-Анджелес, Мельбурн, Нью-Йорк, Квебек, Сиэтл, Сингапур, Сидней, Торонто, Вашингтон (округ Колумбия) |
| **MTN** |Поддерживаются |Поддерживаются |Лондон |
| **[Next Generation Data](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Поддерживаются |Поддерживаются |Ньюпорт (Уэльс) |
| **NEXTDC** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Поддерживаются |Поддерживаются |Лондон, Лос-Анджелес, Осака, Сингапур, Токио, Вашингтон (округ Колумбия) |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Поддерживаются |Поддерживаются |Амстердам, Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Вашингтон (округ Колумбия) |
| **PCCW Global Limited** |Поддерживаются |Поддерживаются |Гонконг |
| **Sejong Telecom** |Поддерживаются |Поддерживаются |Сеул |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Поддерживаются |Поддерживаются |Ченнай |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Поддерживаются |Поддерживаются |Сингапур |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Поддерживаются |Поддерживаются |Осака, Токио |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Поддерживаются |Поддерживаются |Амстердам, Ченнаи, Гонконг, Лондон, Мумбаи, Кремниевая долина, Сингапур, Вашингтон (округ Колумбия) |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Поддерживаются |Поддерживаются |Амстердам, Дублин, Лондон |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Поддерживаются |Поддерживаются |Амстердам+, Сан-Паулу |
| **[Telehouse — KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Поддерживаются |Поддерживаются |Лондон |
| **Telenor** |Поддерживаются |Поддерживаются |Амстердам, Лондон |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Поддерживаются |Поддерживаются |Мельбурн, Сидней |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Поддерживаются |Поддерживаются |Амстердам, Чикаго, Даллас, Гонконг, Лондон, Кремниевая долина, Сингапур, Сидней, Токио, Вашингтон (округ Колумбия) |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Поддерживаются |Не поддерживается |Лондон |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Поддерживаются |Поддерживаются |Чикаго, Даллас+, Лондон+, Лос-Анджелес, Нью-Йорк, Кремниевая долина, Торонто, Вашингтон (округ Колумбия) |

 **+** означает "скоро"

### <a name="national-cloud-environment"></a>Национальная облачная среда

### <a name="us-government-cloud"></a>Облако правительства США
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Поддерживаются |Поддерживаются |Чикаго, Вашингтон (округ Колумбия) |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Нью-Йорк, Сиэтл, Кремниевая долина, Вашингтон (округ Колумбия) |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Поддерживаются |Поддерживаются |Чикаго, Нью-Йорк+, Вашингтон (округ Колумбия) |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются | Поддерживаются | Чикаго, Даллас |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Поддерживаются |Поддерживаются |Чикаго, Даллас, Нью-Йорк, Вашингтон (округ Колумбия) |

### <a name="china"></a>Китай
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **China Telecom** |Поддерживаются |Не поддерживается |Пекин, Шанхай |

Дополнительные сведения см. на странице [ExpressRoute в Китае](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Германия
| **Поставщик услуг** | **Microsoft Azure** | **Office 365** | **Расположения** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Поддерживаются |Не поддерживается |Берлин+, Франкфурт |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Поддерживаются |Не поддерживается |Франкфурт |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Поддерживаются |Не поддерживается |Берлин |
| **Interxion** |Поддерживаются |Не поддерживается |Франкфурт |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Поддерживаются  | Не поддерживается | Берлин |

## <a name="c1partners"></a>Подключение через других поставщиков услуг
Вы можете создать подключение, даже если ваш поставщик услуг подключения не указан в предыдущих разделах.

* Узнайте у своего поставщика услуг подключения, подключен ли он к какому-либо Exchange, указанному в таблице выше. Дополнительные сведения об услугах, предлагаемых поставщиками Exchange, см. по ссылкам ниже. Несколько поставщиков услуг подключения уже подключены к серверам Ethernet Exchange.
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equnix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Обратитесь к своему поставщику услуг подключения, чтобы он расширил вашу сеть, добавив необходимое пиринговое расположение.
  * Убедитесь, что поставщик услуг подключения расширяет границы вашего подключения, сохраняя высокую доступность во избежание влияния единых точек отказа.
* Закажите канал ExpressRoute с Exchange у вашего поставщика услуг подключения для связи с Майкрософт.
  * Для настройки подключения выполните действия, описанные в статье [Создание и изменение канала ExpressRoute с помощью PowerShell](expressroute-howto-circuit-classic.md) .

| **Поставщик услуг подключения** | **Exchange** | **Расположения** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Сингапур |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Торонто, Монреаль |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Сиэтл; |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Токио |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Лондон |
| **[C3ntro](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Даллас |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Монреаль, Торонто |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Даллас |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Сингапур |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Амстердам |
| **[Exponential E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Лондон |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Амстердам |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Лондон, Слау |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |Нью-Йорк, Вашингтон (округ Колумбия) |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Сидней |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Вашингтон, округ Колумбия |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Лондон |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Амстердам, Франкфурт |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Франкфурт |  
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Лондон
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Даллас, Лос-Анджелес |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Чикаго, Кремниевая долина, Вашингтон, округ Колумбия |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Мадрид |


## <a name="expressroute-system-integrators"></a>Системные интеграторы ExpressRoute
Возможность частного подключения, соответствующего вашим потребностям, будет зависеть от масштаба сети. Чтобы упростить переход на ExpressRoute, вы можете обратиться к одному из системных интеграторов, указанных в таблице ниже.

| **Системный интегратор** | **Континент** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | Европа |
| **[Avanade Inc.](http://www.avanade.com/)** | Азия, Европа, Северная Америка, Южная Америка |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Европа
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** | Европа |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | Северная Америка |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Австралия |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Европа (Германия) |
| **[Nelite](http://nelite.com/)** | Европа |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Азия |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Европа |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Северная Америка |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | Северная Америка |
| **[Project Leadership](http://www.projectleadership.net/azure)** | Северная Америка |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | Европа |


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения об ExpressRoute см. в статье [Вопросы и ответы по ExpressRoute](expressroute-faqs.md).
* Убедитесь, что выполнены все необходимые условия. См. статью [Предварительные требования и контрольный список для ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Карта расположения"

