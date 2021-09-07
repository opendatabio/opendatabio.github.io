
---
title: "Serviços de API"
linkTitle: "Serviços de API"
weight: 3
description: >
  Como obter ou importar dados do OpenDataBio?
---

> Cada instalação do OpenDataBio fornece um serviço de API, permitindo aos usuários OBTER dados programaticamente e os colaboradores de INSERIREM novos dados. O serviço é de acesso aberto a dados públicos, requer autenticação do usuário para INSERIR dados ou para OBTER dados de acesso restrito.

O OpenDataBio [Application Programming Interface -API](https://en.wikipedia.org/wiki/API) permite que os usuários interajam com um banco de dados OpenDataBio para exportar e importar dados sem usar a interface web.

<a href="https://www.r-project.org/logo/"><img src = "Rlogo.png" style = "margin: 0px 5px 10px 0px; float: left; width: 45px; border: 0 "/></a> O [pacote OpenDataBio R](https://github.com/opendatabio/opendatabio-r) é um **cliente** para esta API, permitindo a interação com o repositório de dados diretamente do R.

A API OpenDataBio permite a consulta ao banco de dados e também a importação de dados por meio de uma interface inspirada em [REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Todas as solicitações e respostas da API são formatadas em [JSON](https://en.wikipedia.org/wiki/JSON).

## A interação com a API do OpenDataBio

Uma simples chamada para a API OpenDataBio possui quatro partes independentes:

{{<alert color = "success">}} [verbo HTTP] + URL base + endpoint + parâmetros de solicitação {{</ alert>}}

1. **HTTP-verbo** -  `GET` para exportações ou` POST` para importações.
1. **URL-base** - o URL usado para acessar seu servidor OpenDataBio + mais `/ api / v0`. Por exemplo, `http:// opendatabio.inpa.gov.br/api/v0`
1. **endpoint** - representa o objeto ou coleção de objetos que você deseja acessar, por exemplo, para consultar nomes taxonômicos, o endpoint é "taxons"
1. **parâmetros de solicitação** - representam a filtragem e o processamento que devem ser feitos com os objetos e são representados na chamada da API após um ponto de interrogação. Por exemplo, para recuperar apenas nomes taxonômicos válidos finalize a solicitação com `?valid = 1`.

A chamada API acima pode ser inserida em um navegador para OBTER dados de acesso público. Por exemplo, para obter a lista de táxons válidos de uma instalação do OpenDataBio, a solicitação da API poderia ser:

```bash

http://opendb.inpa.gov.br/api/v0/taxons?valid=1&limit=10

```

Quando usar o [OpenDataBio R package](https://github.com/opendatabio/opendatabio-r) essa mesma chamada seria algo como  `odb_get_taxons(list(valid=1,limit=10))`.

A resposta será algo como:

```JSON
{
  "meta":
  {
    "odb_version":"0.9.1-alpha1",
    "api_version":"v0",
    "server":"http://opendb.inpa.gov.br",
    "full_url":"http://opendb.inpa.gov.br/api/v0/taxons?valid=1&limit1&offset=100"},
    "data":
    [
      {
        "id":62,
        "parent_id":25,
        "author_id":null,
        "scientificName":"Laurales",
        "taxonRank":"Ordem",
        "scientificNameAuthorship":null,
        "namePublishedIn":"Juss. ex Bercht. & J. Presl. In: Prir. Rostlin: 235. (1820).",
        "parentName":"Magnoliidae",
        "family":null,
        "taxonRemarks":null,
        "taxonomicStatus":"accepted",
        "ScientificNameID":"http:\/\/tropicos.org\/Name\/43000015 | https:\/\/www.gbif.org\/species\/407",
        "basisOfRecord":"Taxon"
    }]}
```


***

## Autenticação da API

1. **Não é obrigatória** para obter quaisquer dados de acesso público numa base de dados ODB, que por padrão inclui Localidades, Taxons, Referências Bibliográficas, Pessoas e Variáveis.
1. **Autenticação é necessária** para OBTER quaisquer dados que não sejam de acesso público.
  - A autenticação é feita usando um `token API`, que pode ser encontrado no seu perfil de usuário na interface do aplicativo. O token é atribuído a um único usuário do banco de dados e não deve ser compartilhado, exposto, enviado por e-mail ou armazenado em controles de versão.
  - Para autenticar na API OpenDataBio, use o token no cabeçalho "Authorization" da solicitação da API. Ao usar o cliente R, passe o token para a função `odb_config`` cfg = odb_config (token = "seu-token-aqui") `.

Os usuários somente terão acesso aos dados para os quais o usuário tem permissão e para quaisquer dados com acesso público na base de dados. O acesso à Medições, Indivíduos, Vouchers e Mídia depende das permissões compreendidas pelo token do usuário.

***

## Versões da API

A API OpenDataBio segue seu próprio número de versão. Isso significa que o cliente pode esperar usar o mesmo código e obter as mesmas respostas, independentemente da versão do OpenDataBio que o servidor está executando. Todas as alterações feitas na mesma versão da API devem ser compatíveis com versões anteriores. Nosso controle de versão da API é controlado pelo URL, portanto, para solicitar uma versão específica da API, use o número da versão entre o URL base e o endpoint:

```bash
http://opendatabio.inpa.gov.br/opendatabio/api/v1/taxons

http://opendatabio.inpa.gov.br/opendatabio/api/v2/taxons

```

{{< alert color="warning" title="Version v0">}}A versão 0 da API (v0) é uma versão instável. A primeira versão estável será a versão 1 da API.
{{< /alert >}}
