---
title: "Importar Pessoas"
linkTitle: "Pessoas"
author: "A. Vicentini & A. Chalom"
weight: 4
date: 2021-09-09
description: >
  Importar Pessoas usando o pacote OpenDataBio R
---

> Ver o [POST Persons API docs](/docs/api/post-data/#post-persons) para entender quais colunas podem ser declaradas ao importar Pessoas.

A API verificará por abreviações idênticas, que é a única restrição da [classe Pessoa](/docs/concepts/auxiliary-objects/#person). **Abreviações são exclusivas e duplicações não são permitidas**. Isso não impede que os dados baixados de repositórios tenham abreviações ou nomes completos diferentes para a mesma pessoa. Portanto, você deve padronizar os dados secundários antes de importá-los para o servidor para minimizar esses erros comuns.

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ"
cfg = odb_config(base_url=base_url, token = token)

one = data.frame(full_name='Adolpho Ducke',abbreviation='DUCKE, A.',notes='Grande botânico da Amazônia',stringsAsFactors = F)
two = data.frame(full_name='Michael John Gilbert Hopkins',abbreviation='HOPKINKS, M.J.G.',notes='Curador herbário INPA',stringsAsFactors = F)
to.odb= rbind(one,two)
odb_import_persons(to.odb,odb_cfg=cfg)

#pode adicionar um email

```

Pegar os dados

```r
library(opendatabio)
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
persons = odb_get_persons(odb_cfg=cfg)
persons = persons[order(persons$id,decreasing = T),]
head(persons,2)
```

resultado:


```
id                    full_name     abbreviation email institution                       notes
613 1582 Michael John Gilbert Hopkins HOPKINKS, M.J.G.  <NA>          NA       Curador herbário INPA
373 1581                Adolpho Ducke        DUCKE, A.  <NA>          NA Grande botânico da Amazônia
```
