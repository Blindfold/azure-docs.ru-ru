---
title: "Azure Active Directory B2C | Документация Майкрософт"
description: "Сведения по созданию веб-приложений, позволяющих пользователям выполнять вход, изменять профиль, регистрироваться и сбрасывать пароль с помощью Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: parakhj
manager: krassk
editor: 
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/17/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 87b8b91fc5970bd127dfdc47e24d99a19471aa8c
ms.contentlocale: ru-ru
ms.lasthandoff: 05/03/2017


---
# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD B2C: регистрация и вход в систему в веб-приложении ASP.NET

Azure AD B2C позволяет добавлять в веб-приложения мощные функции для управления удостоверениями. В этой статье описывается, как создать веб-приложение ASP.NET, которое предусматривает регистрацию и вход пользователя, изменение профиля, а также сброс пароля.

## <a name="create-an-azure-ad-b2c-directory"></a>Создание каталога Azure AD B2C

Перед использованием Azure AD B2C необходимо создать каталог или клиент. Каталог — это контейнер для данных всех ваших пользователей, приложений, групп и т. д. Прежде чем продолжать работу с руководством, [создайте каталог B2C](active-directory-b2c-get-started.md), если вы его еще не создали.

## <a name="create-an-application"></a>Создание приложения

Теперь необходимо создать приложение в каталоге B2C. Это дает Azure AD информацию, необходимую для безопасного взаимодействия с вашим приложением. Чтобы создать приложение, следуйте [этим инструкциям](active-directory-b2c-app-registration.md). Не забудьте сделать следующее.

* Включите в приложение **веб-приложение или веб-API** .
* Укажите `https://localhost:44316/` в качестве **URI перенаправления**. Это URL-адрес по умолчанию для данного примера кода.
* Скопируйте **идентификатор приложения** , назначенный приложению.  Оно понадобится вам позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Создание политик

В Azure AD B2C любое взаимодействие с пользователем определяется [политикой](active-directory-b2c-reference-policies.md). Этот пример кода включает три способа идентификации: **регистрацию и вход в систему**, **изменение профиля**, а также **сброс пароля**.  Вам нужно создать по одной политике каждого типа, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md). При создании политик обязательно сделайте следующее:

* В колонке поставщиков удостоверений выберите элемент **User ID sign-up** (Регистрация с помощью идентификатора пользователя) или **Email sign-up** (Регистрация по электронной почте).
* В политике регистрации и входа в систему выберите атрибут **Отображаемое имя** и другие атрибуты регистрации.
* В каждой политике в качестве утверждения приложения выберите утверждение **Отображаемое имя** . Можно также выбрать другие утверждения.
* Скопируйте **имя** каждой политики после ее создания. Имена политик понадобятся вам позднее.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

После создания политик можно приступать к сборке приложения.

## <a name="download-the-code"></a>Загрузка кода

Код примеров для этого руководства размещен на портале [GitHub](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi). Вы можете клонировать пример, выполнив такую команду:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Скачав пример кода, откройте SLN-файл Visual Studio, чтобы начать работу. Теперь решение содержит два проекта: `TaskWebApp` и `TaskService`. `TaskWebApp` — это веб-приложение MVC, с которым взаимодействует пользователь. `TaskService` — веб-API серверной части приложения, в котором хранится список дел для каждого пользователя. В этой статье рассматривается только приложение `TaskWebApp`. Сведения о создании `TaskService` с помощью Azure AD B2C см. в [этом руководстве по веб-приложениям .NET](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="update-the-azure-ad-b2c-configuration"></a>Обновление конфигурации Azure AD B2C

В нашем примере настроено использование политик и идентификатора демонстрационного клиента. Если вы хотите использовать собственный клиент, необходимо открыть `web.config` в проекте `TaskWebApp` и заменить следующие значения:

* `ida:Tenant` именем своего клиента;
* `ida:ClientId` идентификатором клиента для веб-приложения;
* `ida:ClientSecret` секретным ключом веб-приложения;
* `ida:SignUpSignInPolicyId` именем политики регистрации и входа в систему;
* `ida:EditProfilePolicyId` именем политики "Изменение профиля";
* `ida:ResetPasswordPolicyId` именем политики "Сброс профиля".

## <a name="add-authentication-support"></a>Добавление поддержки проверки подлинности

Теперь можно настроить приложение для использования Azure AD B2C. Приложение взаимодействует с Azure AD B2C, отправляя запросы проверки подлинности по протоколу OpenID Connect. В запросах задана политика, регулирующая взаимодействие пользователя с приложением. Вы можете использовать библиотеку OWIN от Майкрософт. С ее помощью можно отправлять запросы, выполнять политики, управлять сеансом пользователя и решать другие задачи.

### <a name="install-owin"></a>Установка OWIN

Сначала добавьте пакеты NuGet ПО промежуточного слоя OWIN в проект с помощью консоли диспетчера пакетов Visual Studio.

```Console
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="add-an-owin-startup-class"></a>Добавление класса запуска OWIN

Добавьте класс запуска OWIN в API с именем `Startup.cs`.  Щелкните проект правой кнопкой мыши и выберите **Добавить** и **Новый элемент**, после чего найдите OWIN. При запуске вашего приложения промежуточный слой OWIN вызовет метод `Configuration(…)` .

В нашем примере мы изменили объявление класса на `public partial class Startup` и реализовали другую часть класса в `App_Start\Startup.Auth.cs`. Внутри метода `Configuration` мы добавили вызов `ConfigureAuth`, определенный в `Startup.Auth.cs`. После внесения изменений `Startup.cs` выглядит следующим образом:

```CSharp
// Startup.cs

public partial class Startup
{
    // The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        // ConfigureAuth defined in other part of the class
        ConfigureAuth(app);
    }
}
```

### <a name="configure-the-authentication-middleware"></a>Настройка ПО промежуточного слоя для проверки подлинности

Откройте файл `App_Start\Startup.Auth.cs` и реализуйте метод `ConfigureAuth(...)`. Параметры, указанные в разделе `OpenIdConnectAuthenticationOptions`, будут служить координатами приложения для взаимодействия с Azure AD B2C. Если не задать определенные параметры, будут использоваться значения по умолчанию. Например, в примере не был указан параметр `ResponseType`, поэтому для каждого исходящего запроса к Azure AD B2C будет использоваться значение по умолчанию `code id_token`.

Также будет необходимо настроить проверку подлинности для файлов Cookie — промежуточный слой OpenID Connect использует файлы cookie для поддержки сеанса пользователя и выполнения других задач.

```CSharp
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // Initialize variables ...

    // Configure the OWIN middleware
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseCookieAuthentication(new CookieAuthenticationOptions());
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseOpenIdConnectAuthentication(
            new OpenIdConnectAuthenticationOptions
            {
                // Generate the metadata address using the tenant and policy information
                MetadataAddress = String.Format(AadInstance, Tenant, DefaultPolicy),

                // These are standard OpenID Connect parameters, with values pulled from web.config
                ClientId = ClientId,
                RedirectUri = RedirectUri,
                PostLogoutRedirectUri = RedirectUri,

                // Specify the callbacks for each type of notifications
                Notifications = new OpenIdConnectAuthenticationNotifications
                {
                    RedirectToIdentityProvider = OnRedirectToIdentityProvider,
                    AuthorizationCodeReceived = OnAuthorizationCodeReceived,
                    AuthenticationFailed = OnAuthenticationFailed,
                },

                // Specify the claims to validate
                TokenValidationParameters = new TokenValidationParameters
                {
                    NameClaimType = "name"
                },

                // Specify the scope by appending all of the scopes requested into one string (seperated by a blank space)
                Scope = $"{OpenIdConnectScopes.OpenId} {ReadTasksScope} {WriteTasksScope}"
            }
        );
    }

    // Implement the "Notification" methods...
}
```

В приведенном выше разделе `OpenIdConnectAuthenticationOptions` мы определили набор функций обратного вызова для специальных уведомлений, получаемых с помощью ПО промежуточного слоя OpenID Connect. Эти поведения определяются с помощью объекта `OpenIdConnectAuthenticationNotifications` и хранятся в переменной `Notifications`. В этом примере мы определяем три различных обратных вызова в зависимости от события.

#### <a name="using-different-policies"></a>Использование различных политик

При каждом запросе к Azure AD B2C инициируется уведомление `RedirectToIdentityProvider`. Если требуется использовать другую политику, в функции обратного вызова `OnRedirectToIdentityProvider` нужно проверять исходящий вызов. Чтобы сбросить пароль или изменить профиль, необходимо использовать соответствующую политику, например политику сброса паролей, вместо политики регистрации или входа, заданной по умолчанию.

В этом примере, если пользователь хочет сбросить пароль или изменить профиль, мы добавляем политику, которую рекомендуется использовать в контексте OWIN. Для этого необходимо сделать следующее:

```CSharp
    // Let the middleware know you are trying to use the edit profile policy
    HttpContext.GetOwinContext().Set("Policy", EditProfilePolicyId);
```

Вы можете реализовать функцию обратного вызова `OnRedirectToIdentityProvider`, выполнив следующую команду:

```CSharp
/*
*  On each call to Azure AD B2C, check if a policy (e.g. the profile edit or password reset policy) has been specified in the OWIN context.
*  If so, use that policy when making the call. Also, don't request a code (since it won't be needed).
*/
private Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    var policy = notification.OwinContext.Get<string>("Policy");

    if (!string.IsNullOrEmpty(policy) && !policy.Equals(DefaultPolicy))
    {
        notification.ProtocolMessage.Scope = OpenIdConnectScopes.OpenId;
        notification.ProtocolMessage.ResponseType = OpenIdConnectResponseTypes.IdToken;
        notification.ProtocolMessage.IssuerAddress = notification.ProtocolMessage.IssuerAddress.Replace(DefaultPolicy, policy);
    }

    return Task.FromResult(0);
}
```

#### <a name="handling-authorization-codes"></a>Обработка кодов авторизации

При получении кода авторизации инициируется уведомление `AuthorizationCodeReceived`. ПО промежуточного слоя OpenID Connect не поддерживает обмен кодов на маркеры доступа. Вы можете вручную выполнить обмен кода на маркер в функции обратного вызова. Дополнительные сведения см. в [этой документации](active-directory-b2c-devquickstarts-web-api-dotnet.md).

#### <a name="handling-errors"></a>Обработка ошибок

При сбое проверки подлинности инициируется уведомление `AuthenticationFailed`. С помощью метода обратного вызова можно обрабатывать ошибки по своему усмотрению. Тем не менее необходимо добавить проверку кода ошибки `AADB2C90118`. Во время выполнения политики регистрации и входа пользователь имеет возможность щелкнуть ссылку **Забыли пароль?**. В этом случае Azure AD B2C отправит код ошибки, указывающий, что приложение должно выполнить запрос с помощью политики сброса пароля.

```CSharp
/*
* Catch any failures received by the authentication middleware and handle appropriately
*/
private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    // Handle the error code that Azure AD B2C throws when trying to reset a password from the login page
    // because password reset is not supported by a "sign-up or sign-in policy"
    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```

## <a name="send-authentication-requests-to-azure-ad"></a>Отправка запросов на проверку подлинности в Azure AD

Теперь приложение настроено на взаимодействие с Azure AD B2C с использованием протокола проверки подлинности OpenID Connect. OWIN полностью возьмет на себя выполнение процессов создания сообщений проверки подлинности, проверки маркеров из Azure AD B2C и поддержки сеанса пользователя. Осталось инициировать все потоки пользователей.

Когда пользователь выбирает **Регистрация или Вход**, **Изменить профиль** или **Сброс пароля** в веб-приложении, в `Controllers\AccountController.cs` вызывается соответствующее действие:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign up or sign in
*/
public void SignUpSignIn()
{
    // Use the default policy to process the sign up / sign in flow
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge();
        return;
    }

    Response.Redirect("/");
}

/*
*  Called when requesting to edit a profile
*/
public void EditProfile()
{
    if (Request.IsAuthenticated)
    {
        // Let the middleware know you are trying to use the edit profile policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
        HttpContext.GetOwinContext().Set("Policy", Startup.EditProfilePolicyId);

        // Set the page to redirect to after editing the profile
        var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
        HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

        return;
    }

    Response.Redirect("/");

}

/*
*  Called when requesting to reset a password
*/
public void ResetPassword()
{
    // Let the middleware know you are trying to use the reset password policy (see OnRedirectToIdentityProvider in Startup.Auth.cs)
    HttpContext.GetOwinContext().Set("Policy", Startup.ResetPasswordPolicyId);

    // Set the page to redirect to after changing passwords
    var authenticationProperties = new AuthenticationProperties { RedirectUri = "/" };
    HttpContext.GetOwinContext().Authentication.Challenge(authenticationProperties);

    return;
}
```

Для выхода пользователя из приложения также можно использовать OWIN. В `Controllers\AccountController.cs` у нас есть следующее:

```CSharp
// Controllers\AccountController.cs

/*
*  Called when requesting to sign out
*/
public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

Помимо явного вызова политики, можно использовать тег `[Authorize]` в контроллерах, что приведет к выполнению политики, если пользователь не выполнил вход. Откройте файл `Controllers\HomeController.cs` и добавьте тег `[Authorize]` в контроллер утверждений.  OWIN выберет последнюю настроенную политику при вызове тега `[Authorize]` .

```CSharp
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

## <a name="display-user-information"></a>Отображение сведений о пользователе

При проверке подлинности пользователей с помощью OpenID Connect служба Azure AD B2C возвращает в приложение маркер идентификатора, который содержит **утверждения**. о пользователе. Эти утверждения можно использовать для персонализации приложения.

Откройте файл `Controllers\HomeController.cs` . Для доступа к утверждениям о пользователе в своих контроллерах можно использовать объект субъекта безопасности `ClaimsPrincipal.Current` .

```CSharp
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Вы можете открыть любое утверждение, которое ваше приложение получает таким же образом.  Список всех получаемых приложением утверждений можно найти на странице **Утверждения** .

## <a name="run-the-sample-app"></a>Запуск примера приложения

Приложение готово к сбору и запуску. Зарегистрируйтесь в приложении с использованием адреса электронной почты или имени пользователя. Выйдите и снова войдите под именем того же пользователя. Измените профиль или сбросьте пароль. Выйдите и зарегистрируйтесь от имени другого пользователя. Обратите внимание, что информация на вкладке **Утверждения** соответствует сведениям, настроенным в политиках.

## <a name="add-social-idps"></a>Добавление поставщиков удостоверений социальных сетей

В настоящее время приложение поддерживает регистрацию и вход пользователей только с использованием **локальных учетных записей**. Учетные записи хранятся в каталоге B2C, где применяется имя пользователя и пароль. С помощью Azure AD B2C можно добавить поддержку для других **поставщиков удостоверений** (IDP), не изменяя код.

Чтобы добавить в приложение поставщиков удостоверений социальных сетей, следуйте подробным инструкциям, приведенным в указанных ниже статьях. Для каждого поставщика удостоверений, поддержку которого нужно добавить, необходимо зарегистрировать приложение в соответствующей системе и получить идентификатор клиента.

* [Настройка Facebook как поставщика удостоверений](active-directory-b2c-setup-fb-app.md)
* [Настройка Google как поставщика удостоверений](active-directory-b2c-setup-goog-app.md)
* [Настройка Amazon как поставщика удостоверений](active-directory-b2c-setup-amzn-app.md)
* [Настройка LinkedIn как поставщика удостоверений](active-directory-b2c-setup-li-app.md)

Добавив поставщики удостоверений в каталог B2C, внесите соответствующие изменения в три политики, как описано в [справочной статье о политиках](active-directory-b2c-reference-policies.md). Сохраните политики и перезапустите приложение.  Добавленные поставщики удостоверений должны отобразиться в виде вариантов при входе и регистрации.

Вы можете свободно экспериментировать с политиками, например: добавлять или удалять поставщиков удостоверений, управлять утверждениями приложений или изменять атрибуты регистрации, а также наблюдать эффект в примере приложения. Эксперименты помогают увидеть связь между политиками, запросами на проверку подлинности и библиотекой OWIN.

