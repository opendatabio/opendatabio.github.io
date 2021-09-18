---
title: "Importar dados via R"
linkTitle: "Importar dados via R"
weight: 3
author: "A. Vicentini & A. Chalom"
date: 2021-09-09
description: >
  Importar dados com o pacote OpenDataBio-R
---

{{% pageinfo color="warning" %}}
O pacote [Opendatabio-R](https://github.com/opendatabio/opendatabio-r) foi criado para permitir aos usuários interagir com um servidor OpenDataBio, tanto para obter (GET) dados ou para importar (POST) dados para o banco de dados. Este tutorial é um exemplo básico de como importar dados.
{{% /pageinfo %}}

## Configure a conexão

1. Configure a conexão com o servidor OpenDataBio usando a função `odb_config()` do pacote. Os parâmetros mais importantes para
esta função são `base_url`, que deve apontar para a URL da API do seu servidor OpenDataBio e
`token`, que é o token de acesso usado para autenticar seu usuário.
1. O `token` só é necessário para obter dados de conjuntos de dados que possuem uma das políticas de acesso restrito. Os dados dos conjuntos de dados de acesso público podem ser extraídos sem a especificação do token.
1. Seu token está disponível em seu perfil na interface web

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ"
#create a config object
cfg = odb_config(base_url=base_url, token = token)
#test connection
odb_test(cfg)
```

## Importar Dados (POST API)

> Verifique a [Referência rápida da API]/docs/api/quick-reference) para obter uma lista completa dos POST endpoints e os campos necessários para importação de dados.


### Funções de importação OpenDataBio-R

Todas as funções de importação têm a mesma assinatura: o primeiro argumento é um `data.frame` com os dados a serem importados, e o segundo parâmetro é um objeto de configuração gerado por `odb_config`.

Ao escrever uma solicitação de importação, verifique os [documentos da API POST](/docs/api/post-data) para entender quais colunas podem ser declaradas no `data.frame`.

Todas as funções de importação retornam um `id do job`, que pode ser usado para verificar se o job ainda está em execução, se terminou com sucesso ou se encontrou um erro. Este id de trabalho pode ser usado nas funções `odb_get_jobs()`, `odb_get_affected_ids()` e `odb_get_log()`, para encontrar detalhes sobre a submissão de importação (job). Você também pode ver o log em sua lista de trabalhos do usuário na interface da web.

{{<alert color="warning" title="Atenção">}}
**Ordem é importante** - a importação correta de dados pode depender de registros já registrados. Por exemplo, importar um indivíduo com uma identidade de táxon requer o nome do táxon registrado no banco de dados, então primeiro valide sua lista de táxons usando a [API GET](/docs/api/get-data) e, em seguida, importe os registros para o Indivíduos.
{{</ alert>}}

## Trabalhando com datas e datas incompletas

Para Indivíduos, Vouchers e identificações, você pode usar datas incompletas.

O formato de data usado no OpenDataBio é AAA-MM-DD (ano - mês - dia), portanto, uma entrada válida seria `2018-05-28`.

Particularmente em dados históricos, o dia (ou mês) exato pode não ser conhecido, então você pode substituir esses campos por NA: '1979-05-NA' significa "um dia desconhecido, em maio de 1979" e '1979-NA- NA 'significa "dia e mês desconhecidos, 1979". Você não pode adicionar uma data para a qual tenha apenas o dia, mas pode, se tiver apenas o mês, se for realmente significativo de alguma forma.
