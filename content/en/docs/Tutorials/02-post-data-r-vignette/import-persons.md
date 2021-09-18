---
title: "Import Persons"
linkTitle: "Persons"
author: "A. Vicentini & A. Chalom"
weight: 4
date: 2021-09-09
description: >
  Import Persons using the OpenDataBio R client
---

> Check the [POST Persons API docs](/docs/api/post-data/#post-persons) in order to understand which columns can be declared when importing Persons.

It is recommended you use the web interface, as it will warn you in case the person you want to register has a similar, likely the same, person already registered. The API will only check for identical Abbreviations, which is the single restriction of the [Person class](/docs/concepts/auxiliary-objects/#person). **Abbreviations are unique and duplications are not allowed**. This does not prevent data downloaded from repositories to have different abbreviations or full name for the same person. So, you should standardize secondary data before importing into the server to minimize such common errors.

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ"
cfg = odb_config(base_url=base_url, token = token)

one = data.frame(full_name='Adolpho Ducke',abbreviation='DUCKE, A.',notes='Grande botânico da Amazônia',stringsAsFactors = F)
two = data.frame(full_name='Michael John Gilbert Hopkins',abbreviation='HOPKINKS, M.J.G.',notes='Curador herbário INPA',stringsAsFactors = F)
to.odb= rbind(one,two)
odb_import_persons(to.odb,odb_cfg=cfg)

#may also add an email entry if you have one

```

Get the data

```r
library(opendatabio)
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
persons = odb_get_persons(odb_cfg=cfg)
persons = persons[order(persons$id,decreasing = T),]
head(persons,2)
```
Will output:

```
id                    full_name     abbreviation email institution                       notes
613 1582 Michael John Gilbert Hopkins HOPKINKS, M.J.G.  <NA>          NA       Curador herbário INPA
373 1581                Adolpho Ducke        DUCKE, A.  <NA>          NA Grande botânico da Amazônia
```
