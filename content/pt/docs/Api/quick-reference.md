---
title: "Referência rápida"
linkTitle: "Referência rápida"
weight: 1
description: >
  Lista dos EndPoints e dos Parâmetros para GET e POST!
---

{{< alert color="warning" title="Formato da API">}}
base-URL + '/api/v0/' + endpoint + '?' + request-parameters
{{< /alert >}}

{{< alert color="success" title="Exemplo para o Taxons endpoint">}}
http://opendb.inpa.gov.br/api/v0/taxons?valid=1&limit=2&offset=10
{{< /alert >}}


## OBTER DADOS - GET

<a name="shared-get-parameters"></a>
### Parâmetros GET compartilhados

{{< alert color="danger">}}
Quando múltiplos parâmetros são especificados eles são combinados com o operador **AND**. Não há opção de parâmetro **OR** nas suas buscas.{{< /alert >}}

Todos os endpoints compartilham estes parâmetros GET:
- `id` retorna apenas o recurso especificado. Pode ser um número ou uma lista delimitada por vírgulas, como `api/v0/locations? Id = 1,50,124`
- `limit`: limita o número de itens que devem ser retornados (deve ser maior que 0). Exemplo: `api/v0/taxons?limit=10`
- `offset`: o registro inicial a partir do qual deseja extrair, para ser usado com limite ao tentar baixar uma grande quantidade de dados. Exemplo: `api/v0/taxons?offset=1000&limit=10` retorna 10 registros começando da posição 1K da consulta realizada.
- `fields`: o campo ou campos que devem ser retornados. Cada EndPoint possui seus próprios campos, mas há duas palavras especiais, **simple** (padrão) e **all**, que retornam diferentes coleções de campos. `fields = all` pode retornar sub-objetos para cada objeto. Exemplo: `api/v0/taxons?fields=id,scientificName,valid`

{{< alert color="warning" title="wildcards">}}
Alguns parâmetros aceitam um asterisco como curinga, então `api/v0/taxons?name=Euterpe` retornará táxons com nome exatamente como "Euterpe", enquanto `api/v0/taxons?name=Eut*` retornará nomes começando com "Eut".
{{< /alert >}}

### Parâmetros GET específicos

| Endpoint | Descrição | Parâmetros possíveis |
| --- | --- | --- |
| [/]() | Testa o acesso | nenhum |
| [bibreferences](/docs/api/get-data/#bibreferences-endpoint) | Obter referências bibliográficas | `id`, `bibkey` |
| [biocollections](/docs/api/get-data/#biocollections-endpoint) | Lista de BioColeções registradas |  `id`  |
| [datasets](/docs/api/get-data/#datasets-endpoint) | Lista de Conjuntos de Dados  | `id` |
| [individuals](/docs/api/get-data/#individuals-endpoint) | Obter dados de Indivíduos (organisms) |`id`, `location`, `location_root`,`taxon`, `taxon_root`, `tag`,`project`, `dataset`|
| [individual-locations](/docs/api/get-data/#individual-locations-endpoint) | Obter localizações (ocorrências) de indivíduos |`individual_id`, `location`, `location_root`,`taxon`, `taxon_root`, `dataset`|
| [languages](/docs/api/get-data/#languages-endpoint) | Lista de Idiomas | |
| [measurements](/docs/api/get-data/#measurements-endpoint) | Obter Medições | `id`, `taxon`,`dataset`,`trait`,`individual`,`voucher`,`location`|
| [locations](/docs/api/get-data/#locations-endpoint) | Obter localidades | `root`, `id`, `parent_id`,`adm_level`, `name`, `limit`, `querytype`, `lat`, `long`,`project`,`dataset`|
| [persons](/docs/api/get-data/#persons-endpoint) | Lista de Pessoas |`id`, `search`, `name`, `abbrev`, `email`|
| [projects](/docs/api/get-data/#projects-endpoint) | Lista de projects | `id`|
| [taxons](/docs/api/get-data/#taxons-endpoint) | Lista de Taxons |`root`, `id`, `name`,`level`, `valid`, `external`, `project`,`dataset`|
| [traits](/docs/api/get-data/#traits-endpoint) | Lista Variáveis (traits) |`id`, `name`|
| [vouchers](/docs/api/get-data/#vouchers-endpoint) | Lista Vouchers | `id`, `number`, `individual`, `location`, `collector`, `location_root`,`taxon`, `taxon_root`,`project`, `dataset` |
| [userjobs](/docs/api/get-data/#jobs-endpoint) | Lista Jobs de Usuário | `id`, `status`|


----

## Importar dados - POST

{{< alert color="success" title="Atenção">}}
1. A importação de dados de arquivos por meio da interface da web requer a especificação dos parâmetros do verbo POST da API listados abaixo
1. As importações em lote de [Referências bibliográficas](/docs/concepts/auxiliary-objects/#bibreference) e [Arquivos de Mídia](/docs/concepts/auxiliary-objects/#media) só são possíveis por meio da interface no navegador.
{{< /alert >}}


| Endpoint | Descrição |  Parâmetros POST |
| --- | --- | --- |
| [biocollections](/docs/api/post-data/#post-biocollections) | Importar BioColeções | `name`, `acronym` |
| [individuals](/docs/api/post-data/#post-individuals) | Importar Indivíduos (organims)  | `collector`**, `tag`**, `dataset`**, `date`**, (`location` or `latitude` + `longitude`)**, `altitude`, `location_notes`, `location_date_time`, `x`, `y`, `distance `, `angle `, `notes `, `taxon `, `identifier `, `identification_date `, `modifier`, `identification_notes `, `identification_based_on_biocollection`, `identification_based_on_biocollection_id `, `identification_individual`,`biocollection`, `biocollection_number`,`biocollection_type` |
| [individual-locations](/docs/api/post-data/#post-individual-locations) | Importar Localidades para Indivíduos registrados | `individual`**, (`location` or `latitude` + `longitude`)**, `altitude`, `location_notes`, `location_date_time`, `x`, `y`, `distance`, `angle`|
| [locations](#endpoint_locations) | Importar localidades | `name`**, `adm_level`**, (`geom` or `lat`+`long`)** , `parent`, `altitude`, `datum`, `x`, `y` , `startx`, `starty`, `notes`, `ismarine` |
| [measurements](#endpoint_measurements) | Importar Medições  |`dataset`**, `date`**, `object_type`**, `object_type`**, `person`**, `trait_id`**, `value`**, `link_id`, `bibreference`, `notes`, `duplicated`|
| [persons](#endpoint_persons) | Importar Pessoas |`full_name`**, `abbreviation`, `email`, `institution`, `biocollection`|
| [traits](#endpoint_traits) | Importar Variáveis | `export_name`**, `type`**, `objects`**, `name`**, `description`**, `units`, `range_min`, `range_max`, `categories`, `wavenumber_min` and `wavenumber_max`, `value_length`, `link_type`, `bibreference`  |
| [taxons](#endpoint_taxons)  | Importar Nomes Taxonômicos |`name`**, `level`, `parent`, `bibreference`, `author`, `author_id` or `person`, `valid`, `mobot`, `ipni`, `mycobank`, `zoobank`, `gbif`|
| [vouchers](#endpoint_vouchers)  | Importar Vouchers |`individual`**, `biocollection`**, `biocollection_type`, `biocollection_number`, `number`, `collector`, `date`, `dataset`, `notes` |
***
** indica campos obrigatórios.

### Nomenclature Types

|  Tipo Nomenclatural : código numérico  |
|:-----------------|:------------------|
|NotType :  0      |Isosyntype :  8    |
|Type :  1         |Neotype :  9       |
|Holotype :  2     |Epitype :  10      |
|Isotype :  3      |Isoepitype :  11   |
|Paratype :  4     |Cultivartype :  12 |
|Lectotype :  5    |Clonotype :  13    |
|Isolectotype :  6 |Topotype :  14     |
|Syntype :  7      |Phototype :  15    |


### Níveis taxonômicos (Ranks)

|Nível |Nível |Nível  |Nível|
|:----------------------------------------|:--------------------------------|:----------------------------------|:----------------------------------------|
|<span style='color:red'>-100</span>  `clade`                      |60  `cl., class`           |120  `fam., family`          |210  `section, sp., spec., species`|
|0  `kingdom`                       |70  `subcl., subclass`     |130  `subfam., subfamily`    |220  `subsp., subspecies`          |
|10  `subkingd.`                    |80  `superord., superorder`|150  `tr., tribe`            |240  `var., variety`               |
|30  `div., phyl., phylum, division`|90  `ord., order`          |180  `gen., genus`           |270  `f., fo., form`               |
|40  `subdiv.`                      |100  `subord.`             |190  `subg., subgenus, sect.`|                                         |
