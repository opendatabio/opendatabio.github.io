---
title: "Importar Variáveis"
linkTitle: "Variáveis"
author: "A. Vicentini & A. Chalom"
date: 2021-09-09
weight: 5
description: >
  Importar Variáveis usando o pacote OpenDataBio R
---

{{< alert color="warning" title="Atenção">}}
1. Recomendamos fortemente que você use a interface da web para inserir variáveis uma por uma. Use importações em lote somente se você tiver muitas variáveis.
1. Antes de importar qualquer variável **certifique-se de que ela já não esteja cadastrada** no sistema com um nome diferente. Prevenir a duplicação é importante porque a classe Trait é compartilhada entre todos os usuários de uma instalação OpenDataBio. Isso também significa que, uma vez que uma variável é usada para Medições, você só poderá alterar a definição da variável, por exemplo, um nome de uma categoria, se você for o único usuário que inseriu medições para a variável. Caso contrário, outra pessoa usou essa definição e você não poderá alterá-la.
1. **export_name deve ser exclusivo** em uma única instalação ODB. Este nome é usado ao exportar dados e em formulários, e você deve considerar torná-lo o mais curto e informativo (de definição) possível. Além disso, a **API validará o nome da exportação - deve ser camelCase, PascalCase ou snake_case, e você não pode adicionar espaços ou qualquer caractere especial (acentos)**. Ex. 'dbh', 'dbhPom', 'leafLength', 'LeafLength' ou 'leaf_length', respectivamente.
{{< /alert >}}

As características podem ser importadas usando `odb_import_traits()`.

Leia atentamente o [Traits POST API](/docs/api/pos-data/#post-traits).

## Tipos de variáveis

Veja `odb_traitTypeCodes()` para os códigos numéricos possíveis tipos de variáveis

## Traduções Nome da Variável e de Categorias
Os campos **name** e **description** podem ter um dos seguintes conteúdos:
1. usando o código do idioma como chaves: `list("en" = "Diameter at Breast Height","pt" ="Diâmetro a Altura do Peito")`
1. ou usando os nomes dos idiomas como chaves: `list("English" ="Diameter at Breast Height","Portuguese" ="Diâmetro a Altura do Peito")`.
<br> <br>
O campo **categories** deve incluir para cada categoria + classificação + idioma os seguintes campos:
1. `lang` = misto - **obrigatório**, o id, código ou nome do idioma da tradução
1. `name` = string - **obrigatório**, o nome da categoria traduzido obrigatório (name + rank + lang deve ser único)
1. `rank` = número - **obrigatório**, a classificação é importante para indicar a mesma categoria entre os idiomas e define variáveis ordinais;
1. `description` = string - opcional para categorias, uma definição da categoria.

Isso pode ser formatado como um data.frame e colocado na coluna `categories` de outro data.frame:

```
data.frame(
  rbind(
    c("lang"="en","rank"=1,"name"="small","description"="smaller than 1 cm"),
    c("lang"="pt","rank"=1,"name"="pequeno","description"="menor que 1 cm"),
    c("lang"="en","rank"=2,"name"="big","description"="bigger than 10 cm"),
    c("lang"="pt","rank"=2,"name"="grande","description"="maior que 10 cm")
  ),
  stringsAsFactors=FALSE
)
```

## Variável quantitativa

Para variáveis quantitativas para valores _integers_ ou _real_ (type 0 ou 1).


```r
odb_traitTypeCodes()

library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)

#do this first to build a correct data.frame as it will include translations list
to.odb = data.frame(type=1,export_name = "dbh", unit='centimeters',stringsAsFactors = F)

#add translations (note double list)
#format is language_id = translation (and the column be a list with the translation lists)
to.odb$name[[1]]= list('1' = 'Diameter at breast height', '2' = 'Diâmetro à altura do peito')
to.odb$description[[1]]= list('1' = 'Stem diameter measured at 1.3m height','2' = 'Diâmetro do tronco medido à 1.3m de altura')

#measurement validations
to.odb$range_min = 10  #this will restrict the minimum measurement value allowed in the trait
to.odb$range_max = 400 #this will restrict the maximum value

#measurements can be linked to (classes concatenated by , or a list)
to.odb$objects = "Individual,Voucher,Taxon"  #makes no sense link such measurements to Locations

to.odb$notes = 'this is quantitative trait example'

#import
odb_import_traits(to.odb,odb_cfg=cfg)

```
## Variável categórica

1. Deve incluir categorias. A única diferença entre as características ordinais e categóricas é que as categorias ordinais terão um rank, ou ordem. Observe que as variáveis ordinais são semiquantitativas e, portanto, se você tiver categorias, pergunte-se se elas não são realmente ordinais e registre de acordo.
1. Assim como o `name` e a `description` da variável, as categorias também podem ter traduções em diferentes idiomas, e você DEVE inserir as traduções para os idiomas disponíveis (`odb_get_languages ​​()`) para que a variável esteja acessível em todos os idiomas. O inglês é obrigatório, portanto, pelo menos o nome em inglês deve ser informado. As categorias podem ter uma descrição associada, mas às vezes o nome da categoria é autoexplicativo, portanto, as descrições das categorias não são obrigatórias.

```r
odb_traitTypeCodes()

library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)

#do this first to build a correct data.frame as it will include translations list

#do this first to build a correct data.frame as it will include translations list
to.odb = data.frame(type=3,export_name = "specimenFertility", stringsAsFactors = F)

#trait name and description
to.odb$name =  data.frame("en"="Specimen Fertility","pt"="Fertilidade do especímene",stringsAsFactors=F)
to.odb$description =  data.frame("en"="Kind of reproductive stage of a collected plant","pt"="Estágio reprodutivo de uma amostra de planta coletada",stringsAsFactors=F)

#categories (if your trait is ORDINAL, the add categories in the wanted order here)
categories = data.frame(
  rbind(
    c('en',1,"Sterile"),
    c('pt',1,"Estéril"),
    c('en',2,"Flowers"),
    c('pt',2,"Flores"),
    c('en',3,"Fruits"),
    c('pt',3,"Frutos"),
    c('en',4,"Flower buds"),
    c('pt',4,"Botões florais")
  ),
  stringsAsFactors =FALSE
)
colnames(categories) = c("lang","rank","name")

#descriptions not included for categories as they are obvious,
# but you may add a 'description' column to the categories data.frame

#objects for which the trait may be used for
to.odb$objects = "Individual,Voucher"

to.odb$notes = 'a fake note for a multiselection categorical trait'
to.odb$categories = list(categories)

#import
odb_import_traits(to.odb,odb_cfg=cfg)
```


## Variável tipo LINK

> Um variável de tipo LINK permite vincular um Táxon ou Voucher como uma medição de outro objeto. Por exemplo, você pode conduzir um inventário de plantas para o qual possui apenas contagens para o táxon associado a uma localidade. Portanto, você pode criar uma variável do tipo LINK, que permitirá que você armazene os valores de contagem para qualquer Táxon como medições para um local específico (PONTO, POLÍGONO).

Use a interface ou siga o exemplo para as demais variáveis.

## Variáveis do tipo texto ou cor

Variáveis de texto permitem o armazenamento de observações textuais. A cor permitirá apenas códigos de cores.

```r
odb_traitTypeCodes()

library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)


to.odb = data.frame(type=5,export_name = "taxonDescription", stringsAsFactors = F)

#trait name and description
to.odb$name =  data.frame("en"="Taxonomic descriptions","pt"="Descrições taxonômicas",stringsAsFactors=F)
to.odb$description =  data.frame("en"="Taxonomic descriptions from the literature","pt"="Descrições taxonômicas da literatura",stringsAsFactors=F)

#will only be able to use this trait for a measurment associated with a Taxon
to.odb$objects = "Taxon"

#import
odb_import_traits(to.odb,odb_cfg=cfg)

```

## Variáveis espectrais

> Variáveis espectrais são específicos para dados espectrais. Você deve especificar a amplitude dos números de ondas para os quais você pode ter dados de absorbância ou refletância e o comprimento dos espectros a serem armazenados como medições para permitir a validação durante a entrada. Portanto, para cada intervalo e espaçamento dos valores espectrais que você tem, uma variável ESPECTRAL diferente deve ser criada.

Use a interface ou siga o exemplo para as demais variáveis.
