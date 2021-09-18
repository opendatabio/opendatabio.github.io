---
title: "Import Traits"
linkTitle: "Traits"
author: "A. Vicentini & A. Chalom"
date: 2021-09-09
weight: 5
description: >
  Import Traits using the OpenDataBio R client
---

{{< alert color="warning" title="Attention">}}
1. Strongly recommended you use the web interface to enter traits one by one. Use batch imports only if you have too many.
1. Before importing any trait, **make sure the variable is not already registered** in the system with a different name. Preventing duplication is important as the Trait class is shared among all users of an OpenDataBio installation. This also means that once a trait is used for Measurements, you may only alter the trait's definition, for example, a category name, if you were the only user entering measurements for the trait. Otherwise, somebody else used that definition and you will not be able to alter it.
1. **Export_name must be unique** in a single ODB installation. This name is used when exporting data and on forms, and you should consider making it as short and informative (of definition) as possible. Also, **API will validate the export name - must be camelCase, PascalCase or snake_case, and you cannot add spaces or any special character (accents)**. Ex. 'dbh','dbhPom', 'leafLength', 'LeafLength'  or 'leaf_length', respectively.
{{< /alert >}}

Traits can be imported  using `odb_import_traits()`.

Read carefully the [Traits POST API](/docs/api/pos-data/#post-traits).

## Traits types

See `odb_traitTypeCodes()` for possible trait types.

## Trait name and categories User translations
Fields **name** and **description** could be one of following:
1. using the Language code as keys: `list("en" = "Diameter at Breast Height","pt" ="Diâmetro a Altura do Peito")`
1. or using the Language names as keys: `list("English" ="Diameter at Breast Height","Portuguese" ="Diâmetro a Altura do Peito")`.
<br><br>
Field **categories** must include for each category+rank+lang the following fields:
1. `lang`=mixed - **required**, the id, code or name of the language of the translation
1. `name`=string - **required**, the translated category name required (name+rank+lang must be unique)
1. `rank`=number - **required**, rank is important to indicate the same category across languages, and defines ordinal traits;
1. `description`=string - optional for categories, a definition of the category.

This may be formatted as a data.frame and placed in the `categories` column of another data.frame:
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



## Quantitative trait example

For quantitative traits for either _integers_ or _real_ values (types 0 or 1).

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

## Categorical trait example

1. Must include categories. The only difference between ordinal and categorical traits is that ordinal categories will have a rank and the rank will be inferred from the order the categories are informed during importation. Note that ordinal traits are semi-quantitative and so, if you have categories ask yourself whether they are not really ordinal and register accordingly.
1. Like the Trait name and description, categories may also have different language translations, and you SHOULD enter the translations for the languages available (`odb_get_languages()`) in the server, so the Trait will be accessible in all languages. English is mandatory, so at least the English name must be informed. Categories may have a description associated, but sometimes the category name is self explanatory, so descriptions of categories are not mandatory.

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


## Link type trait example

> Link types are traits that allow you link a Taxon or Voucher as a value measurement to another object. For example, you may conduct a plant inventory for which you have only counts for Taxon associated to a locality. Therefore, you may create a LINK trait, which will allow you to store the count values for any Taxon as measurements for a particular location (POINT, POLYGON). Or you may link such values to Vouchers instead of Taxons if you have a representative specimen for the taxons.

Use the WebInterface.

## Text and color traits

Text and color traits require the minimum fields only for trait registration. Text traits allow the storage of textual observations. Color will only allow color codes (see example in Importing Measurements)

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

## Spectral traits

> Spectral traits are specific to spectral data. You must specify the range of wavenumber values for which you may have absorbance or reflectance data, and the length of the spectra to be stored as measurements to allow validation during input. So, for each range and spacement of the spectral values you have, a different SPECTRAL trait must be created.

Use the WebInterface.
