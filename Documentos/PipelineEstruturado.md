## Sumário

+ [Pipeline com dados estruturados](#PipelineEstruturados)
    + [Criando um bucket S3](#S3)
    + [Criando uma tabela no Glue](#Glue)
    + [Criando o serviço Kinesis Data Firehose](#Firehose)
    + [Processando dados estruturados (S3, Glue e Data Firehose)](#ProcessandoEstruturados)
    + [Criando e fazendo consultas com o Athena](#Athena)

<a name = "PipelineEstruturados"></a>
## Pipeline com dados estruturados 

<a name = "S3"></a>
### Criando um bucket S3

Para que é usado o serviço S3? O S3 é um sistema de armazenamento, nele pode ser armazenado qualquer tipo de arquivo

Depois de ter enviado os dados para o DynamoDB, temos a opção de enviar eles para o S3

Vá no painel e na barra pesquisa procure por `S3`

Clique em: `S3`

![Untitled](Imagens/Untitled%2054.png)

Clique em: `Criar bucket`

![Untitled](Imagens/Untitled%2055.png)

> **💡**: Ao nomear o bucket, de um nome inexistente até o momento, o nome do bucket deve ser globalmente exclusivo.

Insira o nome do bucket

Em Região da AWS, selecione: `Leste dos EUA (Norte da Virgínia) us-east-1`

![Untitled](Imagens/Untitled%2056.png)

Mantenha as outras configurações padrões

Clique em: `Criar bucket`

![Untitled](Imagens/Untitled%2057.png)

<br>

<a name = "Glue"></a>
### Criando uma tabela no Glue 

Para que é usado o serviço Glue? O Glue é um serviço de integração de dados, nele será possível capturar os dados vindo do Kinesis e levando ele ao bucket S3.

Vá no painel e na barra pesquisa procure por `Glue`

Clique em: `AWS Glue`

![Untitled](Imagens/Untitled%2058.png)

Vá na aba `Tables`, em Data Catalog

Clique em: `Add table`

![Untitled](Imagens/Untitled%2059.png)

Em Table details, insira um nome para a tabela

Em Database, selecione: `default`

> **💡**: Caso não apareça nenhum banco de dados, crie um no botão: `Create database`

![Untitled](Imagens/Untitled%2060.png)

Em Data Store, selecione a opção: `S3`

Depois selecione onde está localizado o bucket, clique em: `Browse S3`

![Untitled](Imagens/Untitled%2061.png)

Selecione o bucket criado anteriormente

Clique em: `Choose`

![Untitled](Imagens/Untitled%2062.png)

Em Data format, selecione o formato: `Parquet`

Clique em: `Next`

![Untitled](Imagens/Untitled%2063.png)

Para definir o schema, clique em: `Add`

![Untitled](Imagens/Untitled%2064.png)

O schema deve ser baseado nas informações geradas pelo Producer

![Untitled](Imagens/Untitled%2065.png)

Insira cada coluna do schema, seguindo a ordem de saída gerada pelo Producer e seguindo os tipos de dados.

![Untitled](Imagens/Untitled%2066.png)

Repita esse processo para todas as colunas

| Coluna | Tipo |
| --- | --- |
| Distance | float |
| HealthPoints | int |
| Latitude | float |
| Longitude | float |
| MagicPoints | int |
| Name | string |
| InputData | string |
| StatusTime | timestamp |

![Untitled](Imagens/Untitled%2067.png)

Mantenha as outras configurações em padrão

Clique em: `Next`

![Untitled](Imagens/Untitled%2068.png)

Verifique se tudo está corretamente configurado

Clique em: `Create`

![Untitled](Imagens/Untitled%2069.png)

<br>

<a name = "Firehose"></a>
### Criando o serviço Kinesis Data Firehose 

Vá no painel e na barra pesquisa procure por `Kinesis`

Clique em: `Kinesis`

![Untitled](Imagens/Untitled%2010.png)

Clique em: `Criar stream de entrega`

![Untitled](Imagens/Untitled%2070.png)

Na origem, selecione: `Amazon Kinesis Data Streams`

No destino, selecione: `Amazon S3`

![Untitled](Imagens/Untitled%2071.png)

Na configuração da origem, clique em: `Browse`

![Untitled](Imagens/Untitled%2072.png)

Selecione o Data stream criado anteriormente

Clique em: `Choose`

![Untitled](Imagens/Untitled%2073.png)

Insira um nome para o fluxo de entrega

![Untitled](Imagens/Untitled%2074.png)

Em transformar e converter registros, selecione: `Ativar conversão de formato de registro`

Em formato de saída, selecione: `Apache Parquet`

Em região, selecione `Leste dos EUA (Norte da Virgínia)`

Em banco de dados do AWS Glue, selecione o banco de dados criado no Glue

Em tabela do AWS Glue, selecione a tabela criada no Glue

![Captura da Web_14-3-2023_185721_us-east-1.console.aws.amazon.com.jpeg](Imagens/Captura_da_Web_14-3-2023_185721_us-east-1.console.aws.amazon.com.jpeg)

Em configurações do destino, clique em: `Browse`

![Untitled](Imagens/Untitled%2075.png)

Selecione o bucket criado anteriormente

Clique em: `Choose`

![Untitled](Imagens/Untitled%2076.png)

Em intervalo do buffer, coloque `60`

![Untitled](Imagens/Untitled%2077.png)

Clique em: `Criar fluxo de entrega`

![Untitled](Imagens/Untitled%2078.png)

<br>

<a name = "ProcessandoEstruturados"></a>
### Processando dados estruturados **(S3, Glue e Data Firehose)** 

Com tudo configurado, agora os dados gerados pelo Producer, serão enviados diretamente para o S3.

Para isso, rode o Producer indicando o nome do Stream

```bash
./producer -stream wildrydes
```

Deixe alguns minutos rodando

Acesse o bucket criado e cheque se foi criado um diretório

![Untitled](Imagens/Untitled%2079.png)

Siga até o último destino e verifique se lá possuem os objetos `.parquet`

![Untitled](Imagens/Untitled%2080.png)

Clique em qualquer objeto

Dentro do objeto, clique em: `Consulta com o S3 Select`

![Untitled](Imagens/Untitled%2081.png)

Em configurações de entrada, selecione o formato: `Apache Parquet`

Em configurações de saída, selecione o formato: `CSV`

![Captura da Web_14-3-2023_192012_us-east-1.console.aws.amazon.com.jpeg](Imagens/Captura_da_Web_14-3-2023_192012_us-east-1.console.aws.amazon.com.jpeg)

Em consulta SQL, selecione: `Executar consulta SQL`

Em resultados da consulta, verifique se o resultado da consulta, batem com os mesmos dados gerados pelo Producer.

![Untitled](Imagens/Untitled%2082.png)

<br>

<a name = "Athena"></a>
### Criando e fazendo consultas com o Athena

Para que é usado o serviço Athena? Com o Athena é possível analisar dados, utilizando consultas em SQL, é possível utilizar como fonte de dados o S3 e outros serviços

Vá no painel e na pesquisa procure e clique no serviço Athena

![Untitled](Imagens/Untitled%2083.png)

Selecione: `Query your data`

![Untitled](Imagens/Untitled%2084.png)

Verifique se o database selecionado é o que foi criado anteriormente

Verifique se a tabela selecionada é a que foi criada anteriormente

Faça uma consulta simples, verificando se está tudo funcionando corretamente

```sql
SELECT * FROM wildrydes_table
```

![Untitled](Imagens/Untitled%2085.png)

Clique em: `Run`

![Untitled](Imagens/Untitled%2086.png)