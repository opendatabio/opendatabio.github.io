---
title: "Obter dados via R"
linkTitle: "Obter dados via R"
author: "A. Vicentini & A. Chalom"
weight: 2
date: 2021-09-09
description: >
  Obter dados com o pacote OpenDataBio-R
---

{{% pageinfo color="warning"%}}
O [pacote Opendatabio-R](https://github.com/opendatabio/opendatabio-r) foi criado para permitir aos usuários interagir com um servidor OpenDataBio, para obter (GET) dados ou importar (POST) dados para a base de dados. Este tutorial é um exemplo básico de como obter dados.
{{% / pageinfo%}}

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
cfg = odb_config(base_url=base_url, token = token)
```

A configuração mais avançada envolve a definição de uma versão de API específica, um agente de usuário personalizado ou
outros cabeçalhos HTTP, mas isso não é coberto aqui.

## Teste sua conexão
A função `odb_test()` pode ser usada para verificar se a conexão foi bem sucedida e se
seu usuário foi identificado corretamente:

```r
odb_test(cfg)
#will output
Host: http://localhost:8080/api/v0
Versions: server 0.9.1-alpha1 api v0
$message
[1] "Success!"

$user
[1] "admin@example.org"
```

Como alternativa, você pode especificar esses parâmetros como variáveis ​​de sistema. Antes de iniciar o R,
configure isso em seu shell (ou adicione ao final de seu arquivo .bashrc):

```sh
export ODB_TOKEN="YourToken"
export ODB_BASE_URL="http://localhost:8080/api"
export ODB_API_VERSION="v0"
```
## Obter dados

> Verifique a [Referência rápida da API GET]/docs/api/quick-reference) para obter uma lista completa de endpoints e parâmetros de solicitação.

Para dados de acesso público o `token` é opcional. Abaixo dois exemplos para Localidades e Taxons que são de acesso aberto. Siga um raciocínio semelhante para usar os demais endpoints. Veja a ajuda do pacote em R para todas as funções `odb_get_{endpoint}` disponíveis.

## Obtendo nomes de táxons

> Consulte [GET API Taxon Endpoint](/docs/api/get-data/#taxons-endpoint) para uma lista dos parâmetros de solicitação e uma lista de campos de resposta.


```r
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
#get id for a taxon
mag.id = odb_get_taxons(params=list(name='Magnoliidae',fields='id,name'),odb_cfg = cfg)
#use this id to get all descendants of this taxon
odb_taxons = odb_get_taxons(params=list(root=mag.id$id,fields='id,scientificName,taxonRank,parent_id,parentName'),odb_cfg = cfg)
head(odb_taxons)

```
Algo como, dependo da sua base:

```
  id scientificName taxonRank parent_id  parentName
1 25    Magnoliidae     Clado        20 Angiosperms
2 43     Canellales     Ordem        25 Magnoliidae
3 62       Laurales     Ordem        25 Magnoliidae
4 65    Magnoliales     Ordem        25 Magnoliidae
5 74      Piperales     Ordem        25 Magnoliidae
6 93  Chloranthales     Ordem        25 Magnoliidae
```

## Obtendo locais

> Consulte [GET API Location Endpoint](/docs/api/get-data/#locations-endpoint) para os parâmetros de solicitação e uma lista de campos de resposta.

Obtenha alguns campos listando todas as Unidades de Conservação (`adm_level=99`) registradas no servidor:

```r
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
odblocais = odb_get_locations(params = list(fields='id,name,parent_id,parentName',adm_level=99),odb_cfg = cfg)
head(odblocais)
```

Se o servidor usar os dados de seed fornecidos o resultado será:

```
id                                                           name
1 5628                              Estação Ecológica Mico-Leão-Preto
2 5698          Área de Relevante Interesse Ecológico Ilha do Ameixal
3 5700 Área de Relevante Interesse Ecológico da Mata de Santa Genebra
4 5703     Área de Relevante Interesse Ecológico Buriti de Vassununga
5 5707                                Reserva Extrativista do Mandira
6 5728                                   Floresta Nacional de Ipanema
parent_id parentName
1         6  São Paulo
2         6  São Paulo
3         6  São Paulo
4         6  São Paulo
5         6  São Paulo
6         6  São Paulo
```

Pegar as parcelas [importadas no tutorial de localidades](/docs/tutorials/02-post-data-r-vignette/importing-locations/).
Para obter um objeto espacial no R, use a função `readWKT` do pacote `rgeos`.

```r
library(rgeos)
library(opendatabio)
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)

locais = odb_get_locations(params=list(adm_level=100),odb_cfg = cfg)
locais[,c('id','locationName','parentName')]
colnames(locais)
for(i in 1:nrow(locais)) {
  geom = readWKT(locais$footprintWKT[i])
  if (i==1) {
    plot(geom,main=locais$locationName[i],cex.main=0.8)
    axis(side=1,cex.axis=0.5)
    axis(side=2,cex.axis=0.5,las=2)
  } else {
    plot(geom,main=locais$locationName[i],add=T,col='red')
  }
}
```
Figura gerada:

![](/docs/tutorials/01-get-r-vignette/get-plot-locations.png)
