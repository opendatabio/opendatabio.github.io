---
title: "Getting data with OpenDataBio-R"
linkTitle: "Get data with R"
weight: 2
author: "A. Vicentini & A. Chalom"
date: 2021-09-09
description: >
  Getting data using the OpenDataBio R client
---

{{% pageinfo color="warning" %}}
The [Opendatabio-R package](https://github.com/opendatabio/opendatabio-r) was created to allow users to interact with an OpenDataBio server, to both obtain (GET) data or to import (POST) data into the database. This tutorial is a basic example of how to get data.
{{% /pageinfo %}}

## Set up the connection

1. Set up the connection to the OpenDataBio server using the `odb_config()` function. The most important parameters for
this function are `base_url`, which should point to the API url for your OpenDataBio server, and
`token`, which is the access token used to authenticate your user.
1. The `token` is only need to get data from datasets that have one of the restricted access policies. Data from datasets of public access can be extracted without the token specification.
1. Your token is avaliable in your profile in the web interface

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ"
cfg = odb_config(base_url=base_url, token = token)
```

More advanced configuration involves setting a specific API version, a custom User Agent, or
other HTTP headers, but this is not covered here.

## Test your connection
The function `odb_test()` may be used to check if the connection was successful, and whether
your user was correctly identified:

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

As an alternative, you can specify these parameters as systems variables. Before starting R,
set this up on your shell (or add this to the end of your .bashrc file):

```sh
export ODB_TOKEN="YourToken"
export ODB_BASE_URL="http://localhost:8080/api"
export ODB_API_VERSION="v0"
```
## GET Data

> Check the [GET API Quick-Reference](/docs/api/quick-reference) for a full list of endpoints and request parameters.

For data of public access the `token` is optional. Below two examples for the Locations and Taxons endpoints. Follow a similar reasoning for using the remaining endpoints. See the package help in R for all available `odb_get_{endpoint}` functions.

## Getting Taxon names

> See [GET API Taxon Endpoint](/docs/api/get-data/#taxons-endpoint) request parameters and a list of response fields.

```r
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
#get id for a taxon
mag.id = odb_get_taxons(params=list(name='Magnoliidae',fields='id,name'),odb_cfg = cfg)
#use this id to get all descendants of this taxon
odb_taxons = odb_get_taxons(params=list(root=mag.id$id,fields='id,scientificName,taxonRank,parent_id,parentName'),odb_cfg = cfg)
head(odb_taxons)

```

If the server used the seed data provided and the default language is portuguese, the result will be:

```
  id scientificName taxonRank parent_id  parentName
1 25    Magnoliidae     Clado        20 Angiosperms
2 43     Canellales     Ordem        25 Magnoliidae
3 62       Laurales     Ordem        25 Magnoliidae
4 65    Magnoliales     Ordem        25 Magnoliidae
5 74      Piperales     Ordem        25 Magnoliidae
6 93  Chloranthales     Ordem        25 Magnoliidae
```

## Getting Locations

> See [GET API Location Endpoint](/docs/api/get-data/#locations-endpoint) request parameters and a list of response fields.

Get some fields listing all Conservation Units (`adm_level==99`) registered in the server:

```r
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
odblocais = odb_get_locations(params = list(fields='id,name,parent_id,parentName',adm_level=99),odb_cfg = cfg)
head(odblocais)
```

If the server used the seed data provided and the default language is portuguese, the result will be:

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

Get the plots imported in the [import locations tutorial](/docs/tutorials/02-post-data-r-vignette/importing-locations/).
To obtain a spatial object in R, use the `readWKT` function of the `rgeos` package.

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
Figure generated:

![](/docs/tutorials/01-get-r-vignette/get-plot-locations.png)
