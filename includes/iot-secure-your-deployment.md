# <a name="secure-your-iot-deployment"></a>Защита развертывания IoT
В этой статье более подробно рассматривается защита инфраструктуры "Интернета вещей" (IoT) на основе Azure IoT. Она содержит ссылки на сведения о настройке и развертывании каждого компонента на уровне реализации. Кроме того, в статье представлены сравнения различных конкурирующих методов и рекомендации по их выбору.

Обеспечение безопасности развертывания Azure IoT можно разделить на следующие три области безопасности.

* **Безопасность устройства**: защита устройства IoT при его развертывании на практике.
* **Безопасность подключения**: обеспечение конфиденциальности и защиты от несанкционированного изменения всех данных, передаваемых между устройством IoT и центром IoT.
* **Безопасность в облаке**: предоставление средств для защиты данных, перемещаемых и хранимых в облаке.

![Три области безопасности][img-overview]

## <a name="secure-device-provisioning-and-authentication"></a>Безопасная подготовка и проверка подлинности устройств
Azure IoT Suite позволяет защитить устройства IoT следующими двумя методами:

* предоставляя уникальный ключ удостоверения (маркеры безопасности) для каждого устройства, который может использоваться им для взаимодействия с центром IoT;
* используя [сертификат X.509][lnk-x509] и закрытый ключ на устройстве для аутентификации устройства в Центре Интернета вещей. Этот метод аутентификации гарантирует, что закрытый ключ на устройстве никогда не будет известен вне этого устройства, что позволяет повысить уровень безопасности.

Метод маркеров безопасности обеспечивает аутентификацию каждого вызова центра IoT, отправленного устройством, за счет привязывания симметричного ключа к каждому вызову. Применение сертификатов X.509 позволяет аутентифицировать устройства IoT на физическом уровне при установлении подключения TLS. Метод на основе маркеров безопасности можно использовать без аутентификации на основе сертификатов X.509, являющейся менее защищенной схемой. Выбор между этими двумя методами в основном определяется требованиями к безопасности аутентификации устройств и наличием защищенного хранилища на устройстве (для безопасного хранения закрытого ключа).

## <a name="iot-hub-security-tokens"></a>Маркеры безопасности центра IoT
Центр IoT использует маркеры безопасности для аутентификации устройств и служб, чтобы избежать отправки ключей по сети. Кроме того, маркеры безопасности ограничены по времени и области действия. Пакеты SDK для Центра Интернета вещей Azure автоматически создают маркеры без специальной настройки. Однако в некоторых случаях требуется, чтобы пользователь напрямую создал и использовал маркеры. В их число входит непосредственное использование поверхностей MQTT, AMQP или HTTP либо реализация схемы службы маркеров.

Дополнительные сведения о структуре маркера безопасности и его использовании можно найти в следующих статьях:

* [Структура маркера безопасности][lnk-security-tokens]
* [Использование маркеров SAS в клиенте устройства][lnk-sas-tokens]

В каждом Центре Интернета вещей есть [реестр удостоверений][lnk-identity-registry], который можно использовать для создания в службе уникальных ресурсов для каждого устройства (например, очереди с сообщениями, отправляемыми из облака на устройство) и предоставления доступа к конечным точкам для устройств. Реестр удостоверений центра IoT обеспечивает безопасное хранение удостоверений устройств и ключей безопасности для решения. Отдельные удостоверения устройств или группы удостоверений можно включать в список разрешений или блокировок, обеспечивая таким образом полный контроль над доступом к устройству. В приведенных ниже статьях представлены сведения о структуре реестра удостоверений и поддерживаемых им операциях.

[Центр Интернета вещей поддерживает такие протоколы, как MQTT, AMQP и HTTP][lnk-protocols]. Каждый из этих протоколов по-разному используют маркеры безопасности, передаваемые из устройства IoT в центр IoT:

* AMQP: применяется защита на основе утверждений SASL PLAIN и AMQP ({policyName}@sas.root.{iothubName} в случае использования маркеров уровня центра или {deviceId} при использовании маркеров уровня устройства.
* MQTT: пакет CONNECT использует {ИД_устройства} в качестве значений {ИД_клиента}, {имя_Центра_Интернета_вещей}/{ИД_устройства} в поле **Имя пользователя** и маркер SAS в поле **Пароль**.
* HTTP: действительный маркер находится в заголовке запроса авторизации.

С помощью реестра удостоверений Центра Интернета вещей можно настроить контроль доступа и учетные данные безопасности для каждого устройства. Если в решение Интернета вещей уже вложены значительные средства для реализации [настраиваемого реестра удостоверений устройств и (или) схемы аутентификации][lnk-custom-auth], то с помощью службы маркеров его можно интегрировать в существующую инфраструктуру, использующую Центр Интернета вещей.

### <a name="x509-certificate-based-device-authentication"></a>Аутентификация устройств на основе сертификатов X.509
Использование [сертификатов X.509 на устройствах][lnk-use-x509] и связанных с ними пар закрытого и открытого ключей позволяет реализовать дополнительную аутентификацию на физическом уровне. Закрытый ключ безопасно хранится на устройстве и не может быть обнаружен за его пределами. Сертификат X.509 содержит сведения об устройстве, например идентификатор устройства и другие организационные сведения. Закрытый ключ используется для создания подписи сертификата.

Ниже приведена общая последовательность подготовки устройства:

* Привязка идентификатора к физическому устройству. Это может быть удостоверение устройства или сертификат X.509, привязанный к устройству во время его производства или введения в эксплуатацию.
* Создание соответствующей записи в реестре удостоверений Центра Интернета вещей, содержащей удостоверение устройства и сведения об устройстве.
* Безопасное хранение отпечатка сертификата X.509 в реестре устройств Центра Интернета вещей.

### <a name="root-certificate-on-device"></a>Корневой сертификат на устройстве
Во время установления безопасного подключения TLS к центру IoT устройство IoT аутентифицирует центр IoT с помощью корневого сертификата, который является частью пакета SDK для устройств. Сертификат клиентского пакета SDK для C находится в папке \\c\\certs в корне репозитория. Хотя эти корневые сертификаты действуют продолжительное время, срок их действия может истечь или они могут быть отозваны. Если нет возможности обновить сертификат на устройстве, вследствие этого устройство не сможет подключиться к центру IoT (или другой облачной службе). Наличие средств обновления корневого сертификата после развертывания устройства IoT позволит эффективно уменьшить этот риск.

## <a name="securing-the-connection"></a>Защита подключения
Подключение через Интернет между устройством IoT и центром IoT защищается с помощью протокола TLS. Azure IoT поддерживает протоколы [TLS 1.2][lnk-tls12], TLS 1.1 и TLS 1.0 (в указанном порядке приоритетности). Поддержка TLS 1.0 предоставляется только для обеспечения обратной совместимости. Рекомендуется использовать протокол TLS 1.2, так как он обеспечивает наиболее надежную защиту.

Azure IoT Suite поддерживает приведенные ниже комплекты шифров в указанном порядке.

| Комплект шифров | Длина |
| --- | --- |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (эквивалент 7680-разрядного алгоритма RSA) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (эквивалент 3072-разрядного алгоритма RSA) FS |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (эквивалент 7680-разрядного алгоритма RSA) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (эквивалент 3072-разрядного алгоритма RSA) FS |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="securing-the-cloud"></a>Защита облака
Центр Интернета вещей Azure дает возможность определить [политики контроля доступа][lnk-protocols] для каждого ключа безопасности. В нем используется приведенный ниже набор разрешений для предоставления доступа к каждой из конечных точек центра IoT. Разрешения ограничивают доступ к центру IoT на основе функций.

* **RegistryRead**. Предоставляет доступ на чтение к реестру удостоверений. Дополнительные сведения см. в разделе о [реестре удостоверений][lnk-identity-registry].
* **RegistryReadWrite**. Предоставляет доступ на чтение и запись к реестру удостоверений. Дополнительные сведения см. в разделе о [реестре удостоверений][lnk-identity-registry].
* **ServiceConnect**. Предоставляет доступ к конечным точкам обмена данными и мониторинга, обращенным к облачной службе. Например, это разрешение позволяет серверным облачным службам получать сообщения с устройств, отправлять сообщения в устройства и получать соответствующие уведомления о доставке.
* **DeviceConnect**. Предоставляет доступ к конечным точкам для устройств. Например, это разрешение позволяет отправлять сообщения с устройства в облако и получать сообщения, отправленные из облака на устройство. Это разрешение используют устройства.

Существует два способа получения разрешений **DeviceConnect** для Центра Интернета вещей с использованием [маркеров безопасности][lnk-sas-tokens]: с помощью ключа удостоверения устройства или общего ключа доступа. Кроме того, следует отметить, что все функциональные возможности, доступные с устройств, намеренно предоставляются в конечных точках с префиксом `/devices/{deviceId}`.

[Компоненты службы могут создавать маркеры безопасности только][lnk-service-tokens] с помощью политик общего доступа, предоставляющих соответствующие разрешения.

Центр IoT Azure и другие службы, которые могут быть частью решения, обеспечивают управление пользователями с помощью Azure Active Directory.

Данные, принимаемые Центром Интернета вещей Azure, могут использоваться различными службами, в том числе службой Azure Stream Analytics, хранилищем BLOB-объектов и т. д. Эти службы предоставляют доступ к управлению. Ниже приведены дополнительные сведения об этих службах и доступных вариантах.

* [Azure DocumentDB][lnk-docdb] — служба масштабируемых и полностью индексированных баз данных для частично структурированных данных, которая управляет метаданными для подготавливаемых устройств: атрибутами, конфигурацией и параметрами безопасности. DocumentDB обеспечивает высокую производительность и пропускную способность при обработке, схемонезависимое индексирование данных, а также полнофункциональный интерфейс SQL-запросов.
* [Azure Stream Analytics][lnk-asa] — потоковая обработка в режиме реального времени обеспечивает быстрое развертывание и внедрение бюджетных аналитических решений для анализа актуальных данных, передаваемых устройствами, датчиками, инфраструктурой и приложениями. Данные, поступающие из этой полностью управляемой службы, можно масштабировать до любого объема, поддерживая высокую пропускную способность, небольшую задержку и устойчивость.
* [Служба приложений Azure][lnk-appservices] — облачная платформа, предназначенная для создания мощных мобильных приложений и веб-приложений, которые могут подключаться к данным в любом расположении, в облаке или локально. Создавайте привлекательные мобильные приложения для iOS, Android и Windows. Выполняйте интеграцию с приложениями SaaS (приложения как услуга) и корпоративными приложениями со встроенной возможностью подключения при запуске к десяткам разных облачных служб и корпоративных приложений. Программируйте на предпочитаемом языке в интегрированной среде разработки (.NET, NodeJS, PHP, Python или Java), чтобы создавать веб-приложения и интерфейсы API еще быстрее.
* [Logic Apps][lnk-logicapps] — этот компонент службы приложений Azure позволяет интегрировать решения Интернета вещей в существующие бизнес-системы для автоматизации рабочих процессов. Приложения логики позволяют разработчикам проектировать запускаемые с помощью триггера рабочие процессы, а затем выполнять ряд действий (включая правила), в ходе которых используются мощные соединители для интеграции с бизнес-процессами. В приложениях логики реализована встроенная возможность подключения к обширной экосистеме SaaS, включающей облачные и локальные приложения.
* [Хранилище BLOB-объектов Azure][lnk-blob] — надежное и экономичное облачное хранилище для данных, отправляемых устройствами в облако.

## <a name="conclusion"></a>Заключение
В этой статье приведены общие сведения на уровне реализации о проектировании и развертывании инфраструктуры IoT с помощью Azure IoT. Настройка каждого компонента, позволяющая обеспечить его безопасность, является ключом к защите всей инфраструктуры IoT. Варианты проектирования, доступные в Azure IoT, обеспечивают определенный уровень гибкости и выбора, однако каждый из них может влиять на безопасность. Рекомендуется использовать каждый из этих вариантов, предварительно оценив затраты и риски.

[img-overview]: media/iot-secure-your-deployment/overview.png

[lnk-security-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure
[lnk-sas-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-identity-registry]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-protocols]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-custom-auth]: ../articles/iot-hub/iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: http://www.itu.int/rec/T-REC-X.509-201210-I/en
[lnk-use-x509]: ../articles/iot-hub/iot-hub-devguide-security.md
[lnk-tls12]: https://tools.ietf.org/html/rfc5246
[lnk-service-tokens]: ../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components
[lnk-docdb]: https://azure.microsoft.com/services/documentdb/
[lnk-asa]: https://azure.microsoft.com/services/stream-analytics/
[lnk-appservices]: https://azure.microsoft.com/services/app-service/
[lnk-logicapps]: https://azure.microsoft.com/services/app-service/logic/
[lnk-blob]: https://azure.microsoft.com/services/storage/
