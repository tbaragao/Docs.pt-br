---
title: "Carregando arquivos em uma página de Razor no ASP.NET Core"
author: guardrex
description: "Saiba como carregar arquivos em uma página do Razor."
manager: wpickett
ms.author: riande
ms.date: 09/12/2017
ms.prod: aspnet-core
ms.technology: aspnet
ms.topic: get-started-article
uid: tutorials/razor-pages/uploading-files
ms.openlocfilehash: 4a2c6da6ed698d1a65ee51bd00a557e607f012da
ms.sourcegitcommit: f2a11a89037471a77ad68a67533754b7bb8303e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="uploading-files-to-a-razor-page-in-aspnet-core"></a>Carregando arquivos em uma página de Razor no ASP.NET Core

Por [Luke Latham](https://github.com/guardrex)

Nesta seção, o carregamento de arquivos com uma página do Razor é demonstrado.

O [aplicativo de exemplo Filme da páginas do Razor](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie) nesse tutorial usa a associação de modelos simples para carregar arquivos, o que funciona bem para carregar arquivos pequenos. Para obter informações sobre a transmissão de arquivos grandes, consulte [Carregando arquivos grandes com streaming](xref:mvc/models/file-uploads#uploading-large-files-with-streaming).

Nas etapas a seguir, um recurso de upload de arquivo da agenda de filmes será adicionado ao aplicativo de exemplo. Um agendamento de filmes é representado por uma classe `Schedule`. A classe inclui duas versões do agendamento. Uma versão é fornecida aos clientes, `PublicSchedule`. A outra versão é usada para os funcionários da empresa, `PrivateSchedule`. Cada versão é carregada como um arquivo separado. O tutorial demonstra como executar dois carregamentos de arquivos de uma página com um único POST para o servidor.

## <a name="security-considerations"></a>Considerações sobre segurança

É preciso ter cuidado ao fornecer aos usuários a possibilidade de carregar arquivos em um servidor. Os invasores podem executar uma [negação de serviço](/windows-hardware/drivers/ifs/denial-of-service) e outros ataques em um sistema. Algumas etapas de segurança que reduzem a probabilidade de um ataque bem-sucedido são:

* Fazer o upload de arquivos para uma área de upload de arquivos dedicada no sistema, o que facilita a aplicação de medidas de segurança sobre o conteúdo carregado. Ao permitir os uploads de arquivos, certifique-se de que as permissões de execução estejam desabilitadas no local do upload.
* Use um nome de arquivo seguro determinado pelo aplicativo, não da entrada do usuário ou o nome do arquivo carregado.
* Permitir somente um conjunto específico de extensões de arquivo aprovadas.
* Certifique-se de que as verificações do lado do cliente sejam realizadas no servidor. As verificações do lado do cliente são fáceis de contornar.
* Verifique o tamanho do upload e evite uploads maiores do que o esperado.
* Execute um verificador de vírus/malware no conteúdo carregado.

> [!WARNING]
> Carregar códigos mal-intencionados em um sistema é frequentemente a primeira etapa para executar o código que pode:
> * Tomar o controle total de um sistema.
> * Sobrecarregar um sistema, fazendo com que ele falhe completamente.
> * Comprometer dados do sistema ou de usuários.
> * Aplicar pichações a uma interface pública.

## <a name="add-a-fileupload-class"></a>Adicionar uma classe FileUpload

Criar uma Página Razor para lidar com um par de carregamentos de arquivos. Adicione uma classe `FileUpload`, que é vinculada à página para obter os dados do agendamento. Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **Classe**. Nomeie a classe **FileUpload** e adicione as seguintes propriedades:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/FileUpload.cs)]

A classe tem uma propriedade para o título do agendamento e uma propriedade para cada uma das duas versões do agendamento. Todas as três propriedades são necessárias e o título deve ter 3-60 caracteres.

## <a name="add-a-helper-method-to-upload-files"></a>Adicionar um método auxiliar para carregar arquivos

Para evitar duplicação de código para processar arquivos do agendamento carregados, primeiro, adicione um método auxiliar estático. Crie uma pasta de *Utilitários* no aplicativo e adicione um arquivo *FileHelpers.cs* com o seguinte conteúdo. O método auxiliar, `ProcessFormFile`, usa um [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile) e [ModelStateDictionary](/api/microsoft.aspnetcore.mvc.modelbinding.modelstatedictionary) e retorna uma cadeia de caracteres que contém o tamanho e o conteúdo do arquivo. O comprimento e o tipo de conteúdo são verificados. Se o arquivo não passar em uma verificação de validação, um erro será adicionado ao `ModelState`.

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Utilities/FileHelpers.cs)]

### <a name="save-the-file-to-disk"></a>Salvar o arquivo no disco

O aplicativo de exemplo salva o conteúdo do arquivo em um campo de banco de dados. Para salvar o conteúdo do arquivo no disco, use um [FileStream](/dotnet/api/system.io.filestream):

```csharp
using (var fileStream = new FileStream(filePath, FileMode.Create))
{
    await formFile.CopyToAsync(fileStream);
}
```

O processo de trabalho deve ter permissões de gravação para o local especificado por `filePath`.

### <a name="save-the-file-to-azure-blob-storage"></a>Salvar o arquivo no Armazenamento de Blobs do Azure

Para carregar o conteúdo do arquivo para o Armazenamento de Blobs do Azure, confira [Introdução ao Armazenamento de Blobs do Azure usando o .NET](/azure/storage/blobs/storage-dotnet-how-to-use-blobs). O tópico demonstra como usar [UploadFromStream](/dotnet/api/microsoft.windowsazure.storage.file.cloudfile.uploadfromstreamasync) para salvar um [FileStream](/dotnet/api/system.io.filestream) para armazenamento de blobs.

## <a name="add-the-schedule-class"></a>Adicionar a classe de Agendamento

Clique com o botão direito do mouse na pasta *Modelos*. Selecione **Adicionar** > **Classe**. Nomeie a classe **Agendamento** e adicione as seguintes propriedades:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/Schedule.cs)]

Usa a classe usa os atributos `Display` e `DisplayFormat`, que produzem formatação e títulos fáceis quando os dados de agendamento são renderizados.

## <a name="update-the-moviecontext"></a>Atualizar o MovieContext

Especifique um `DbSet` no `MovieContext` (*Models/MovieContext.cs*) para os agendamentos:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Models/MovieContext.cs?highlight=13)]

## <a name="add-the-schedule-table-to-the-database"></a>Adicione a tabela de Agendamento ao banco de dados

Abra o Console Gerenciador de pacote (PMC): **Ferramentas** > **Gerenciador de pacote NuGet** > **Console Gerenciador de pacote**.

![Menu do PMC](../first-mvc-app/adding-model/_static/pmc.png)

No PMC, execute os seguintes comandos. Estes comandos adicionam uma tabela `Schedule` ao banco de dados:

```powershell
Add-Migration AddScheduleTable
Update-Database
```

## <a name="add-a-file-upload-razor-page"></a>Adicionar uma página do Razor de upload de arquivo

Na pasta *Páginas*, crie uma pasta *Agendamentos*. Na pasta *Agendamentos*, crie uma página chamada *Index.cshtml* para carregar um agendamento com o seguinte conteúdo:

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml)]

Cada grupo de formulário inclui um **\<rótulo>** que exibe o nome de cada propriedade de classe. Os atributos `Display` no modelo `FileUpload` fornecem os valores de exibição para os rótulos. Por exemplo, o nome de exibição da propriedade `UploadPublicSchedule` é definido com `[Display(Name="Public Schedule")]` e, portanto, exibe "Agendamento público" no rótulo quando o formulário é renderizado.

Cada grupo de formulário inclui uma validação **\<span>**. Se a entrada do usuário não atender aos atributos de propriedade definidos na classe `FileUpload` ou se qualquer uma das verificações de validação do arquivo de método `ProcessFormFile` falhar, o modelo não será validado. Quando a validação do modelo falha, uma mensagem de validação útil é renderizada para o usuário. Por exemplo, a propriedade `Title` é anotada com `[Required]` e `[StringLength(60, MinimumLength = 3)]`. Se o usuário não fornecer um título, ele receberá uma mensagem indicando que um valor é necessário. Se o usuário inserir um valor com menos de três caracteres ou mais de sessenta, ele receberá uma mensagem indicando que o valor tem um comprimento incorreto. Se um arquivo que não tem nenhum conteúdo for fornecido, uma mensagem aparecerá indicando que o arquivo está vazio.

## <a name="add-the-page-model"></a>Adicionar o modelo de página

Adicione o modelo de página (*Index.cshtml.cs*) à pasta *Schedules*:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs)]

O modelo de página (`IndexModel` no *Index.cshtml.cs*) associa a classe `FileUpload`:

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet1)]

O modelo também usa uma lista dos agendamentos (`IList<Schedule>`) para exibir os agendamentos armazenados no banco de dados na página:

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet2)]

Quando a página for carregada com `OnGetAsync`, `Schedules` é preenchido com o banco de dados e usado para gerar uma tabela HTML de agendamentos carregados:

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet3)]

Quando o formulário é enviado para o servidor, o `ModelState` é verificado. Se for inválido, `Schedule` é recriado e a página é renderizada com uma ou mais mensagens de validação informando por que a validação de página falhou. Se for válido, as propriedades `FileUpload` serão usadas em *OnPostAsync* para concluir o upload do arquivo para as duas versões do agendamento e criar um novo objeto `Schedule` para armazenar os dados. O agendamento, em seguida, é salvo no banco de dados:

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Index.cshtml.cs?name=snippet4)]

## <a name="link-the-file-upload-razor-page"></a>Vincular a página do Razor de upload de arquivo

Abra *_Layout.cshtml* e adicione um link para a barra de navegação para acessar a página de upload de arquivo:

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/_Layout.cshtml?range=31-38&highlight=4)]

## <a name="add-a-page-to-confirm-schedule-deletion"></a>Adicionar uma página para confirmar a exclusão de agendamento

Quando o usuário clica para excluir um agendamento, é oferecida uma oportunidade de cancelar a operação. Adicione uma página de confirmação de exclusão (*Delete.cshtml*) à pasta *Agendamentos*:

[!code-cshtml[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml)]

O modelo de página (*Delete.cshtml.cs*) carrega um único agendamento identificado por `id` nos dados de rota da solicitação. Adicione o arquivo *Delete.cshtml.cs* à pasta *Agendamentos*:

[!code-csharp[Main](razor-pages-start/sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs)]

O método `OnPostAsync` lida com a exclusão do agendamento pelo seu `id`:

[!code-csharp[Main](razor-pages-start/snapshot_sample/RazorPagesMovie/Pages/Schedules/Delete.cshtml.cs?name=snippet1&highlight=8,12-13)]

Após a exclusão com êxito do agendamento, o `RedirectToPage` envia o usuário para a página *Index.cshtml* dos agendamentos.

## <a name="the-working-schedules-razor-page"></a>A página de Razor de agendamentos de trabalho

Quando a página é carregada, os rótulos e entradas para o título do agendamento, o agendamento público e o agendamento privado são renderizados com um botão de envio:

![Agenda a página de Razor como visto no carregamento inicial sem erros de validação e campos vazios](uploading-files/_static/browser1.png)

Selecionar o botão **Upload** sem preencher nenhum dos campos viola os atributos `[Required]` no modelo. O `ModelState` é inválido. As mensagens de erro de validação são exibidas para o usuário:

![As mensagens de erro de validação aparecem ao lado de cada controle de entrada](uploading-files/_static/browser2.png)

Digite duas letras no campo de **Título**. A mensagem de validação muda para indicar que o título deve ter entre 3 e 60 caracteres:

![Mensagem de validação de título alterada](uploading-files/_static/browser3.png)

Quando um ou mais agendamentos são carregados, a seção **Agendamentos carregados** renderiza os agendamentos carregados:

![Tabela de agendamentos carregados, mostrando o título de cada agendamento, dados carregados em UTC, tamanho do arquivo de versão pública e tamanho do arquivo de versão privada](uploading-files/_static/browser4.png)

O usuário pode clicar no link **Excluir** para chegar à exibição de confirmação de exclusão, na qual ele tem a oportunidade de confirmar ou cancelar a operação de exclusão.

## <a name="troubleshooting"></a>Solução de problemas

Para solucionar problemas de informações com o carregamento de `IFormFile`, consulte [Carregamentos de arquivos no ASP.NET Core: solução de problemas](xref:mvc/models/file-uploads#troubleshooting).

Obrigado por concluir esta introdução às Páginas Razor. Agradecemos os comentários. [Introdução ao MVC e ao EF Core](xref:data/ef-mvc/intro) é um excelente acompanhamento para este tutorial.

## <a name="additional-resources"></a>Recursos adicionais

* [Carregamentos de arquivos no ASP.NET Core](xref:mvc/models/file-uploads)
* [IFormFile](/dotnet/api/microsoft.aspnetcore.http.iformfile)

>[!div class="step-by-step"]
[Anterior: validação](xref:tutorials/razor-pages/validation)
