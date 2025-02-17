# Azure Functions com CosmosDB

Este repositório contém exemplos de Azure Functions para salvar, filtrar e listar registros em um CosmosDB. 

## Pré-requisitos

- Conta no Azure
- Visual Studio Code com a extensão Azure Functions
- Azure CLI

## Passos para Configuração

### 1. Criar uma Conta do CosmosDB

1. Acesse o portal do Azure.
2. No menu à esquerda, clique em "Azure Cosmos DB".
3. Clique em "Adicionar".
4. Selecione a API desejada (por exemplo, Core (SQL)).
5. Preencha os campos necessários, como o nome da conta e a região.
6. Clique em "Revisar + criar" e depois em "Criar".

### 2. Criar um Function App

1. No menu à esquerda, clique em "Criar um recurso".
2. Selecione "Compute" e depois "Function App".
3. Preencha os campos necessários, como o nome do Function App, grupo de recursos e sistema operacional.
4. Escolha o plano de hospedagem "Consumo (Serverless)".
5. Clique em "Revisar + criar" e depois em "Criar".

### 3. Criar Azure Functions

#### 3.1. Salvar Dados no CosmosDB

1. No portal do Azure, vá para "Function Apps" e selecione o Function App que você criou.
2. Clique em "Funções" no menu à esquerda.
3. Clique em "Adicionar" e selecione "HTTP trigger".
4. Dê um nome à função e clique em "Criar".
5. Substitua o código padrão pelo seguinte:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;
using System.Threading.Tasks;

public static class SaveToCosmosDBFunction
{
    private static readonly string EndpointUri = Environment.GetEnvironmentVariable("CosmosDBEndpointUri");
    private static readonly string PrimaryKey = Environment.GetEnvironmentVariable("CosmosDBPrimaryKey");
    private static CosmosClient cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    private static Database database = cosmosClient.GetDatabase("meu-database");
    private static Container container = database.GetContainer("meu-container");

    [FunctionName("SaveToCosmosDBFunction")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);

        await container.CreateItemAsync(data, new PartitionKey(data.id.ToString()));

        return new OkObjectResult($"Dados salvos com sucesso no CosmosDB.");
   ```

#### 3.2. Filtrar Registros no CosmosDB

1. Adicione uma nova função HTTP trigger.
2. Substitua o código padrão pelo seguinte:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;
using System.Threading.Tasks;
using System.Linq;

public static class FilterCosmosDBFunction
{
    private static readonly string.GetEnvironmentVariable("CosmosDBEndpointUri");
    private static readonly string PrimaryKey = Environment.GetEnvironmentVariable("CosmosDBPrimaryKey");
    private static CosmosClient cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    private static Database database = cosmosClient.GetDatabase("meu-database");
    private static Container container = database.GetContainer("meu-container");

    [FunctionName("FilterCosmosDBFunction")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string filter = req.Query["filter"];

        var sqlQueryText = $"SELECT * FROM c WHERE c.property = '{filter}'";
        QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
        FeedIterator<dynamic> queryResultSetIterator = container.GetItemQueryIterator<dynamic>(queryDefinition);

        var results = new List<dynamic>();
        while (queryResultSetIterator.HasMoreResults)
        {
            FeedResponse<dynamic> currentResultSet = await queryResultSetIterator.ReadNextAsync();
            results.AddRange(currentResultSet);
        }

        return new OkObjectResult(results);
    }
}
```

#### 3.3. Listar Registros no CosmosDB

1. Adicione uma nova função HTTP trigger.
2. Substitua o código padrão pelo seguinte:

```csharp
using System.IO;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;
using System.Threading.Tasks;
using System.Collections.Generic;

public static class ListCosmosDBFunction
{
    private static readonly string EndpointUri = Environment.GetEnvironmentVariable("CosmosDBEndpointUri");
    private static readonly string PrimaryKey = Environment.GetEnvironmentVariable("CosmosDBPrimaryKey");
    private static CosmosClient cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    private static Database database = cosmosClient.GetDatabase("meu-database");
    private static Container container = database.GetContainer("meu-container");

    [FunctionName("ListCosmosDBFunction")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var sqlQueryText = "SELECT * FROM c";
        QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
        FeedIterator<dynamic> queryResultSetIterator = container.GetItemQueryIterator<dynamic>(queryDefinition);

        var results = new List<dynamic>();
        while (queryResultSetIterator.HasMoreResults)
        {
            FeedResponse<dynamic> currentResultSet = await queryResultSetIterator.ReadNextAsync();
            results.AddRange(currentResultSet);
        }

        return new OkObjectResult(results);
    }
}
```

### 4. Configurar Variáveis de Ambiente

1. No portal do Azure, vá para "Configurações" do Function App.
2. Clique em "Configurações de Aplicativo" e adicione as seguintes configurações:
   - `CosmosDBEndpointUri`: URI do endpoint do CosmosDB.
   - `CosmosDBPrimaryKey`: Chave primária do CosmosDB.

### 5. Testar as Funções

1. **Obter a URL da Função**:
   - No portal do Azure, vá para a função que você criou e clique em "Obter URL da Função".
2. **Enviar uma Requisição HTTP**:
   - Use uma ferramenta como Postman ou cURL para enviar requisições HTTP para as URLs das funções.

## Contribuição

Sinta-se à vontade para contribuir com este projeto. Faça um fork do repositório, crie um branch para suas alterações e envie um pull request.

## Licença

Este projeto está licenciado sob a licença MIT. Veja o arquivo LICENSE para mais detalhes.

---


```
