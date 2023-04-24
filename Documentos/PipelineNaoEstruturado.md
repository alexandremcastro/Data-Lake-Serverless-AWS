## Sumário

+ [Pipeline com dados não estruturados](#PipelineNaoEstruturado)
    + [Criando o serviço Kinesis Data Streams](#CriacaoDataStreams)
    + [Criando uma tabela no DynamoDB](#CriacaoDynamoDB)
    + [Administrando políticas de segurança com o IAM](#IAM)
        + [Criação da política](#Politica)
        + [Criação da função](#Funcao)
    + [Criando uma função com o serviço Lambda](#FuncaoLambda)
        + [Primeira variável de ambiente](#Ambiente1)
        + [Segunda variável de ambiente](#Ambiente2)
    + [Processando dados não estruturados (Data Streams, DynamoDB e Lambda)](#ProcessandoNaoEstruturado)

<a name = "PipelineNaoEstruturado"></a>
## Pipeline com dados não estruturados 

<a name = "CriacaoDataStreams"></a>
### Criando o serviço Kinesis Data Streams 

Para que é usado o serviço Kinesis? O AWS Kinesis é um serviço de streaming de dados, usado para coletar, processar e analisar grandes volumes de dados em tempo real.

O Kinesis Data Streams será necessário no projeto para capturar os dados gerados pelo Producer.

Vá no painel e na barra pesquisa procure por `Kinesis`

Clique em: `Kinesis`

![Untitled](/Imagens/Untitled%2010.png)

Nesta tela, selecione: `Kinesis Data Streams` 

Em seguida, selecione: `Criar fluxo de dados`

![Untitled](/Imagens/Untitled%2011.png)

Insira o nome do fluxo de dados como: `wildrydes`

Selecione a capacidade do fluxo de dados para: `Provisionado`

Mantenha as outras opções em padrão

Em seguida clique em: `Criar fluxo de dados`

![Untitled](/Imagens/Untitled%2012.png)

<br>

<a name = "CriacaoDynamoDB"></a>
### Criando uma tabela no DynamoDB 

Para que é usado o serviço DynamoDB? O AWS DynamoDB é um serviço de banco de dados NoSQL, que permite armazenar e recuperar grandes volumes de dados de forma rápida e escalável, sem a necessidade de provisionar ou gerenciar servidores. Ele será uma das fontes de armazenamentos de dados do Data Lake.

Vá no painel e na barra pesquisa procure por `DynamoDB`

Clique em: `DynamoDB`

![Untitled](/Imagens/Untitled%2013.png)

Para criar a tabela que receberemos os dados, clique em: `Criar tabela`

![Untitled](/Imagens/Untitled%2014.png)

Como nome da tabela utilize o nome: `UnicornSensorData`

Como chave de partição utilize: `Name`

Como chave de classificação utilize: `StatusTime`

![Untitled](/Imagens/Untitled%2015.png)

As próximas opções são padrão

Ao fim da página, clique em: `Criar tabela`

![Untitled](/Imagens/Untitled%2016.png)

<br>

<a name = "IAM"></a>
### Administrando políticas de segurança com o IAM 

Para que é usado o serviço IAM? O AWS IAM (Identity and Access Management) é um serviço de gerenciamento de identidade e acesso, usado para gerenciar o acesso a recursos da AWS de forma segura, permitindo gerenciar usuários, grupos e permissões de acesso aos serviços.

Vá no painel e na barra pesquisa procure por `IAM`

Clique em: `IAM`

![Untitled](/Imagens/Untitled%2017.png)

<br>

<a name = "Politica"></a>
<b> Criação da política </b>

Criarei uma politica para o DynamoDB permitir a gravação de dados na tabela criada.

Na página redirecionada, clique em: `Politicas`

![Untitled](/Imagens/Untitled%2018.png)

Em seguida clique em: `Criar politica`

![Untitled](/Imagens/Untitled%2019.png)

No serviço, selecione: `DynamoDB`

Nas ações, selecione: `BatchWriteItem`

![Untitled](/Imagens/Untitled%2020.png)

Em recursos, selecione: `Adicionar ARN`

![Untitled](/Imagens/Untitled%2021.png)

Para preencher essa tabela, é necessário do código ARN da tabela criada no DynamoDB

![Untitled](/Imagens/Untitled%2022.png)

Para ter acesso ao ARN da tabela no DynamoDB, vá no serviço e clique na tabela que foi criada

![Untitled](/Imagens/Untitled%2023.png)

O ARN fica localizado nas informações gerais da tabela

Copie o código ARN

![Untitled](/Imagens/Untitled%2024.png)

Cole o código ARN e todas as informações serão preenchidas automaticamente

Clique em: `Adicionar`

![Untitled](/Imagens/Untitled%2025.png)

Prosseguindo a criação da política

Clique em: `Próximo: Tags`

![Untitled](/Imagens/Untitled%2026.png)

Clique em: `Próximo: Revisar`

![Untitled](/Imagens/Untitled%2027.png)

Em nome, insira: `WildRydesDynamoDBWritePolicy`

Após, clique em: `Criar política`

![Untitled](/Imagens/Untitled%2028.png)

<br>

<a name = "Funcao"></a>
<b> Criação da função </b>

Agora irei criar uma função para o Lambda se comunicar com o Kinesis e DynamoDB utilizando as politicas:`WildRydesDynamoDBWritePolicy` e `AWSLambdaKinesisExecutionRole`

Clique em: `Funções`

Depois clique em: `Criar função`

![Untitled](/Imagens/Untitled%2029.png)

Em caso de uso, selecione `Lambda`

Depois clique em: `Próximo`

![Untitled](/Imagens/Untitled%2030.png)

Pesquise pela política `AWSLambdaKinesisExecutionRole`

Selecione a política: `AWSLambdaKinesisExecutionRole`

![Untitled](/Imagens/Untitled%2031.png)

Pesquise pela política `WildRydesDynamoDBWritePolicy`

Selecione a política: `WildRydesDynamoDBWritePolicy`

![Untitled](/Imagens/Untitled%2032.png)

Confirme se as duas políticas estão selecionadas

![Untitled](/Imagens/Untitled%2033.png)

Prossiga a criação da função

Selecione: `Próximo`

![Untitled](/Imagens/Untitled%2034.png)

Em nome da função, insira: `WildRydesStreamProcessorRole`

![Untitled](/Imagens/Untitled%2035.png)

Verifique as políticas selecionadas

Selecione: `Criar função`

![Untitled](/Imagens/Untitled%2036.png)

Verifique se a função foi criada

![Untitled](/Imagens/Untitled%2037.png)

<br>

<a name = "FuncaoLambda"></a>
### Criando uma função com o serviço Lambda 

Para que é usado o serviço Lambda? É um serviço para rodar códigos de diversas linguagens diferentes, nele rodará o código que será responsável por trazer os dados do Kinesis e levar para o DynamoDB.

Vá no painel e na barra pesquisa procure por `Lambda`

Clique em: `Lambda`

![Untitled](/Imagens/Untitled%2038.png)

Clique em: `Criar uma função`

![Untitled](/Imagens/Untitled%2039.png)

Em nome da função, insira o nome: `WildRydesStreamProcessor`

Selecione abaixo a opção de execução: `Node.js 14.x`

![Untitled](/Imagens/Untitled%2040.png)

No papel de execução, selecione a opção: `Usar uma função existente`

Na função, selecione a `WildRydesStreamProcessorRole`, criada anteriormente na seção do IAM

Em seguida, selecione: `Criar função`

![Untitled](/Imagens/Untitled%2041.png)

Na parte de código, insira o código a seguir:

```jsx
"use strict";

const AWS = require("aws-sdk");
const dynamoDB = new AWS.DynamoDB.DocumentClient();
const tableName = process.env.TABLE_NAME;

exports.handler = function (event, context, callback) {
    const requestItems = buildRequestItems(event.Records);
    const requests = buildRequests(requestItems);

    Promise.all(requests)
        .then(() =>
            callback(null, `Delivered ${event.Records.length} records`)
        )
        .catch(callback);
};

function buildRequestItems(records) {
    return records.map((record) => {
        const json = Buffer.from(record.kinesis.data, "base64").toString(
            "ascii"
        );
        const item = JSON.parse(json);

        return {
            PutRequest: {
                Item: item,
            },
        };
    });
}

function buildRequests(requestItems) {
    const requests = [];
    // Batch Write 25 request items from the beginning of the list at a time
    while (requestItems.length > 0) {
        const request = batchWrite(requestItems.splice(0, 25));

        requests.push(request);
    }

    return requests;
}

function batchWrite(requestItems, attempt = 0) {
    const params = {
        RequestItems: {
            [tableName]: requestItems,
        },
    };

    let delay = 0;

    if (attempt > 0) {
        delay = 50 * Math.pow(2, attempt);
    }

    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            dynamoDB
                .batchWrite(params)
                .promise()
                .then(function (data) {
                    if (data.UnprocessedItems.hasOwnProperty(tableName)) {
                        return batchWrite(
                            data.UnprocessedItems[tableName],
                            attempt + 1
                        );
                    }
                })
                .then(resolve)
                .catch(reject);
        }, delay);
    });
}
```

![Untitled](/Imagens/Untitled%2042.png)

Antes do Deploy, vá nas configurações de variáveis de ambiente

Clique em: `Editar`

![Untitled](/Imagens/Untitled%2043.png)

<a name = "Ambiente1"></a>
<b> Primeira variável de ambiente </b>

Clique em: `Adicionar variáveis de ambiente`

Na primeira variável de ambiente, insira:

Chave: `TABLE_NAME`

Valor: `UnicornSensorData`

<br>

<a name = "Ambiente2"></a>
<b> Segunda variável de ambiente </b>

Clique em: `Adicionar variáveis de ambiente`

Na segunda variável de ambiente, insira:

Chave: `AWS_NODEJS_CONNECTION_REUSE_ENABLED`

Valor: `1`

Clique em: `Salvar`

![Untitled](/Imagens/Untitled%2044.png)

Adicionando o Kinesis na função

Clique em: `Adicionar gatilho`

![Untitled](/Imagens/Untitled%2045.png)

Selecione o serviço: `Kinesis` 

Em Stream do Kinesis, insira o stream criado anteriormente no Kinesis Data Streams

![Untitled](/Imagens/Untitled%2046.png)

Mantenha o tamanho do lote em 100

Clique em: `Adicionar`

![Untitled](/Imagens/Untitled%2047.png)

Com tudo configurado, farei o Deploy da função

Vá na aba Código e clique em: `Deploy`

![Untitled](/Imagens/Untitled%2048.png)

<br>

<a name = "ProcessandoNaoEstruturado"></a>
### Processando dados não estruturados **(Data Streams, DynamoDB e Lambda)** 

Com o Kinesis, DynamoDB, IAM e Lambda criados e configurados, agora é possível fazer o processamento dos dados gerados pelo script Producer.

Com o Cloud9 aberto, rode o Producer, mencionando o stream criado no Kinesis

```bash
./producer -stream wildrydes
```

Rode o Consumer e verifique a produção dos dados

![Untitled](/Imagens/Untitled%2049.png)

Na aba `Monitor`, verifique se os dados estão sendo capturados

![Untitled](/Imagens/Untitled%2050.png)

Para verificar se os dados estão sendo entregues, abra o serviço `DynamoDB`

Vá na aba tabelas e selecione a tabela `UnicornSensorData`

![Untitled](/Imagens/Untitled%2051.png)

Clique em: `Explorar itens da tabela`

![Untitled](/Imagens/Untitled%2052.png)

Verifique se os dados foram preenchidos

![Untitled](/Imagens/Untitled%2053.png)