## Criar uma Azure Function para salvar arquivos em uma Storage Account. 

### Passo 1: Criar uma Conta de Armazenamento
1. **Acesse o Portal do Azure**: Faça login no portal do Azure.
2. **Criar uma Conta de Armazenamento**:
   - No menu à esquerda, clique em "Contas de armazenamento".
   - Clique em "Adicionar".
   - Preencha os campos necessários, como o nome da conta e a região.
   - Clique em "Revisar + criar" e depois em "Criar".

### Passo 2: Criar um Function App
1. **Acesse o Portal do Azure**: No menu à esquerda, clique em "Criar um recurso".
2. **Criar um Function App**:
   - Selecione "Compute" e depois "Function App".
   - Preencha os campos necessários, como o nome do Function App, grupo de recursos e sistema operacional.
   - Escolha o plano de hospedagem "Consumo (Serverless)".
   - Clique em "Revisar + criar" e depois em "Criar".

### Passo 3: Criar uma Azure Function
1. **Acesse o Function App**: No portal do Azure, vá para "Function Apps" e selecione o Function App que você criou.
2. **Adicionar uma Nova Função**:
   - Clique em "Funções" no menu à esquerda.
   - Clique em "Adicionar" e selecione "HTTP trigger".
   - Dê um nome à função e clique em "Criar".

### Passo 4: Configurar a Função para Salvar Arquivos
1. **Editar o Código da Função**:
   - No editor de código do portal do Azure, substitua o código padrão pelo seguinte exemplo em C#:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Azure.Storage.Blobs;

public static class SaveFileFunction
{
    [FunctionName("SaveFileFunction")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string connectionString = Environment.GetEnvironmentVariable("AzureWebJobsStorage");
        string containerName = "meu-container";
        string blobName = req.Query["filename"];

        BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);
        BlobContainerClient containerClient = blobServiceClient.GetBlobContainerClient(containerName);
        BlobClient blobClient = containerClient.GetBlobClient(blobName);

        using (var stream = new MemoryStream())
        {
            await req.Body.CopyToAsync(stream);
            stream.Position = 0;
            await blobClient.UploadAsync(stream, true);
        }

        return new OkObjectResult($"Arquivo {blobName} salvo com sucesso no container {containerName}.");
    }
}
```

2. **Configurar a String de Conexão**:
   - No portal do Azure, vá para "Configurações" do Function App.
   - Clique em "Configurações de Aplicativo" e adicione uma nova configuração com o nome `AzureWebJobsStorage` e o valor da string de conexão da sua conta de armazenamento.

### Passo 5: Testar a Função
1. **Obter a URL da Função**:
   - No portal do Azure, vá para a função que você criou e clique em "Obter URL da Função".
2. **Enviar uma Requisição HTTP**:
   - Use uma ferramenta como Postman ou cURL para enviar uma requisição POST para a URL da função, incluindo o arquivo no corpo da requisição e o nome do arquivo como parâmetro de consulta.

Pronto! Agora você deve ter  uma Azure Function que salva arquivos em uma Storage Account. 
[1](https://learn.microsoft.com/en-us/azure/azure-functions/functions-create-function-app-portal): [Create your first function in the Azure portal](https://learn.microsoft.com/en-us/azure/azure-functions/functions-create-function-app-portal)
[2](https://learn.microsoft.com/en-us/azure/azure-functions/functions-create-your-first-function-visual-studio): [Visão geral da conta de armazenamento - Azure Storage](https://learn.microsoft.com/pt-br/azure/storage/common/storage-account-overview)
