## Criar uma Azure Function que filtra registros no CosmosDB. 

### Passo 1: Criar uma Conta do CosmosDB
1. **Acesse o Portal do Azure**: Faça login no portal do Azure.
2. **Criar uma Conta do CosmosDB**:
   - No menu à esquerda, clique em "Azure Cosmos DB".
   - Clique em "Adicionar".
   - Selecione a API desejada (por exemplo, Core (SQL)).
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

### Passo 4: Configurar a Função para Filtrar Registros no CosmosDB
1. **Editar o Código da Função**:
   - No editor de código do portal do Azure, substitua o código padrão pelo seguinte exemplo em C#:

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
    private static readonly string EndpointUri = Environment.GetEnvironmentVariable("CosmosDBEndpointUri");
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
       > currentResultSet = await queryResultSetIterator.ReadNextAsync();
            results.AddRange(currentResultSet);
        }

        return new OkObjectResult(results);
    }
}
```

2. **Configurar as Variáveis de Ambiente**:
   - No portal do Azure, vá para "Configurações" do Function App.
   - Clique em "Configurações de Aplicativo" e adicione as seguintes configurações:
     - `CosmosDBEndpointUri`: URI do endpoint do CosmosDB.
     - `CosmosDBPrimaryKey`: Chave primária do CosmosDB.

### Passo 5: Testar a Função
1. **Obter a URL da Função**:
   - No portal do Azure, vá para a função que você criou e clique em "Obter URL da Função".
2. **Enviar uma Requisição HTTP**:
   - Use uma ferramenta como Postman ou cURL para enviar uma requisição GET para a URL da função, incluindo o parâmetro de consulta `filter` com o valor desejado.

Agora você deve ter uma Azure Function que filtra registros no CosmosDB. 

[1](https://dev.to/willvelida/working-with-azure-cosmos-db-in-your-azure-functions-4ppp): [Working with Azure Cosmos DB in your Azure Functions](https://dev.to/willvelida/working-with-azure-cosmos-db-in-your-azure-functions-4ppp)
[2](https://learn.microsoft.com/en-us/azure/azure-functions/functions-add-output-binding-cosmos-db-vs-code): [Connect Azure Functions to Azure Cosmos DB using Visual Studio Code](https://learn.microsoft.com/en-us/azure/azure-functions/functions-add-output-binding-cosmos-db-vs-code)
