# Data Lake Serverless (AWS)
[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/alexandremcastro/Data-Lake-Serverless-AWS/blob/main/LICENSE)

> **📝Nota:**: Eu não sou responsável pelos custos do projeto, isso significa que qualquer gasto que ocorra durante a realização do projeto, como utilização de serviços, não é de minha responsabilidade. É importante que você esteja ciente desses custos e os gerencie adequadamente.

### Arquitetura do projeto

Este projeto tem como objetivo oferecer um Data Lake na nuvem, sem servidor, utilizando os serviços da Amazon Web Services (AWS).

Para atender a essa necessidade, ofereço a criação de um Data Lake na nuvem, utilizando os serviços da AWS. A AWS é uma plataforma líder de computação em nuvem que oferece uma ampla variedade de serviços para atender às necessidades de diferentes tipos de empresas.

A escolha de uma arquitetura Serverless é uma opção que tem se mostrado cada vez mais popular no mundo da computação em nuvem. Com essa abordagem, a responsabilidade pela infraestrutura é transferida para o provedor de serviços em nuvem, permitindo que as empresas se concentrem em suas atividades principais.

Além disso, a utilização de serviços da AWS permite que o Data Lake seja altamente escalável e resiliente, garantindo que ele possa lidar com grandes volumes de dados e oferecer alta disponibilidade aos usuários.

Essa será a arquitetura a ser seguida no projeto:

![Diagrama sem nome.jpg](/Imagens/Diagrama_sem_nome.jpg)

## Tecnologias
[Cloud9](https://aws.amazon.com/pt/cloud9/) • [Kinesis](https://aws.amazon.com/pt/kinesis/) • [DynamoDB](https://aws.amazon.com/pt/dynamodb/) • [IAM](https://aws.amazon.com/pt/iam/) • [Lambda](https://aws.amazon.com/pt/lambda/) • [S3](https://aws.amazon.com/pt/s3/) • [Glue](https://aws.amazon.com/pt/glue/) • [Athena](https://aws.amazon.com/pt/athena/)

## Motivação
Este projeto experimental é o resultado das minhas experiências com a implementação de um Data Lake Serverless utilizando os serviços da Amazon Web Services (AWS).

Foi uma oportunidade para aplicar meus conhecimentos em tecnologia da AWS e construir uma solução de Data Lake Serverless, que pode ser utilizada por empresas que precisam lidar com grandes volumes de dados. Aprendi muito durante o processo e espero poder utilizar esse conhecimento em projetos futuros.

## Sumário
+ [Pipeline com dados não estruturados](/Documentos/PipelineNaoEstruturado.md#PipelineNaoEstruturado)
    + [Criando o serviço Kinesis Data Streams](/Documentos/PipelineNaoEstruturado.md#CriacaoDataStreams)
    + [Criando uma tabela no DynamoDB](/Documentos/PipelineNaoEstruturado.md#CriacaoDynamoDB)
    + [Administrando políticas de segurança com o IAM](/Documentos/PipelineNaoEstruturado.md#IAM)
    + [Criando uma função com o serviço Lambda](/Documentos/PipelineNaoEstruturado.md#FuncaoLambda)
    + [Processando dados não estruturados (Data Streams, DynamoDB e Lambda)](/Documentos/PipelineNaoEstruturado.md#ProcessandoNaoEstruturado)
+ [Pipeline com dados estruturados](/Documentos/PipelineEstruturado.md#PipelineEstruturados)
    + [Criando um bucket S3](/Documentos/PipelineEstruturado.md#S3)
    + [Criando uma tabela no Glue](/Documentos/PipelineEstruturado.md#Glue)
    + [Criando o serviço Kinesis Data Firehose](/Documentos/PipelineEstruturado.md#Firehose)
    + [Processando dados estruturados (S3, Glue e Data Firehose)](/Documentos/PipelineEstruturado.md#ProcessandoEstruturados)
    + [Criando e fazendo consultas com o Athena](/Documentos/PipelineEstruturado.md#Athena)

## Requisitos para o projeto
> **📝Nota:**: Este projeto foi concluído em Fevereiro de 2023, e é importante ressaltar que alguns dos requisitos abordados podem sofrer alterações ao longo do tempo. Como em qualquer área de tecnologia, novas soluções e tecnologias surgem constantemente, o que pode tornar algumas das abordagens e soluções apresentadas neste projeto desatualizadas em algum momento.


<br/>

**1. Serviço Cloud9**

Para que é usado o serviço Cloud9? Ele fornece um terminal de comando para uma instância EC2. Isso é essencial neste projeto, pois será nele que serão gerados os dados para consumo

Vá no painel e na pesquisa procure e clique no serviço Cloud9

![Untitled](/Imagens/Untitled.png)

Você será redirecionado a essa tela

Clique em: `Criar ambiente`

![Untitled](/Imagens/Untitled%201.png)

Coloque um nome para identificação, a descrição é opcional

Mantenha as outras opções em padrão

![Untitled](/Imagens/Untitled%202.png)

> **💡**: Verifique se a instância que está sendo criada é coberta pelo nível gratuito.

<br>

![Untitled](/Imagens/Untitled%203.png)

Para criar a o Cloud9, clique em `Criar`

![Untitled](/Imagens/Untitled%204.png)

Para acessar o terminal criado pelo Cloud9, clique em: `Em aberto`

![Untitled](/Imagens/Untitled%205.png)

A criação do serviço demora alguns minutos, pois ainda será criado o serviço EC2 simultaneamente. Ao criar você será redirecionado para essa tela:

![Untitled](/Imagens/Untitled%206.png)

É importante manter essa tela aberta até o fim do projeto, nela que será rodado o script responsável por gerar os dados para o Data Lake

<br>

**2. Arquivos do Producer e Consumer**

Baixando o Producer e Consumer

Para baixar o Producer/Consumer, abra a janela com o terminal do Cloud9 e rode o comando:

```bash
curl -s [https://data-processing.serverlessworkshops.io/client/client.tar](https://data-processing.serverlessworkshops.io/client/client.tar) | tar -xv
```

![Untitled](/Imagens/Untitled%207.png)

Essa etapa será repetida algumas vezes, então é de extrema importância saber como realizar ela até o fim do projeto

<br>

<b> Producer </b>

O Producer é responsável por simular os dados dos sensores nos cavalos, é um script fornecido pela AWS feito em Go, já compilado.

Para começar gerar os dados, abra o terminal do Cloud9 e rode o comando:

```bash
./producer
```

![Untitled](/Imagens/Untitled%208.png)

<br>

<b> Consumer </b>

O Consumer é responsável por consumir os dados dos sensores nos cavalos, neste caso, o Consumer está lendo os dados gerados pelo Producer, o Consumer também é um script fornecido pela AWS feito em Go, já compilado.

Para verificar se está tudo funcionando corretamente, abra uma nova janela do terminal e rode o comando:

```bash
./consumer
```

![Untitled](/Imagens/Untitled%209.png)

Para parar de rodar ambos, faça o comando: `CTRL + C`

## Tarefas

**Tarefas concluídas**
- [x] Descrever sobre a finalidade do Data Lake
- [x] Realizar os requisitos mínimos
- [x] Configurar os serviços AWS
- [x] Testar/Verificar o Data Lake

**Futuras implementações**
- [ ] Simular novas requisições do cliente
- [ ] Acrescentar novos serviços ao Data Lake, atentendo ao cliente
- [ ] Montar um relatório de consumo mensal com o serviço Billing
- [ ] Organizar os dados em camadas, semelhante a medalhão

## Links
**Projeto:**

[Projeto no Site](https://alexandre-castro.vercel.app/blog/datalake-serverless)

[Projeto no Notion](https://alexandremcastro.notion.site/02-2023-AWS-Data-Lake-Serverless-c8f8198221134364991976c26ee1a985)

[Projeto no GitLab](https://gitlab.com/alexandremcastro/Data-Lake-Serverless-AWS)

[Projeto no GitHub](https://github.com/alexandremcastro/Data-Lake-Serverless-AWS)

**Artigos orientadores:**

[Computação sem servidor - AWS](https://aws.amazon.com/pt/serverless/)

[Conceitos básicos da arquitetura sem servidor da AWS - AWS](https://aws.amazon.com/pt/serverless/getting-started/?serverless.sort-by=item.additionalFields.createdDate&serverless.sort-order=desc)

[Deploying Code Faster with Serverless Framework and AWS Service Catalog - AWS](https://aws.amazon.com/pt/blogs/apn/deploying-code-faster-with-serverless-framework-and-aws-service-catalog/)

[Deploy and manage a serverless data lake on the AWS Cloud by using infrastructure as code - AWS](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-and-manage-a-serverless-data-lake-on-the-aws-cloud-by-using-infrastructure-as-code.html)

## Conclusão 
Em resumo, a proposta desse projeto é criar um Data Lake em nuvem, Serverless, utilizando os serviços da AWS, permitindo que as empresas possam armazenar, processar e analisar grandes volumes de dados de maneira escalável e eficiente.
