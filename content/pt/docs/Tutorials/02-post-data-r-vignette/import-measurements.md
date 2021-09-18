---
title: "Importar Medições"
linkTitle: "Medições"
author: "A. Vicentini & A. Chalom"
weight: 7
date: 2021-09-09
description: >
  Importar Medições com o pacote OpenDataBio R
---

{{< alert color="warning" title="Atenção">}}
1. A medição tem uma restrição: valores ou categorias de medição duplicados para o mesmo object_id e mesma data não serão importados, a menos que você especifique `duplicated = 1`  para o registro.
1. Para importar medições você precisa coletar algumas informações de outros modelos. Você precisa fornecer um `dataset` para o qual você tem permissões,` trait_id` (export_name ou id), `value` (o conteúdo varia dependendo do tipo de variável),` date` (uma data completa ou incompleta), `person` (pessoa que mediu), `object_type` (uma de taxon, individual, voucher ou location) e` object_id` (o id do object_type). Para o tipo de variável LINK você deve fornecer uma coluna `link_id`, e 'value' torna-se opcional para este caso específico.
1. **DICA**: Para relacionar medições de diferentes variáveis, você pode adicionar palavaras chave campo de medição `notes`. Por exemplo, você mede LeafLength e LeafWidth para as mesmas folhas, portanto, em ambas as medições, adicione "leaf1", "leaf2" ao campo de nota para vinculá-los. Além disso, se você medir o DAP e o DAP.POM em um inventário de parcela florestal e se tiver vários troncos para uma planta, você pode adicionar "caule1", "caule2" para permitir a correspondência de um DAP do caule com sua altura específica de medição. Esse tipo de link pode ser útil em análises.
1. Várias validações acontecerão durante a importação da medição. Considere verificar os dados localmente antes de enviar um trabalho (job) para facilitar a solução de problemas e garantir que os valores sejam consistentes com suas expectativas. Você não poderá ATUALIZAR nem EXCLUIR medidas com a API, portanto, evite importar medições que precisarão ser corrigidas posteriormente (uma por uma).
1. Para características quantitativas, OpenDataBio não pode validar se suas medições estão na mesma unidade que a definição de variável. Portanto, verifique qual unidade a variável possui e converta seus dados de acordo antes de enviar.
{{< /alert >}}

As medições podem ser importadas usando `odb_import_measurements()`. Leia atentamente o [Measurements POST API](/docs/api/pos-data/#post-measurements).

## Variáveis Quantitativas

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)

#obter o id do trait do servidor (verifique se o trait existe)
#gerar alguns dados falsos para 10 medições

dbhs = sample(seq(10,100,by=0.1),10)
object_ids = sample(1:3,length(dbhs),replace=T)
dates = sample(as.Date("2000-01-01"):as.Date("2000-03-31"),length(dbhs))
dates = lapply(dates,as.Date,origin="1970-01-01")
dates = lapply(dates,as.character)
dates = unlist(dates)


to.odb = data.frame(
  trait_id = 'dbh',
  value = dbhs,
  date = dates,
  object_type = 'Individual',
  object_id=object_ids,
  person="Oliveira, A.A. de",
  dataset = 1,
  notes = "some fake measurements",
  stringsAsFactors=F)

  #isso só funcionará se a pessoa existir, os ids individuais existirem
  #e se a característica com export_name = dbh existe
  odb_import_measurements(to.odb,odb_cfg=cfg)
```
Baixar os dados importados:

```r
dad = odb_get_measurements(params = list(dataset=1),odb_cfg=cfg)
dad[,c("id","basisOfRecord", "measured_type", "measured_id", "measurementType",
  "measurementValue", "measurementUnit", "measurementDeterminedDate",
  "datasetName", "license")]
```

```
id      basisOfRecord           measured_type measured_id measurementType measurementValue measurementUnit measurementDeterminedDate
1   1 MeasurementsOrFact App\\Models\\Individual           3             dbh             86.8     centimeters                2000-02-19
2   2 MeasurementsOrFact App\\Models\\Individual           2             dbh             84.8     centimeters                2000-03-25
3   3 MeasurementsOrFact App\\Models\\Individual           2             dbh             65.7     centimeters                2000-03-15
4   4 MeasurementsOrFact App\\Models\\Individual           3             dbh             88.0     centimeters                2000-03-05
5   5 MeasurementsOrFact App\\Models\\Individual           3             dbh             35.3     centimeters                2000-01-04
6   6 MeasurementsOrFact App\\Models\\Individual           2             dbh             36.0     centimeters                2000-03-23
7   7 MeasurementsOrFact App\\Models\\Individual           2             dbh             78.6     centimeters                2000-03-22
8   8 MeasurementsOrFact App\\Models\\Individual           2             dbh             69.7     centimeters                2000-03-09
9   9 MeasurementsOrFact App\\Models\\Individual           3             dbh             12.3     centimeters                2000-01-30
10 10 MeasurementsOrFact App\\Models\\Individual           3             dbh             14.7     centimeters                2000-01-18
   datasetName   license
1  Dataset test CC-BY 4.0
2  Dataset test CC-BY 4.0
3  Dataset test CC-BY 4.0
4  Dataset test CC-BY 4.0
5  Dataset test CC-BY 4.0
6  Dataset test CC-BY 4.0
7  Dataset test CC-BY 4.0
8  Dataset test CC-BY 4.0
9  Dataset test CC-BY 4.0
10 Dataset test CC-BY 4.0
```
## Medidas categóricas

As categorias DEVEM ser informadas por seus ids ou nome no campo `value`. Para características CATEGORICAL ou ORDINAL, `value` deve ser um valor único. Para CATEGORICAL_MULTIPLE, `value` pode ser um ou vários ids de categorias ou nomes separados por um de ` | ou ; ou, `.

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)

#a categorical trait
(odbtraits = odb_get_traits(params=list(name="specimenFertility"),odb_cfg = cfg))

#base line
to.odb = data.frame(trait_id = odbtraits$id, date = '2021-07-31', stringsAsFactors=F)

#the plant was collected with both flowers and fruits, so the value are the two categories
value = c("Flowers","Fruits")

#get categories for this trait if found
(cats = odbtraits$categories[[1]])
#check that your categories are registered for the trait and get their ids
value = cats[match(value,cats$name),'id']
#make multiple categories ids a string value
value = paste(value,collapse=",")

to.odb$value = value

#this links to a voucher
to.odb$object_type = "Voucher"

#get voucher id from API (must be ID).
#Search for collection number 1234
odbspecs = odb_get_vouchers(params=list(number="3456-A"),odb_cfg=cfg)
to.odb$object_id = odbspecs$id[1]

#get dataset id
odbdatasets = odb_get_datasets(params=list(name='Dataset test'),odb_cfg=cfg)
head(odbdatasets)
to.odb$dataset = odbdatasets$id

#person that measured
odbperson = odb_get_persons(params=list(search='ana cristina sega'),odb_cfg=cfg)
to.odb$person = odbperson$id

#import'
odb_import_measurements(to.odb,odb_cfg=cfg)

#get imported
dad = odb_get_measurements(params = list(voucher=odbspecs$id[1]),odb_cfg=cfg)
dad[,c("id","basisOfRecord", "measured_type", "measured_id", "measurementType",
       "measurementValue", "measurementUnit", "measurementDeterminedDate",
       "datasetName", "license")]
```
```
id      basisOfRecord        measured_type measured_id   measurementType measurementValue measurementUnit
1 11 MeasurementsOrFact App\\Models\\Voucher           1 specimenFertility  Flowers, Fruits              NA
  measurementDeterminedDate  datasetName   license
1                2021-07-31 Dataset test CC-BY 4.0
```

## Cores

Para valores de cor, você deve inserir cor como seus códigos de `strings RGB hexadecimal`, para que possam ser renderizados graficamente e na interface da web. Portanto, qualquer valor de cor é permitido e seria mais fácil usar as cores da paleta na interface da web para inserir tais medidas. O pacote `gplots` permite que você converta nomes de cores em códigos RGB hexadecimais se você quiser fazer isso por meio da API.

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)

#get the trait id from the server (check that trait exists)
odbtraits = odb_get_traits(odb_cfg=cfg)
(m = match(c("fruitColor"),odbtraits$export_name))

#base line
to.odb = data.frame(trait_id = odbtraits$id[m], date = '2014-01-13', stringsAsFactors=F)

#get color value
#install.packages("gplots",dependencies = T)
library(gplots)
(value =  col2hex("red"))
to.odb$value = value

#this links to a specimen
to.odb$object_type = "Individual"
#get voucher id from API (must be ID). Search for collection number 1234
odbind = odb_get_individuals(params=list(tag='3456'),odb_cfg=cfg)
odbind$scientificName
to.odb$object_id = odbind$id[1]

#get dataset id
odbdatasets = odb_get_datasets(params=list(name='Dataset test'),odb_cfg=cfg)
head(odbdatasets)
to.odb$dataset = odbdatasets$id

#person that measured
odbperson = odb_get_persons(params=list(search='ana cristina sega'),odb_cfg=cfg)
to.odb$person = odbperson$id

odb_import_measurements(to.odb,odb_cfg=cfg)
```
![](/docs/tutorials/02-post-data-r-vignette/color-measurement.png)

## Medições de tipo de LINK de banco de dados

O tipo de variável LINK permite registrar dados de contagem, como por exemplo o número de indivíduos de uma espécie em um determinado local. Você deve fornecer o objeto vinculado (`link_id`), que pode ser um Táxon ou um Voucher, dependendo da definição da variável, e então o` value` recebe a contagem numérica.


```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)

#get the trait id from the server (check that trait exists)
odbtraits = odb_get_traits(odb_cfg=cfg)
(m = match(c("taxonCount"),odbtraits$export_name))

#base line
to.odb = data.frame(trait_id = odbtraits$id[m], date = '2014-01-13', stringsAsFactors=F)

#the taxon to link the count value
odbtax = odb_get_taxons(params=list(name='Ocotea guianensis'),odb_cfg=cfg)
to.odb$link_id = odbtax$id

#now add the count value for this trait type
#this is optional for this measurement,
#however, it would make no sense to include such link without a count in this example
to.odb$value = 23

#a note to clarify the measurement (optional)
to.odb$notes = 'No voucher, field identification'

#this measurement will link to a location
to.odb$object_type = "Location"
#get location id from API (must be ID).
#lets add this to a transect
odblocs = odb_get_locations(params=list(adm_level=101,limit=1),odb_cfg=cfg)
to.odb$object_id = odblocs$id

#get dataset id
odbdatasets = odb_get_datasets(params=list(name='Dataset test'),odb_cfg=cfg)
head(odbdatasets)
to.odb$dataset = odbdatasets$id

#person that measured
odbperson = odb_get_persons(params=list(search='ana cristina sega'),odb_cfg=cfg)
to.odb$person = odbperson$id

odb_import_measurements(to.odb,odb_cfg=cfg)
```
![](/docs/tutorials/02-post-data-r-vignette/linktype-measurement.png)

## Medições espectrais


`value` deve ser uma string de valores do espectro separados por ";". O número de valores concatenados deve corresponder ao atributo `value_length` da variável, que é extraído da especificação do intervalo de número de onda para a variável. Portanto, você pode verificar isso facilmente antes de importar com `odb_get_traits(params=list (fields = 'all', type = 9),cfg)`

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)

#read a spectrum
spectrum = read.table("1_Sample_Planta-216736_TAG-924-1103-1_folha-1_abaxial_1.csv",sep=",")

#second column are  NIR leaf absorbance values
#the spectrum has 1557 values
nrow(spectrum)
#[1] 1557
#collapse to single string
value = paste(spectrum[,2],collapse = ";")
substr(value,1,100)
#[1] "0.6768057;0.6763237;0.6755353;0.6746023;0.6733549;0.6718447;0.6701176;0.6682984;0.6662288;0.6636459;"

#get the trait id from the server (check that trait exists)
odbtraits = odb_get_traits(odb_cfg=cfg)
(m = match(c("driedLeafNirAbsorbance"),odbtraits$export_name))

#see the trait
odbtraits[m,c("export_name", "unit", "range_min", "range_max",  "value_length")]
#export_name       unit range_min range_max value_length
#6 driedLeafNirAbsorbance absorbance   3999.64  10001.03         1557

#must be true
odbtraits$value_length[m]==nrow(spectrum)
#[1] TRUE

#base line
to.odb = data.frame(trait_id = odbtraits$id[m], value=value, date = '2014-01-13', stringsAsFactors=F)

#this links to a voucher
to.odb$object_type = "Voucher"
#get voucher id from API (must be ID).
#search for a collection number
odbspecs = odb_get_vouchers(params=list(number="3456-A"),odb_cfg=cfg)
to.odb$object_id = odbspecs$id[1]

#get dataset id
odbdatasets = odb_get_datasets(params=list(name='Dataset test'),odb_cfg=cfg)
to.odb$dataset = odbdatasets$id

#person that measured
odbperson = odb_get_persons(params=list(search='adolpho ducke'),odb_cfg=cfg)
to.odb$person = odbperson$id

#import
odb_import_measurements(to.odb,odb_cfg=cfg)
```

![](/docs/tutorials/02-post-data-r-vignette/spectral-measurement.png)

## Texto e notas

Basta adicionar o texto ao campo `value` e proceder como para os outros tipos de variável.
