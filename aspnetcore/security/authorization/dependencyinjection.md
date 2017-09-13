---
title: "Injeção de dependência em manipuladores de requisito"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 5fb6625c-173a-4feb-8380-73c9844dc23c
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authorization/dependencyinjection
ms.openlocfilehash: 37d197d7696a6e91fa236b2defc577959c95c49f
ms.sourcegitcommit: 0a70706a3814d2684f3ff96095d1e8291d559cc7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/22/2017
---
# <a name="dependency-injection-in-requirement-handlers"></a><span data-ttu-id="9bd59-103">Injeção de dependência em manipuladores de requisito</span><span class="sxs-lookup"><span data-stu-id="9bd59-103">Dependency Injection in requirement handlers</span></span>

<a name=security-authorization-di></a>

<span data-ttu-id="9bd59-104">[Manipuladores de autorização devem ser registrados](policies.md#security-authorization-policies-based-handler-registration) na coleção durante a configuração do serviço (usando [injeção de dependência](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)).</span><span class="sxs-lookup"><span data-stu-id="9bd59-104">[Authorization handlers must be registered](policies.md#security-authorization-policies-based-handler-registration) in the service collection during configuration (using [dependency injection](../../fundamentals/dependency-injection.md#fundamentals-dependency-injection)).</span></span>

<span data-ttu-id="9bd59-105">Suponha que você tenha um repositório de regras que você deseja avaliar dentro de um manipulador de autorização e esse repositório foi registrado na coleção de serviço.</span><span class="sxs-lookup"><span data-stu-id="9bd59-105">Suppose you had a repository of rules you wanted to evaluate inside an authorization handler and that repository was registered in the service collection.</span></span>  <span data-ttu-id="9bd59-106">A autorização será resolver e injetar que seu construtor.</span><span class="sxs-lookup"><span data-stu-id="9bd59-106">Authorization will resolve and inject that into your constructor.</span></span>

<span data-ttu-id="9bd59-107">Por exemplo, se você quiser usar o ASP. NET do log de infraestrutura que você deseja inserir `ILoggerFactory` para o manipulador.</span><span class="sxs-lookup"><span data-stu-id="9bd59-107">For example, if you wanted to use ASP.NET's logging infrastructure you would want to inject `ILoggerFactory` into your handler.</span></span> <span data-ttu-id="9bd59-108">Tal um manipulador pode parecer com:</span><span class="sxs-lookup"><span data-stu-id="9bd59-108">Such a handler might look like:</span></span>

```csharp
public class LoggingAuthorizationHandler : AuthorizationHandler<MyRequirement>
   {
       ILogger _logger;

       public LoggingAuthorizationHandler(ILoggerFactory loggerFactory)
       {
           _logger = loggerFactory.CreateLogger(this.GetType().FullName);
       }

       protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, MyRequirement requirement)
       {
           _logger.LogInformation("Inside my handler");
           // Check if the requirement is fulfilled.
           return Task.CompletedTask;
       }
   }
   ```

<span data-ttu-id="9bd59-109">Você deve registrar o manipulador com `services.AddSingleton()`:</span><span class="sxs-lookup"><span data-stu-id="9bd59-109">You would register the handler with `services.AddSingleton()`:</span></span>

```csharp
services.AddSingleton<IAuthorizationHandler, LoggingAuthorizationHandler>();
   ```

<span data-ttu-id="9bd59-110">Uma instância do manipulador será criado quando o aplicativo é iniciado e será DI injetar registrado `ILoggerFactory` para seu construtor.</span><span class="sxs-lookup"><span data-stu-id="9bd59-110">An instance of the handler will be created when your application starts, and DI will inject the registered `ILoggerFactory` into your constructor.</span></span>

> [!NOTE]
> <span data-ttu-id="9bd59-111">Manipuladores que usam o Entity Framework não devem ser registrados como singletons.</span><span class="sxs-lookup"><span data-stu-id="9bd59-111">Handlers that use Entity Framework shouldn't be registered as singletons.</span></span>