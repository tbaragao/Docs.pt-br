---
title: Adicionando uma pesquisa
author: rick-anderson
description: Mostra como adicionar uma pesquisa a um aplicativo ASP.NET Core MVC simples
ms.author: riande
manager: wpickett
ms.date: 03/07/2017
ms.topic: get-started-article
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/first-mvc-app/search
ms.openlocfilehash: aaa00a9eedda73a2c66da30b5ace3b5b5a7760b2
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
[!INCLUDE[adding-model](../../includes/mvc-intro/search1.md)]

<span data-ttu-id="59730-103">Renomeie rapidamente o parâmetro `searchString` para `id` com o comando **rename**.</span><span class="sxs-lookup"><span data-stu-id="59730-103">You can quickly rename the `searchString` parameter to `id` with the **rename** command.</span></span> <span data-ttu-id="59730-104">Clique com o botão direito do mouse em `searchString` **> Renomear**.</span><span class="sxs-lookup"><span data-stu-id="59730-104">Right click on `searchString` **> Rename**.</span></span>

![Menu contextual](search/_static/rename.png)

<span data-ttu-id="59730-106">Os destinos de renomeação são realçados.</span><span class="sxs-lookup"><span data-stu-id="59730-106">The rename targets are highlighted.</span></span>

![Editor de código mostrando a variável realçada em todo o método ActionResult do Índice](search/_static/rename2.png)

<span data-ttu-id="59730-108">Altere o parâmetro para `id` e todas as ocorrências de `searchString` altere para `id`.</span><span class="sxs-lookup"><span data-stu-id="59730-108">Change the parameter to `id` and all occurrences of `searchString` change to `id`.</span></span>

![Editor de código mostrando que a variável foi alterada para ID](search/_static/rename3.png)

[!INCLUDE[adding-model](../../includes/mvc-intro/search2.md)]

<span data-ttu-id="59730-110">Observe como o IntelliSense nos ajuda a atualizar a marcação.</span><span class="sxs-lookup"><span data-stu-id="59730-110">Notice how intelliSense helps us update the markup.</span></span>

![Menu contextual do IntelliSense com o método selecionado na lista de atributos do elemento de formulário](search/_static/int_m.png)

![Menu contextual do IntelliSense com get selecionado na lista de valores de atributo de método](search/_static/int_get.png)

<span data-ttu-id="59730-113">Observe a fonte diferenciada na marcação `<form>`.</span><span class="sxs-lookup"><span data-stu-id="59730-113">Notice the distinctive font in the `<form>` tag.</span></span> <span data-ttu-id="59730-114">Essa fonte diferenciada indica que a marcação tem o suporte de [Auxiliares de Marcação](../../mvc/views/tag-helpers/intro.md).</span><span class="sxs-lookup"><span data-stu-id="59730-114">That distinctive font indicates the tag is supported by [Tag Helpers](../../mvc/views/tag-helpers/intro.md).</span></span>

![marcação de formulário com texto roxo](search/_static/th_font.png)

[!INCLUDE[adding-model](../../includes/mvc-intro/search3.md)]

>[!div class="step-by-step"]
<span data-ttu-id="59730-116">[Anterior](controller-methods-views.md)
[Próximo](new-field.md)</span><span class="sxs-lookup"><span data-stu-id="59730-116">[Previous](controller-methods-views.md)
[Next](new-field.md)</span></span>  