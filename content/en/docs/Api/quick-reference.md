---
title: "Quick reference"
linkTitle: "Quick reference"
weight: 1
description: >
  List of endpoints and parameters!
---

{{< alert color="warning" title="API call format">}}
base-URL + '/api/v0/' + endpoint + '?' + request-parameters
{{< /alert >}}

{{< alert color="success" title="Taxons endpoint example">}}
http://opendb.inpa.gov.br/api/v0/taxons?valid=1&limit=2&offset=10
{{< /alert >}}


## GET DATA

<a name="shared-get-parameters"></a>
### Shared get-parameters

{{< alert color="danger">}}
When multiple parameters are specified, they are combined with an **AND** operator
{{< /alert >}}

All endpoints share these GET parameters:
- `id` return only the specified resource. May be number or a comma delimited list, such as `api/v0/locations?id=1,50,124`
- `limit`: the number of items that should be returned (must be greater than 0). Example: `api/v0/taxons?limit=10`
- `offset`: the initial record to extract, to be used with limit when trying to download a large amount of data. Example: `api/v0/taxons?offset=10000&limit=10000` returns 10K records starting from the 10K position of the current query.
- `fields`: the field or fields that should be returned. Each endpoint has its own fields but there are two special words, **simple** (default) and **all**, which return different collection of fields. `fields=all` may return sub-objects for each object. Example: `api/v0/taxons?fields=id,scientificName,valid`

{{< alert color="warning" title="wildcards">}}
Some parameters accept an asterisk as wildcard, so `api/v0/taxons?name=Euterpe` will return taxons with name exactly as "Euterpe", while `api/v0/taxons?name=Eut*` will return names starting with "Eut".
{{< /alert >}}

### Endpoint parameters

| Endpoint | Description | Possible parameters |
| --- | --- | --- |
| [/]() | Tests your access | none |
| [bibreferences](/docs/api/get-data/#bibreferences-endpoint) | Lists of bibliographic references | `id`, `bibkey` |
| [biocollections](/docs/api/get-data/#biocollections-endpoint) | List of Biocollections and other vouchers Repositories |  `id`  |
| [datasets](/docs/api/get-data/#datasets-endpoint) | Lists registered datasets | `id` |
| [individuals](/docs/api/get-data/#individuals-endpoint) | Lists registered individuals |`id`, `location`, `location_root`,`taxon`, `taxon_root`, `tag`,`project`, `dataset`|
| [individual-locations](/docs/api/get-data/#individual-locations-endpoint) | Lists occurrences for individuals |`individual_id`, `location`, `location_root`,`taxon`, `taxon_root`, `dataset`|
| [languages](/docs/api/get-data/#languages-endpoint) | Lists registered languages | |
| [measurements](/docs/api/get-data/#measurements-endpoint) | Lists Measurements | `id`, `taxon`,`dataset`,`trait`,`individual`,`voucher`,`location`|
| [locations](/docs/api/get-data/#locations-endpoint) | Lists locations | `root`, `id`, `parent_id`,`adm_level`, `name`, `limit`, `querytype`, `lat`, `long`,`project`,`dataset`|
| [persons](/docs/api/get-data/#persons-endpoint) | Lists registered people |`id`, `search`, `name`, `abbrev`, `email`|
| [projects](/docs/api/get-data/#projects-endpoint) | Lists registered projects | `id` only|
| [taxons](/docs/api/get-data/#taxons-endpoint) | Lists taxonomic names |`root`, `id`, `name`,`level`, `valid`, `external`, `project`,`dataset`|
| [traits](/docs/api/get-data/#traits-endpoint) | Lists variables (traits) list |`id`, `name`|
| [vouchers](/docs/api/get-data/#vouchers-endpoint) | Lists registered voucher specimens | `id`, `number`, `individual`, `location`, `collector`, `location_root`,`taxon`, `taxon_root`, `project`, `dataset` |
| [userjobs](/docs/api/get-data/#jobs-endpoint) | Lists user Jobs | `id`, `status`|


----

## POST DATA

{{< alert color="success" title="Web-interface note">}}
1. Importing data from files through the web-interface require specifying the POST verb parameters of the ODB API
1. Batch imports of  [Bibliographic References](/docs/concepts/auxiliary-objects/#bibreference) and [MediaFiles](/docs/concepts/auxiliary-objects/#media) are possible only through the web interface.
{{< /alert >}}


| Endpoint | Description | POST Fields |
| --- | --- | --- |
| [biocollections](/docs/api/post-data/#post-biocollections) | Import BioCollections | `name`, `acronym` |
| [individuals](/docs/api/post-data/#post-individuals) | Import individuals | `collector`**, `tag`**, `dataset`**, `date`**, (`location` or `latitude` + `longitude`)**, `altitude`, `location_notes`, `location_date_time`, `x`, `y`, `distance `, `angle `, `notes `, `taxon `, `identifier `, `identification_date `, `modifier`, `identification_notes `, `identification_based_on_biocollection`, `identification_based_on_biocollection_id `, `identification_individual` |
| [individual-locations](/docs/api/post-data/#post-individual-locations) | Import IndividualLocations | `individual`**, (`location` or `latitude` + `longitude`)**, `altitude`, `location_notes`, `location_date_time`, `x`, `y`, `distance`, `angle`|
| [locations](/docs/api/post-data/#post-locations) | Import locations | `name`**, `adm_level`**, (`geom` or `lat`+`long`)** , `parent`, `altitude`, `datum`, `x`, `y` , `startx`, `starty`, `notes`, `ismarine` |
| [measurements](/docs/api/post-data/#post-measurements) | Import Measurements to Datasets |`dataset`**, `date`**, `object_type`**, `object_type`**, `person`**, `trait_id`**, `value`**, `link_id`, `bibreference`, `notes`, `duplicated`|
| [persons](/docs/api/post-data/#post-persons) | Imports a list of people |`full_name`**, `abbreviation`, `email`, `institution`, `biocollection`|
| [traits](/docs/api/post-data/#post-traits) | Import traits | `export_name`**, `type`**, `objects`**, `name`**, `description`**, `units`, `range_min`, `range_max`, `categories`, `wavenumber_min` and `wavenumber_max`, `value_length`, `link_type`, `bibreference`  |
| [taxons](/docs/api/post-data/#post-taxons)  | Imports taxonomic names |`name`**, `level`, `parent`, `bibreference`, `author`, `author_id` or `person`, `valid`, `mobot`, `ipni`, `mycobank`, `zoobank`, `gbif`|
| [vouchers](/docs/api/post-data/#post-vouchers)  | Imports voucher specimens |`individual`**, `biocollection`**, `biocollection_type`, `biocollection_number`, `number`, `collector`, `date`, `dataset`, `notes` |


### Nomenclature types

|  Nomenclature types numeric codes  |
|:-----------------|:------------------|
|NotType :  0      |Isosyntype :  8    |
|Type :  1         |Neotype :  9       |
|Holotype :  2     |Epitype :  10      |
|Isotype :  3      |Isoepitype :  11   |
|Paratype :  4     |Cultivartype :  12 |
|Lectotype :  5    |Clonotype :  13    |
|Isolectotype :  6 |Topotype :  14     |
|Syntype :  7      |Phototype :  15    |


<a name="taxon-levels"></a>
### Taxon Level (Rank)

|Level |Level |Level  |Level|
|:----------------------------------------|:--------------------------------|:----------------------------------|:----------------------------------------|
|<span style='color:red'>-100</span>  `clade`                      |60  `cl., class`           |120  `fam., family`          |210  `section, sp., spec., species`|
|0  `kingdom`                       |70  `subcl., subclass`     |130  `subfam., subfamily`    |220  `subsp., subspecies`          |
|10  `subkingd.`                    |80  `superord., superorder`|150  `tr., tribe`            |240  `var., variety`               |
|30  `div., phyl., phylum, division`|90  `ord., order`          |180  `gen., genus`           |270  `f., fo., form`               |
|40  `subdiv.`                      |100  `subord.`             |190  `subg., subgenus, sect.`|  
