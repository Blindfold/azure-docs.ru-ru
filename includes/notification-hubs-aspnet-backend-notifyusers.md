## <a name="create-the-webapi-project"></a>Создание проекта веб-интерфейса API
В следующих разделах будет создаваться новый серверный веб-API ASP.NET. Он будет иметь три основные функции:

1. **Аутентификация клиентов**: позже будет добавлен обработчик сообщений для аутентификации клиентских запросов и связывания пользователя с запросом.
2. **Регистрация клиентов для получения уведомлений**: позже будет добавлен контроллер для обработки новых регистраций клиентских устройств для получения уведомлений. Имя пользователя, прошедшего аутентификацию, будет автоматически добавляться в регистрацию как [тег](https://msdn.microsoft.com/library/azure/dn530749.aspx).
3. **Отправка уведомлений клиентам**: затем также будет добавлен контроллер, дающий пользователю возможность инициировать отправку безопасных push-уведомлений устройствам и клиентам, связанным с тегом. 

Следующие шаги демонстрируют, как создать новую серверную часть веб-API ASP.NET: 

> [!NOTE]
> **Важно!**Перед тем как начать работу с этим учебником, убедитесь, что у вас установлена последняя версия диспетчера пакетов NuGet. Чтобы проверить, запустите Visual Studio. В меню **Средства** выберите пункт **Расширения и обновления**. Найдите **Диспетчер пакетов NuGet для Visual Studio 2013**и убедитесь, что у вас версия 2.8.50313.46 или более поздняя. Если это не так, переустановите диспетчер пакетов NuGet.
> 
> ![][B4]
> 
> [!NOTE]
> Убедитесь, что вы установили [пакет SDK для Azure](https://azure.microsoft.com/downloads/) для Visual Studio, используемый для развертывания веб-сайта.
> 
> 

1. Запустите Visual Studio или Visual Studio Express. Щелкните **Обозреватель серверов** и выполните вход в свою учетную запись Azure. Чтобы система Visual Studio могла создать ресурсы веб-сайта в вашей учетной записи, вы должны выполнить вход.
2. В Visual Studio щелкните **Файл**, **Создать**, **Проект**, откройте **Шаблоны**, **Visual C#**, выберите пункт **Веб-приложения** и **Веб-приложение ASP.NET**, введите имя **AppBackend** и нажмите кнопку **ОК**. 
   
    ![][B1]
3. В диалоговом окне **Новый проект ASP.NET** выберите **Веб-интерфейс API** и нажмите кнопку **ОК**.
   
    ![][B2]
4. В диалоговом окне **Настройка веб-приложения Microsoft Azure** выберите подписку и созданный **план службы приложений**. В этом диалоговом окне также можно выбрать элемент **Создать новый план служб приложений** и создать нужный план. База данных для этого руководства не требуется. После выбора плана служб приложения нажмите кнопку **ОК** для создания проекта.
   
    ![][B5]

## <a name="authenticating-clients-to-the-webapi-backend"></a>Аутентификация клиентов в серверной части веб-API
В этом разделе вы создадите новый класс обработчика сообщений с именем **AuthenticationTestHandler** для новой серверной части. Этот класс является производным от [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) и добавлен в качестве обработчика сообщений, чтобы он мог обрабатывать все запросы, поступающие в серверную часть. 

1. В обозревателе решений щелкните правой кнопкой мыши проект **AppBackend**, выберите **Добавить**, а затем щелкните **Класс**. Присвойте новому классу имя **AuthenticationTestHandler.cs** и нажмите кнопку **Добавить**, чтобы создать класс. В целях упрощения процедуры этот класс используется для аутентификации пользователей с помощью *базовой проверки подлинности* . Обратите внимание, что ваше приложение может использовать любую схему проверки подлинности.
2. В AuthenticationTestHandler.cs добавьте следующие операторы `using` :
   
        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Web;
3. В AuthenticationTestHandler.cs замените определение класса `AuthenticationTestHandler` следующим кодом. 
   
    Этот обработчик авторизует запрос, если выполнены все три следующих условия.
   
   * Запрос, включен в заголовок *авторизации* . 
   * Запрос использует *обычную* проверку подлинности. 
   * Строка имени пользователя и строка пароля являются одной стройкой.
     
     В противном случае запрос будет отклонен. Этот подход нельзя назвать настоящим методом аутентификации и авторизации. Это очень простой пример для этого учебника.
     
     Если сообщение запроса аутентифицируется и авторизуется `AuthenticationTestHandler`, то пользователь обычной проверки подлинности будет подключен к текущему запросу в [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx). Позднее информацию о пользователе в HttpContext будет использовать другой контроллер (RegisterController), чтобы добавить [тег](https://msdn.microsoft.com/library/azure/dn530749.aspx) в запрос регистрации для получения уведомлений.
     
       public class AuthenticationTestHandler : DelegatingHandler   {
     
           protected override Task<HttpResponseMessage> SendAsync(
           HttpRequestMessage request, CancellationToken cancellationToken)
           {
               var authorizationHeader = request.Headers.GetValues("Authorization").First();
     
               if (authorizationHeader != null && authorizationHeader
                   .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
               {
                   string authorizationUserAndPwdBase64 =
                       authorizationHeader.Substring("Basic ".Length);
                   string authorizationUserAndPwd = Encoding.Default
                       .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                   string user = authorizationUserAndPwd.Split(':')[0];
                   string password = authorizationUserAndPwd.Split(':')[1];
     
                   if (verifyUserAndPwd(user, password))
                   {
                       // Attach the new principal object to the current HttpContext object
                       HttpContext.Current.User =
                           new GenericPrincipal(new GenericIdentity(user), new string[0]);
                       System.Threading.Thread.CurrentPrincipal =
                           System.Web.HttpContext.Current.User;
                   }
                   else return Unauthorized();
               }
               else return Unauthorized();
     
               return base.SendAsync(request, cancellationToken);
           }
     
           private bool verifyUserAndPwd(string user, string password)
           {
               // This is not a real authentication scheme.
               return user == password;
           }
     
           private Task<HttpResponseMessage> Unauthorized()
           {
               var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
               var tsc = new TaskCompletionSource<HttpResponseMessage>();
               tsc.SetResult(response);
               return tsc.Task;
           }
       }
     
     > [!NOTE]
     > **Примечание о безопасности.** Класс `AuthenticationTestHandler` не обеспечивает настоящую аутентификацию. Этот класс используется лишь для имитации базовой проверки подлинности и не является безопасным. В реальных приложениях и службах необходимо реализовать безопасный механизм проверки подлинности.                
     > 
     > 
4. Чтобы зарегистрировать обработчик событий, добавьте в конец метода `Register` в классе **App_Start/WebApiConfig.cs** следующий код:
   
        config.MessageHandlers.Add(new AuthenticationTestHandler());
5. Сохраните изменения.

## <a name="registering-for-notifications-using-the-webapi-backend"></a>Регистрация для получения уведомлений с помощью серверной части веб-API
В этом разделе мы добавим новый контроллер в серверный веб-API, который будет обрабатывать запросы регистрации пользователя и устройства на получение уведомлений с помощью клиентской библиотеки для Центров уведомлений. Контроллер будет добавлять тег пользователя для пользователя, который прошел аутентификацию и подключен к HttpContext с помощью `AuthenticationTestHandler`. Тег будет в виде строки: `"username:<actual username>"`.

1. В обозревателе решений щелкните правой кнопкой мыши проект **AppBackend** и выберите **Управление пакетами NuGet**.
2. В левой части окна выберите **В сети** и найдите пакет **Microsoft.Azure.NotificationHubs**, задав это значение в поле **Поиск**.
3. В списке результатов щелкните **Центры уведомлений Microsoft Azure**, а затем нажмите кнопку **Установить**. Завершите установку и закройте окно диспетчера пакетов NuGet.
   
    После этого будет добавлена ссылка на пакет SDK для Центров уведомлений Azure с помощью <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">пакета NuGet Microsoft.Azure.Notification Hubs</a>.
4. Теперь создадим новый файл класса, представляющий подключение к концентратору уведомлений, который используется для отправки уведомлений. В обозревателе решений щелкните правой кнопкой мыши папку **Модели**, выберите **Добавить**, а затем — **Класс**. Назовите новый класс **Notifications.cs** и нажмите кнопку **Добавить** для создания класса. 
   
    ![][B6]
5. В Notifications.cs добавьте следующий оператор `using` в начало файла:
   
        using Microsoft.Azure.NotificationHubs;
6. Замените определение класса `Notifications` указанным ниже и замените два заполнителя строкой подключения (с полным доступом) для своего центра уведомлений и именем центра (доступно на [классическом портале Azure](http://manage.windowsazure.com)):
   
        public class Notifications
        {
            public static Notifications Instance = new Notifications();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }
7. Далее мы создадим новый контроллер с именем **RegisterController**. В обозревателе решений щелкните правой кнопкой мыши папку **Контроллеры**, нажмите кнопку **Добавить** и выберите **Контроллер**. Щелкните элемент **Контроллер веб-интерфейса API 2 — пустой** и нажмите кнопку **Добавить**. Назовите новый класс **RegisterController** и снова щелкните **Добавить**, чтобы создать контроллер.
   
    ![][B7]
   
    ![][B8]
8. В RegiterController.cs добавьте следующие операторы `using` :
   
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
9. Добавьте в определение класса `RegisterController` следующий код: Обратите внимание, что в этом коде мы добавьте тег пользователя для пользователя, который подключен к HttpContext. Пользователь был аутентифицирован и подключен к HttpContext фильтром сообщений, который мы добавили, `AuthenticationTestHandler`. Вы можете также добавить дополнительные проверки, чтобы убедиться, что у пользователя есть право регистрации запрошенных тегов.
   
        private NotificationHubClient hub;
   
        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }
   
        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }
   
        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            string newRegistrationId = null;
   
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);
   
                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }
   
            if (newRegistrationId == null) 
                newRegistrationId = await hub.CreateRegistrationIdAsync();
   
            return newRegistrationId;
        }
   
        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }
   
            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;
   
            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);
   
            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }
10. Сохраните изменения.

## <a name="sending-notifications-from-the-webapi-backend"></a>Отправка уведомлений из серверной части веб-API
В этом разделе вы добавите новый контроллер, который предоставляет клиентским устройствам возможность отправки уведомления, основанного на теге имени пользователя, с помощью библиотеки управления службой Центров уведомлений Azure в серверной части веб-API ASP.NET.

1. Создайте еще один новый контроллер с именем **NotificationsController**. Создайте его так же, как создали **RegisterController** в предыдущем разделе.
2. В NotificationsController.cs добавьте следующие операторы `using` :
   
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
3. Добавьте следующий метод в класс **NotificationsController** :
   
    Этот код отправляет тип уведомлений, основанный на параметре `pns` Службы уведомлений платформы (PNS). Значение `to_tag` используется для задания тега *имени пользователя* в сообщении. Этот тег должен соответствовать тегу имени пользователя активной регистрации центра уведомлений. Сообщение уведомления извлекается из текста запроса POST и форматируется для целевого PNS. 
   
    Поддержка форматов уведомлений зависит от того, какую службу отправки уведомлений платформы (PNS) используют поддерживаемые приложения. Например, на устройствах Windows можно использовать [всплывающие уведомления с помощью WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) , которые не поддерживаются другой PNS. В связи с этим серверная часть решения конвертирует уведомление в формат, соответствующий PNS устройств, которые вы собираетесь поддерживать. Затем воспользуйтесь соответствующим API отправки для [класса NotificationHubClient](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)
   
        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;
   
            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;
   
            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }
   
            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }
   
            return Request.CreateResponse(ret);
        }
4. Нажмите клавишу **F5** , чтобы запустить приложение и убедиться в правильности своих действий до сих пор. Приложение должно запустить браузер и отобразить домашнюю страницу ASP.NET. 

## <a name="publish-the-new-webapi-backend"></a>Публикация новой серверной части веб-API
1. После этого развернем это приложение на веб-сайте Azure, чтобы сделать его доступным для всех устройств. Щелкните правой кнопкой мыши проект **AppBackend** и нажмите кнопку **Опубликовать**.
2. Выберите **Веб-приложения Microsoft Azure** в качестве цели публикации.
   
    ![][B15]
3. Войдите в учетную запись Azure и выберите существующее или новое веб-приложение.
   
    ![][B16]
4. Запишите свойство **URL-адрес назначения** с вкладки **Подключение**. Далее в учебнике этот URL-адрес будет называться *конечная точка сервера* . Щелкните **Опубликовать**.
   
    ![][B18]

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
