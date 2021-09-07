---
title: "Obter dados - GET"
linkTitle: "Obter dados - GET"
weight: 2
description: >
  Como obter dados usando a GET API!
---

{{< alert color="warning">}}
- O [pacote OpenDataBio-R](https://github.com/opendatabio/opendatabio-r) é um **cliente** para esta API.
- Não é necessária autenticação para acessar dados com uma política de acesso público
- Token de autenticação necessário apenas para obter dados com uma política de acesso não pública
{{< /alert >}}


## BibReferences Endpoint

> O endpoint `bibreferences` interage com a tabela [bibreferences](/docs/concepts/auxiliary-objects/# bibreferences). Seu uso básico é obter as referências bibliográficas registradas.

### Parâmetros GET

- `id = list` retorna apenas referências com o id ou ids fornecidos (ex` id = 1,2,3,10`)
- `bibkey = list` retorna apenas referências com bibkey ou bibkeys (ex` bibkey = ducke1953, mayr1992`)
- `taxon = lista de ids` retorna apenas referências vinculadas ao táxon informado.
- `limit` e ` offset`. consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).

### Campos de resposta
  - `id`- o id de BibReference na tabela bibreferences (um id de banco de dados local)
  - `bibkey` - a bibkey usada para pesquisar e usar a referência no sistema web
  - `year` - o ano de publicação
  - `author` - os autores da publicação
  - `title` - o título da publicação
  - `doi` - a publicação DOI, se presente
  - `url` - um url externo para a publicação, se presente
  - `bibtex` - o registro de citação de referência no formato [BibTex] (http://www.bibtex.org/)

***

## Datasets Endpoint

> O endpoint `datasets` interage com a tabela [Conjuntos de Dados] (/docs/concepts/data-access/#datasets). Seu uso básico é obter os conjuntos de dados registrados, mas não os dados nos conjuntos de dados (use a interface para obter os dados completos de um conjunto de dados ou recupere os dados usando as API específicas do objeto do conjunto de dados. Útil apenas para obter dataset_ids para importar medições dinamicamente.

### Parâmetros GET

- `id = list` retorna apenas referências com o id ou ids fornecidos (ex` id = 1,2,3,10`)

### Campos de resposta
- `id` - o id do conjunto de dados na tabela de conjuntos de dados (um id de banco de dados local)
  - `name` - o nome do conjunto de dados
  - `privacyLevel` - o nível de acesso para o conjunto de dados
  - `contactEmail` - o e-mail do administrador do conjunto de dados
  - `description` - uma descrição do conjunto de dados
  - `policy` - a política de dados, se especificada
  - `measurements_count` - o número de medições no conjunto de dados
  - `taggedWidth` - a lista de tags aplicadas ao conjunto de dados

***

## Biocollections Endpoint

> O endpoint `biocollections` interage com a tabela [BioColeções](/docs/concepts/data-access/#biocollections). Seu uso básico é obter a lista das Coleções Biológicas cadastradas no banco de dados. Usando para obter `biocollection_id` ou validar seus códigos para importar dados com os pontos de extremidade [Vouchers](#vouchers-endpoint) ou [Indivíduos](#individuals-endpoint).

### Parâmetros GET

- `id = list` retorna apenas as Biocoleções tendo o id ou ids fornecidos (ex` id = 1,2,3,10`)
- `acronym` retorna apenas Biocoleções tendo o acrônimo ou acrônimo fornecido (ex` acronym = INPA, SP, NY`)

### Campos de resposta
  - `id` - o id do repositório ou museu na tabela de Biocoleções (um id de banco de dados local)
  - `name` - o nome do repositório ou museu
  - `acronym` - o acrônimo do repositório ou museu
  - `irn` - apenas para Herbarios, o número do herbário no [Index Herbariorum](http://sweetgum.nybg.org/science/ih/)

***
## Individuals Endpoint

> O endpoint `individuals` interage com a modelo [Indivíduos](/docs/concepts/core-objects/#individuals). Seu uso básico é obter uma lista de indivíduos e dados associados.

### Parâmetros GET

- `id = número ou lista` - retorna indivíduos que têm id ou ids ex:` id = 2345,345`
- `location = mixed` - retorna indivíduos por localidade, pode ser id ou nome ex:` location = 24,25,26` `location = Parcela 25ha` (ligações diretas)
- `location_root` - igual a location, mas retorna também dos descendentes das localidades informadas (ligações espaciais, tudo o que está dentro da(s) localidade(s))
- `taxon = mixed` - o id ou ids, ou nomes de táxons  (nomes completos, canonicalName) ex:` taxon = Aniba, Ocotea guianensis, Licaria cannela tenuicarpa` ou `taxon = 456.789,3,4`
- `taxon_root` - igual a taxon mas retorna também todos os indivíduos identificados como qualquer um dos descendentes dos táxons informados (por exemplo, `taxon=Lauraceae` retorn todos os indivíduos da família)
- `project = mixed` - o id ou ids ou nomes do projeto, ex:` project = 3` ou `project = OpenDataBio`
- `tag = list` - um ou mais códigos de identificação do individual, ex:` tag = individuala1,2345,2345A`
- `dataset = mixed` - o id ou ids ou nomes dos conjuntos de dados, retorna os indivíduos que têm medições nos conjuntos de dados informados
- `limit` e` offset` -  Consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).

{{< alert color="primary" title="Note">}}
Observe que todos os campos de pesquisa (táxon, localidades e conjunto de dados) podem ser especificados como nomes (por exemplo, "taxon = Euterpe edulis") ou como ids de banco de dados. Se uma lista for especificada para um desses campos, todos os itens da lista devem ser do mesmo tipo, ou seja, você não pode pesquisar por 'taxon = Euterpe, 24'. Além disso, location e taxon têm prioridade sobre location_root e taxon_root se ambos forem informados.
{{< /alert >}}


### Campos de resposta
  - `id` - o id do indivíduo na tabela de individuals (um id de banco de dados local)
  - `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - será sempre [DWC organism](https://dwc.tdwg.org//terms//#organism)
  - `organismID` [DWC](https://dwc.tdwg.org/terms/#dwc:organismID) - uma combinação única local de informações de registro, composta de recordNumber,recordedByMain,locationName
  - `recordedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedBy) - lista separada de abreviações de pessoas registradas (usando pipe " | ")
  - `recordedByMain` - a primeira pessoa listada em recordedBy
  - `recordNumber` [DWC](https://dwc.tdwg.org/terms/#dwc:recordNumber) - um identificador para o indivíduo, pode ser o código em uma etiqueta de alumínio de árvore, um código num anilha de uma ave, um número de coletor
  - `recordedDate` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedDate) - a data de registro
  - `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName) - a identificação taxonômica atual do indivíduo (sem autores) ou "unidentified"
  - `scientificNameAuthorship` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificNameAuthorship) - a autoria do táxon. Para **taxonomicStatus** = _não publicado_ será um nome de pessoa registrado ODB
  - `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family)
  - `genus` [DWC](https://dwc.tdwg.org/terms/#dwc:genus)
  - `identificationQualifier` [DWC](https://dwc.tdwg.org/terms/#dwc:identificationQualifier) - modificador do nome identificado cf. aff. s.l., etc.
  - `identifiedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:identifiedBy) - pessoa que atribuiu o scientificName  
  - `dateIdentified` [DWC](https://dwc.tdwg.org/terms/#dwc:dateIdentified) - data da identificação
  - `identificationRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:identificationRemarks) - notas da identificação
  - `locationName` - nome da localidade
  - `locationParentName` - nome da localidade pai da localidade do indivíduo
  - `higherGeography` [DWC](https://dwc.tdwg.org/terms/#dwc:higherGeography) - a hierarquia completa separada por pipe ('|') - e.g. Brasil | Amazonas | Rio Preto da Eva | Fazenda Esteio | Reserva km37 | Manaus ForestGeo-PDBFF Plot | Quadrat 100x100 );
  - `decimalLatitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLatitude) - a latitude em graus decimais. O valor vai depender do tipo de localidade e da posição relativa do indivíduo (i.e. se houver atributos X e Y, ou Angle e Distance eles serão usados para calcular este valor). Se o indivíduo tiver multiplas localidades registradas (e.g. uma ave monitorada), esta será a coordenada da última localidade registrada.
  - `decimalLongitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLongitude) - mesmo que decimalLatitude, mas para longitude.
  - `x` - a posição X do indivíduo dentro de uma localidade do tipo Parcela
  - `y` - a posição Y do indivíduo dentro de uma localidade do tipo Parcela
  - `gx` - a posição X do indivíduo dentro da localidade pai, quando a localidade for uma subparcela (e.g. protocolo ForestGeo)
  - `gy` - a posição Y do indivíduo dentro da localidade pai, quando a localidade for uma subparcela (e.g. protocolo ForestGeo)
  - `angle` - a direção do azimute da posição do indivíduo em relação uma coordenada geográfica (POINT location)
  - `distance` - a distância do indivíduo a coordenada geográfica (POINT location), quando `angle` é informado.
  - `organismRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:organismRemarks) - notas associadas ao registro do Indivíduo
  - `associatedMedia` [DWC](https://dwc.tdwg.org/terms/#dwc:associatedMedia) - urls para arquivos de media ligados ao indivíduo quando for o caso
  - `datasetName` - nome do Conjunto de Dados ao qual o indivíduo pertence [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
  - `accessRights` - a política de acesso do Conjunto de dados - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
  - `bibliographicCitation` - a citação para Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
  - `license` - a licensa para o Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:license)

***

## Individual-locations Endpoint

> O endpoint `individual-locations` interage com a tabela [individual_location](/docs/concepts/core-objects/#individuals). Seu uso básico é obter dados de ocorrência de indivíduos. Projetado para ocorrências de organismos que se movem e têm vários locais, caso contrário, a mesma informação é recuperada com o [Endpoint Individuals](#individuals-endpoint).


### Parâmetros GET

- `individual_id = número ou lista` - retorna indivíduos que têm id ou ids ex:` id = 2345,345`
- `location = mixed` - retorna indivíduos por localidade, pode ser id ou nome ex:` location = 24,25,26` `location = Parcela 25ha` (ligações diretas)
- `location_root` - igual a location, mas retorna também dos descendentes das localidades informadas (ligações espaciais, tudo o que está dentro da(s) localidade(s))
- `taxon = mixed` - o id ou ids, ou nomes de táxons  (nomes completos, canonicalName) ex:` taxon = Aniba, Ocotea guianensis, Licaria cannela tenuicarpa` ou `taxon = 456.789,3,4`
- `taxon_root` - igual a taxon mas retorna também todos os indivíduos identificados como qualquer um dos descendentes dos táxons informados (por exemplo, `taxon=Lauraceae` retorn todos os indivíduos da família)
- `dataset = mixed` - o id ou ids ou nomes dos conjuntos de dados, retorna os indivíduos que têm medições nos conjuntos de dados informados
- `limit` e` offset` -  Consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).


{{< alert color="primary" title="Note">}}
Observe que todos os campos de pesquisa (táxon, localidades e conjunto de dados) podem ser especificados como nomes (por exemplo, "taxon = Euterpe edulis") ou como ids de banco de dados. Se uma lista for especificada para um desses campos, todos os itens da lista devem ser do mesmo tipo, ou seja, você não pode pesquisar por 'taxon = Euterpe, 24'. Além disso, location e taxon têm prioridade sobre location_root e taxon_root se ambos forem informados.
{{< /alert >}}

#### Campos de resposta
- `individual_id` - o id do indivíduo na tabela de individuals (um id de banco de dados local)
- `location_id` - o id do local na tabela de locations (um id de banco de dados local)
- `basisOfRecord` - será sempre 'occurrence' - [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) e [DWC occurrence](https://dwc.tdwg.org/terms/#occurrence);
- `occurrenceID` - o identificador único para este registro, o indivíduo + local + date_time - [DWC](https://dwc.tdwg.org/terms/#dwc:occurrenceID)
- `organismID` - o identificador exclusivo para o indivíduo [DWC](https://dwc.tdwg.org/terms/#dwc:organismID)
- `recordedDate` - a data e hora do registro da ocorrência  [DWC] (https://dwc.tdwg.org/terms/#dwc:recordedDate)
- `locationName` - o nome da localidade
- `higherGeography` [DWC](https://dwc.tdwg.org/terms/#dwc:higherGeography) - a hierarquia completa separada por pipe ('|') - e.g. Brasil | Amazonas | Rio Preto da Eva | Fazenda Esteio | Reserva km37 | Manaus ForestGeo-PDBFF Plot | Quadrat 100x100 );
- `decimalLatitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLatitude) - a latitude em graus decimais. O valor vai depender do tipo de localidade e da posição relativa do indivíduo (i.e. se houver atributos X e Y, ou Angle e Distance eles serão usados para calcular este valor). Se o indivíduo tiver multiplas localidades registradas (e.g. uma ave monitorada), esta será a coordenada da última localidade registrada.
- `decimalLongitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLongitude) - mesmo que decimalLatitude, mas para longitude.
- `x` - a posição X do indivíduo dentro de uma localidade do tipo Parcela
- `y` - a posição Y do indivíduo dentro de uma localidade do tipo Parcela
ForestGeo)
- `angle` - a direção do azimute da posição do indivíduo em relação uma coordenada geográfica (POINT location)
- `distance` - a distância do indivíduo a coordenada geográfica (POINT location), quando `angle` é informado.
- `organismRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:organismRemarks) - notas associadas ao registro do Indivíduo
- `associatedMedia` [DWC](https://dwc.tdwg.org/terms/#dwc:associatedMedia) - urls para arquivos de media ligados ao indivíduo quando for o caso
- `datasetName` - nome do Conjunto de Dados ao qual o indivíduo pertence [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - a política de acesso do Conjunto de dados - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - a citação para Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - a licensa para o Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:license)
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName) - a identificação taxonômica atual do indivíduo (sem autores) ou "unidentified"
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family)

- `datasetName` - nome do Conjunto de Dados ao qual o indivíduo pertence [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - a política de acesso do Conjunto de dados - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - a citação para Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - a licensa para o Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:license)
- `minimumElevation` - a altitude da ocorrência, se registrado - [DWC](https://dwc.tdwg.org/terms/#dwc:minimumElevation)
- `occurrenceRemarks` - qualquer nota associada ao registro de ocorrência do indivíduo - [DWC](https://dwc.tdwg.org/terms/#dwc:occurrenceRemarks)


***

## Measurements Endpoint

> O endpoint `measurements` interage com o modelo [Medições](/docs/concepts/auxiliary-objects/#measurements). Seu uso básico é obter dados vinculados a indivíduos, táxons, localidades ou vouchers, independentemente dos conjuntos de dados, por isso é útil quando você deseja obter medições específicas de diferentes conjuntos de dados aos quais você tem acesso. Se você quiser um conjunto de dados completo, mais fácil usar a interface da web, pois ela prepara um conjunto completo de medições do conjunto de dados e todas as tabelas de dados associados.

### Parâmetros GET

- `id = lista de ids` retorna apenas a medição ou medições tendo o id ou ids fornecidos (ex` id = 1,2,3,10`)
- `taxon = lista de ids ou nomes` retorna apenas as medições relacionadas aos táxons, tanto medidas diretas quanto medidas indiretas de seus indivíduos e vouchers. Não considera os táxons descendentes para esse uso, em vez disso, use `taxon_root`. Por exemplo, ` taxon = Aniba, Licaria` irá retornar apenas medições diretamente ligadas ao gênero e vouchers identificados em nível de gênero.
- `taxon_root = lista de ids ou nomes` semelhantes a` taxon`, mas obtém também medições para táxons descendentes da consulta informada (ex `taxon = Lauraceae` obterá medições vinculadas a Lauraceae e qualquer táxon que pertença a ela);
- `dataset = lista de ids` retorna apenas as medições pertencentes aos datasets informados (ex` dataset = 1,2`)
- `trait = lista de ids ou export_names` retorna apenas as medições para as variáveis informadas (ex` trait=DBH, DBHpom` ou `dataset=2&trait=DBH`)
- `individual = lista de ids indivíduos` retorna apenas as medições para os indivíduos com esses id informados (ex` individual = 1000,1200`)
- `voucher = lista de ids de vouchers` retorna apenas as medições para os vouchers  com esses id informados  (ex` voucher = 345,321`)
- `location = lista de ids de localidades` retorna apenas medições para as localidades informados (ex:` location = 4,3,2,1`) - não recupera medições para indivíduos e vouchers nessas locais, apenas medições relacionados às próprias localidaes, como, por exemplo, dados de levantamentos de solo de parcelas.
- `limit` e` offset` -  Consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).

### Campos de resposta
- `id` - o id da medição na tabela de measurements (id do banco de dados local)
- `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - será sempre 'MeasurementsOrFact' [dwc measureorfact]([DWC measureorfact](https://dwc.tdwg.org/termos/#measurementsOrFact)
- `measured_type` - o objeto medido, um de 'Individual', 'Location', 'Taxon' ou 'Voucher'
- `measured_id` - o id do objeto medido na respectiva tabela de objetos (individuals.id, locations.id, taxons.id, vouchers.id)
- `measurementID` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - um identificador único para a  medição
- `measurementType` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - o export_name para a Variável medida
- `measurementValue` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - o valor da medição - dependerá do tipo de medição [ver Variáveis](/docs/trait-objects)
- `measurementUnit` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - a unidade de medida para variáveis quantitativas
- `measurementDeterminedDate` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - a data da medição
- `measurementDeterminedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:measurementDeterminedBy) - Pessoa responsável pela medição
- `measurementRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:measurementRemarks) - nota de texto associada a este registro de medição
- `resourceRelationship` [DWC](https://dwc.tdwg.org/terms/#dwc:resourceRelationship) - o objeto medido (recurso) - um de 'location','taxon','organism','preservedSpecimen'
- `resourceRelationshipID` [DWC](https://dwc.tdwg.org/terms/#dwc:resourceRelationshipID) - o id do resourceRelationship
- `relationshipOfResource` [DWC](https://dwc.tdwg.org/terms/#dwc:relationshipOfResource) - será sempre 'measurement of'
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName) - a identificação taxonômica relacionada à medição, se for o caso
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family) - a família taxonômica, se for o caso.
- `datasetName` - nome do Conjunto de Dados ao qual a medição pertence [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - a política de acesso do Conjunto de dados - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - a citação para Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - a licensa para o Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:license)

***
## Media Endpoint

> O endpoint `media` interage com a tabela [media](/docs/concepts/auxiliary-objects/#mediafiles). Seu uso básico é obter os metadados associados aos arquivos de mídia, incluindo a URL dos arquivos.

### Parâmetros GET

- `individual = número ou lista` - retorna mídia associada aos indivíduos com id ou ids, por exemplo:` id = 2345,345`
- `voucher = number or list` - retorna mídia associada aos vouchers com id ou ids, por exemplo:` id = 2345,345`
- `location = mixed` - retorna mídia associada a localidades, pode ser id ou nome ex:` location = 24,25,26` `location = Parcela 25ha` (ligações diretas)
- `location_root` - igual a location, mas retorna também dos descendentes das localidades informadas (ligações espaciais, tudo o que está dentro da(s) localidade(s))
- `taxon = mixed` - o id ou ids, ou nomes de táxons  (nomes completos, canonicalName) ex:` taxon = Aniba, Ocotea guianensis, Licaria cannela tenuicarpa` ou `taxon = 456.789,3,4`
- `taxon_root` - igual a taxon mas retorna também todos os indivíduos identificados como qualquer um dos descendentes dos táxons informados (por exemplo, `taxon=Lauraceae` retorn todos os indivíduos da família)
- `dataset = mixed` - o id ou ids ou nomes dos conjuntos de dados, retorna mídia pertencentes aos conjuntos de dados informados
- `limit` e` offset`-  Consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).

{{< alert color="primary" title="Note">}}
Observe que todos os campos de pesquisa (táxon, localidades e conjunto de dados) podem ser especificados como nomes (por exemplo, "taxon = Euterpe edulis") ou como ids de banco de dados. Se uma lista for especificada para um desses campos, todos os itens da lista devem ser do mesmo tipo, ou seja, você não pode pesquisar por 'taxon = Euterpe, 24'. Além disso, location e taxon têm prioridade sobre location_root e taxon_root se ambos forem informados.
{{< /alert >}}


#### Response fields
- `id` - o id da medição na tabela measurements (local database id)
- `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - será sempre 'MachineObservation' [DWC](https://dwc.tdwg.org/terms/#machineobservation)
- `model_type` - o objeto relacionado 'Individual', 'Location', 'Taxon' or 'Voucher'
- `model_id` - o id do objeto relacionado na respectiva tabela (individuals.id, locations.id, taxons.id, vouchers.id)
- `resourceRelationship` [DWC](https://dwc.tdwg.org/terms/#dwc:resourceRelationship) - o objeto relacionado - one of 'location','taxon','organism','preservedSpecimen'
- `resourceRelationshipID` [DWC](https://dwc.tdwg.org/terms/#dwc:resourceRelationshipID) - o id não numérico do resourceRelationship
- `relationshipOfResource` [DWC](https://dwc.tdwg.org/terms/#dwc:relationshipOfResource) - será um 'dwcType'
- `recordedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedBy) - lista separada por pipe "|" das Pessoas autores
- `recordedDate` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedDate) - a data do arquivo de mídia
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName)  - a identificação taxonômica do objeto na mídia, quando for o caso.
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family)
- `dwcType` [DWC](https://dwc.tdwg.org/terms/#type) - um de StillImage, MovingImage, Sound
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family) - a família taxonômica, se for o caso.
- `datasetName` - nome do Conjunto de Dados ao qual a mídia pertence [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - a política de acesso do Conjunto de dados - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - a citação para Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - a licensa para o Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:license)
- `file_name` - nome do arquivo
- `file_url` - o endereço do arquivo

***
## Languages EndPoint

> O endpoint `languages` interage com a tabela [Language](/docs/concepts/auxiliary-objects/#user_translations). Seu uso básico é obter uma lista de idiomas registrados para importar [Traduções do usuário](/docs/concepts/auxiliary-objects/#user_translations) como é o caso dos nomes de [Variáveis](#traits-endpoint) e categorias, ou descrições de arquivos de mídia.

### Campos de resposta
  - `id` - o id do idioma na tabela de idiomas (um id de banco de dados local)
  - `code` - o código do idioma;
  - `name` - o nome do idioma;

***

## Locations Endpoint

> O endpoint `locations` interage com a modelo [Localidades](/docs/concepts/core-objects/#locations). Seu uso básico é obter uma lista de localidades registradas

### Parâmetros GET

- `id = list` retorna apenas localidades com o id ou ids fornecidos (ex:` id = 1,2,3,10`)
- `adm_level = number` retorna apenas localidades para o nível ou tipo especificado:
  - **2** para o país; **3** para a primeira divisão dentro do país (província, estado); **4** para a segunda divisão (por exemplo, município) ... até adm_level 10 como áreas administrativas (Geometria: polígono, multipolygon);
  - **97** é o código para Camadas Ambientais (Geometria: polygon, multipolygon);
  - **98** é o código para Áreas Indígenas (Geometria: polygon, multipolygon);
  - **99** é o código para Unidades de Conservação (Geometria: polygon, multipolygon);
  - **100** é o código para PARCELAS e SUB-PARCELAS (Geometria: polygon ou point);
  - **101** para transectos (geometria: point ou linestring)
  - **999** para qualquer localidades de 'PONTO' como waypoints GPS (Geometria: point);
- `name = string` retorna apenas as localidades cujo nome corresponde à string de pesquisa. Você pode usar asterisco como curinga. Exemplo: `nome=Manaus` ou` nome=*Ducke* `para encontrar o nome que possui a palavra Ducke;
- `parent_id = list` retorna as localidades cujo pai imediato na lista (ex: parent_id = 2,3)
- `root = number` id da localidade para pesquisar, retorna as localidades para a id especificada junto com todas as localidades descendentes; exemplo: encontre o id para o Brasil e use seu id como root para obter todos as localidades pertencentes ao Brasil;
- `querytype` um de "exact","parent" ou "closest" e deve ser fornecido com` lat` e `long`:
    - quando `querytype = exact` encontrará uma localidade do tipo POINT que tem a correspondência exata de` lat` e `long`;
    - quando `querytype = parent` encontrará a localidade pai mais inclusiva dentro da qual as coordenadas fornecidas por` lat` e `long` caem;
    - quando `querytype = closest` irá encontrar a localidade mais próxima das coordenadas dadas por` lat` e `long`; Ele pesquisará apenas os locais mais próximos com `adm_level>99`, veja acima.
    - `lat` e` long` devem ser coordenadas válidas em graus decimais (negativo para Sul e Oeste);
- `fields = list` especifica quais campos você deseja obter com sua consulta (veja abaixo os nomes dos campos), ou use as opções 'all' ou 'simple', para obter o conjunto completo e as colunas mais importantes, respectivamente
- `project = mixed` - id ou nome do projeto (pode ser uma lista) retorna as localidades utilizadas em um ou mais projetos
- `dataset = mixed` - id ou nome de um conjunto de dados (pode ser uma lista) retorna as localidades utilizadas a um ou mais conjuntos de dados
- `limit` e` offset`-  Consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).

Note que `id`, `search`, `parent` e `root` não devem ser combinadas na mesma busca;


### Response fields

- `id` - o id da localidade na tabela locations (um id de banco de dados local)
- `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - sempre conterá 'location' ([DWC location](https://dwc.tdwg.org/terms/#location)
- `locationName` - o nome do local (se o país for o nome do país, se indicar o nome do estado, etc ...)
- `adm_level` - o valor numérico para o nível administrativo ODB (2 para países, etc)
- `levelName` - o nome do nível administrativo ODB
- `parent_id` - o id da localidade pai na tabela locations
- `parentName` - o nome do pai imediato da localidade
- `higherGeography` [DWC] (https://dwc.tdwg.org/terms/#dwc:higherGeography) - o caminho completo das localidades pais,(por exemplo, Brasil | São Paulo | Cananéia);
- `footprintWKT` [DWC](https://dwc.tdwg.org/terms/#dwc:footprintWKT) - a representação geométrica da localidade em formato [WKT](https://en.wikipedia.org/wiki/Well-known_text); para localidades com `adm_level==100` (parcelas) ou `adm_level==101` mas informadas como POINT, as geometrias do polygon ou linestring, respectivamente, serão geradas a partir das dimensões especificadas para essas localidades.
  - `x` e `y` - (metros) para localidades do tipo Parcela (100 == adm_level), as dimensões X e Y. Para localidades do tipo Transecto (101 == adm_level), x pode ser o comprimento do transecto e Y um valor de buffer para inclusão de indivíduos
  - `startx` e `starty` - (meters) se a localidde for uma subparcela (100 == adm_level com localidade pai também adm_level==100), esses valores representam a posição X e Y da subparcela em relação as coordenadas 0,0 da parcela pai;
  - `distance` - apnas quando **querytype=closest** este valor representa a distância em metros da localidade pesquisada a localidade obtida
  - `locationRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:locationRemarks) - quaquer nota associada ao registro da localidade
  - `decimalLatitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLatitude) - depende do adm_level: se adm_level<=99, a latitude do centroid; se adm_level == 999 (point), a latitude do ponto; se adm_level==100 (parcela) or 101 (transecto), mas estes informados com uma geometria do tipo POINT, a latitude do POINT, caso contrário, se geometria POLYGON ou LINESTRING, o a coordenada do primeiro ponto da geometria.
  - `decimalLongitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLongitude) - mesmo para decimalLatitude
  - `georeferenceRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:georeferenceRemarks) - explicação sobre como decimalLatitude e decimalLongitude form calculados
  - `geodeticDatum` -[DWC](https://dwc.tdwg.org/terms/#dwc:geodeticDatum) - o geodeticDatum informado para a geometria (ODB não faz reprojeções espaciais, assumes que dados espaciais são sempre WSG84)

***
## Persons Endpoint

> O endpoint `persons` interage com a tabela [Person](/docs/concepts/auxiliary-objects/#persons). O uso básico é obter uma lista de pessoas cadastradas (coletores de dados, especialistas taxonômicos, etc).

### Parâmetros GET

- `id = list` retorna apenas pessoas com o id ou ids fornecidos (ex:` id = 1,2,3,10`)
- `name = string` - retorna pessoas cujo nome corresponde à string especificada. Você pode usar asterisco como curinga. Ex: `name =*ducke*`
- `abbrev = string`, retorna pessoas cuja abreviação corresponde à string especificada. Você pode usar asterisco como curinga.
- `email = string`, retorna pessoas cujo e-mail corresponde à string especificada. Você pode usar asterisco como curinga.
- `search = string`, retorna pessoas cujo nome, abreviatura ou e-mail corresponde à string especificada. Você pode usar asterisco como curinga.
- `limit` e` offset`-  Consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).

#### Campos de resposta
- `id` - o id da pessoa na tabela de pessoas (um id de banco de dados local)
- `full_name` - o nome da pessoa;
- `abbreviation` - o nome da pessoa (estes são valores ÚNICOS em um banco de dados OpenDataBio)
- `email` - o e-mail, se registrado ou a pessoa é o usuário
- `institution` - a instituição de pessoas, se registrada
- `notes` - quaisquer notas registradas;
- `biocollection` - o nome da Coleção Biológica (Biocollections, etc) à qual a pessoa está associada


***
## Projects EndPoint

> O endpoint `projects` interage com a tabela [projects](/docs /concepts /data-access /#projects). O uso básico é obter os projetos registrados.

### Parâmetros GET

- `id = list` retorna apenas projetos com o id ou ids fornecidos (ex` id = 1,2,3,10`)

### Campos de resposta
  - `id` - o id do projeto na tabela de projetos (um id de banco de dados local)
  - `fullname` - nome do projeto
  - `contactEmail` - o e-mail do administrador do projeto
  - `individual_count` - o número de indivíduos no projeto
  - `vouchers_count` - o número de vouchers no projeto


***
## Taxons Endpoint

> O endpoint `taxons` interage com a tabela [taxons](/docs /concepts /core-objects/#taxons). O uso básico é obter uma lista de nomes taxonômicos registrados.

### Parâmetros GET

- `id = list` retorna apenas táxons com o id ou ids fornecidos (ex:` id = 1,2,3,10`)
- `name = search` retorna apenas táxons com fullname (sem autores) correspondendo à string de pesquisa. Você pode usar asterisco como curinga.
- `root = number` retorna o táxon para o id especificado junto com todos os seus descendentes
- `level = number` retorna apenas táxons para o [taxonRank](/docs/api/quick-reference/#taxon-levels).
- `valid = 1` retorna apenas nomes válidos
- `external = 1` retorna os números de referência Tropicos, IPNI, MycoBank, ZOOBANK ou GBIF. Você precisa especificar `externalrefs` na lista de campos para retorná-los!
- `project = mixed` - id ou nome do projeto (pode ser uma lista) retorna os táxons pertencentes a um ou mais Projetos
- `dataset = mixed` - id ou nome de um conjunto de dados (pode ser uma lista) retorna os táxons pertencentes a um ou mais conjuntos de dados
- `limit` e` offset`-  Consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).

Note que `id`, `name`  e `root` não devem ser combinadas na mesma busca;

### Campos de resposta
- `id` - este id ODB para este registro de táxons na tabela de táxons
- `senior_id` - se inválido, o id do nome aceito para este taxon (acceptedNameUsage) - somente quando taxonomicStatus == 'invalid'
- `parent_id` - o id do táxon pai
- `author_id` - o id da pessoa que definiu o táxon para **nomes não publicados** (ter um author_id significa que o táxon não foi publicado)
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName) - o nome taxonômico completo sem autores (ou seja, incluindo o nome do gênero e epíteto do nome da espécie)
- `ScientificNameID` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificNameID) - IDs de bancos de dados nomenclaturais, se qualquer referência externa for armazenada para este registro de táxon
- `taxonRank` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName) - o nível taxonômico do taxon registrado
- `level` - o valor numérico do taxonRank para o OpenDataBio
- `scientificNameAuthorship` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificNameAuthorship) - a autoria do táxon. Para **não publicado**  será um nome de pessoa registrado ODB
- `namePublishedIn` - referência bibliográfica unificada (ou seja, o formato curto ou um trecho da referência do bibtex atribuído). Isso será recuperado principalmente de bancos de dados nomenclaturais; As referências vinculadas a táxons podem ser extraídas com o [endpoint BibReferences](/docs/api/get-data/#bibreferences-endpoint).
- `taxonomicStatus` [DWC](https://dwc.tdwg.org/terms/#dwc:taxonomicStatus) - um de 'accepted', 'invalid' ou 'unpublished'; se invalid, os campos senior_id and acceptedNameUsage serão preenchidos.
- `parentNameUsage` [DWC] (https://dwc.tdwg.org/terms/#dwc:parentNameUsage) - o nome do táxon pai, se espécie, gênero, se gênero, família e assim por diante
- `family` [DWC] (https://dwc.tdwg.org/terms/#dwc:family) - o nome da família taxonômica quando for o caso.
- `higherClassification` [DWC] (https://dwc.tdwg.org/terms/#dwc:higherClassification) - a classificação hierárquica taxonômica completa, separada por "|" (incluirá apenas táxons registrados neste banco de dados)
- `acceptedNameUsage` [DWC] (https://dwc.tdwg.org/terms/#dwc:acceptedNameUsage) - se taxonomicStatus for inválido, o nome científico válido para este táxon
- `acceptNameUsageID` [DWC] (https://dwc.tdwg.org/terms/#dwc:acceptedNameUsageID) - se taxonomicStatus for inválido os ids ScientificNameID do táxon válido
- `taxonRemarks` [DWC] (https://dwc.tdwg.org/terms/#dwc:taxonRemarks) - qualquer nota que o registro do táxon possa ter
- `basisOfRecord` [DWC] (https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - será sempre 'taxon'
- `externalrefs` - os números de referência Tropicos, IPNI, MycoBank, ZOOBANK ou GBIF

***
## Traits Endpoint

> O endpoint `traits` interage com o modelo [Variáveis](/docs/trait-objects/#traits). O uso básico é obter uma lista de variáveis ​​e categorias de variáveis ​​para importar [Medições](/docs/trait-objects/#measurements).

### Parâmetros GET


- `id = lista` retorna apenas variáveis tendo o id ou ids fornecidos (ex` id = 1,2,3,10`);
- `name = string` retorna apenas variáveis com o` export_name` indicado (ex: `name = DBH`) - para mais opções de busca, baixe toda a biblioteca e filtre localmente.
- `categories` - se verdadeiro, retorna as categorias para variáveis categóricas
- `language = mixed` idioma para retorno dos nomes e descrições da variável e das categorias para variáveis categóricas. Os valores podem ser 'language_id', 'language_code' ou 'language_name';
- `bibreference = boolean` - se verdadeiro, inclui a [BibReference](/docs/concepts/auxiliary-objects/# bibreference) associado à definição da variável, se houver.
- `limit` e` offset`-  Consulte [Parâmetros comuns](/docs/api/quick-reference/#shared-get-parameters).

### Campos de resposta
- `id` - o id do Trait na tabela odbtraits (um id de banco de dados local)
- `type` - o código numérico que define o tipo de característica
- `typename` - o nome do tipo de Trait
- `export_name` - o valor do nome de exportação
- `measurementType` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - o mesmo que export_name para compatibilidade DWC
- `measurementMethod` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - combine nome, descrição e categorias se aplicável (incluído na API Measurement GET, para compatibilidade DWC)
- `measurementUnit` - a unidade de medida para características quantitativas
- `range_min` - o valor mínimo permitido para características quantitativas
- `range_max` - o valor máximo permitido para características quantitativas
- `link_type` - se o tipo de link trait, a classe do objeto ao qual o traço se vincula (atualmente apenas Taxon)
- `name` - o nome do trait no idioma solicitado ou no idioma padrão
- `description` - a descrição do traço no idioma solicitado ou no idioma padrão
- `value_length` - o comprimento dos valores permitidos para variáveis Espectrais (juntamente com range_min e range_max, define o espaçamento entre os valores que fazem o espectro medido.
- `objects` - os tipos de objeto para os quais o traço pode ser usado, separados por barra vertical '|'
- `categories` - para variáveis categóricas e ordinais, um sub conjunto com os seguintes campos para cada categoria: `id` , `name`,` description` e `rank`. O rank é informativo apenas para variáveis ORDINAIS, mas é reportado para todas as variáveis categóricas.
- `bibreference` - o registro BibReference associado à definição da variável

***
## Vouchers Endpoint

> O endpoint `vouchers` interage com o modelo [Vouchers](/docs/concepts/core-objects/#vouchers). Seu uso básico é obter dados de espécimes de [Coleções Biológicas](/docs/concepts/auxiliary-objects/#biocollections)

### Parâmetros GET
* `id = list` retorna apenas vouchers com o id ou ids fornecidos (ex` id = 1,2,3,10`)
* `numero = string` retorna apenas vouchers para o **número do coletor** informado (mas é uma string e pode conter caracteres não numéricos)
* `collector = mixed` um dos id ou ids ou abreviações, retorna apenas vouchers para o **coletor principal** informado
* `dataset = list` - lista de id ou ids, ou nomes do conjuntos de dados, retorna todos os vouchers relacionados aos conjuntos de dados informados, incluindo vouchers associados indiretamente (por exemplo, vouchers de medições ou de arquivos de mídia nos conjuntos de dados informados)
* `project = mixed` um de ids ou nomes, retorna apenas os vouchers para o projeto informado.
* `location = mixed` um de ids ou nomes; filtra vouchers diretamente vinculados à(s) localidades(s)
* `location_root = mixed` - igual a location, mas inclui também os vouchers para as localides descendentes das localidades informadas. por exemplo. *"location_root = Manaus"* para obter qualquer voucher coletado dentro da área administrativa de Manaus, enquanto *"location=Manaus"* retorna apenas indivíduos cuja localidade é Manaus, mas indivíduos ligados a outras localidades dentro do município..;
* `individual=mixed`  id ou lista de ids, ou organismID(s) de indivíduos, retorna apenas vouchers para os indivíduos informados
* `taxon = mixed` um de ids ou nomes, retorna apenas vouchers para os táxons informados.
* `taxon_root = mixed` - igual a taxon, mas incluirá vouchers para os descendentes dos táxons informados. por exemplo. *"taxon_root = Lauraceae"* para obter qualquer voucher de Lauraceae e *"taxon=Lauraceae"* para obter vouchers identificados no nível de família.


{{< alert color="primary" title="Note">}}
Observe que todos os campos de pesquisa (táxon, localidades e conjunto de dados) podem ser especificados como nomes (por exemplo, "taxon = Euterpe edulis") ou como ids de banco de dados. Se uma lista for especificada para um desses campos, todos os itens da lista devem ser do mesmo tipo, ou seja, você não pode pesquisar por 'taxon = Euterpe, 24'. Além disso, location e taxon têm prioridade sobre location_root e taxon_root se ambos forem informados.
{{< /alert >}}



#### Campos de resposta

- `id` - o id do voucher na table vouchers (id local da instalação)
- `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - será sempre 'preservedSpecimen' [dwc location]([DWC](https://dwc.tdwg.org/terms/#preservedspecimen)
- `occurrenceID` [DWC](https://dwc.tdwg.org/terms/#dwc:occurrenceID) - o identificador único do voucher que combina collectionCode+catalogNumber+organismID
- `organismID` [DWC](https://dwc.tdwg.org/terms/#dwc:organismID) -o identificador textual do Indivíduo ao qual o Voucher pertence
- `individual_id` - o id do Indivíduo  na tabela individuals (id local)
- `collectionCode` [DWC](https://dwc.tdwg.org/terms/#dwc:collectionCode) - o acrônimo da Coleção Biológica onde o Voucher está depositado
- `catalogNumber` [DWC](https://dwc.tdwg.org/terms/#dwc:catalogNumber) - o número de tombo (id) do Voucher na Coleção Biológica
- `typeStatus` [DWC](https://dwc.tdwg.org/terms/#dwc:typeStatus) - se o Voucher representa um tipo nomenclatural
- `recordedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedBy) - abbreviações de coletores separados por  pipe "|"
- `recordedByMain` - a primeira pessoa listada em recordedBy, o coletor ao qual pertence o recordNumber
- `recordNumber` [DWC](https://dwc.tdwg.org/terms/#dwc:recordNumber) - o **número do coletor** ou identificador do Voucher.
- `recordedDate` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedDate) - a data de coleta (pode estar incompleta)
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName)  - o nome científico do Indivíduo ao qual o voucher pertence.
- `scientificNameAuthorship` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificNameAuthorship) - o autor do nome taxonomico. Para **taxonomicStatus unpublished** será o nome da Pessoa que criou o nome não publicado.
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family)
- `genus` [DWC](https://dwc.tdwg.org/terms/#dwc:genus)
- `identificationQualifier` [DWC](https://dwc.tdwg.org/terms/#dwc:identificationQualifier) - modificadores do nome para a identificação atribuída: cf. aff. s.l., etc.
- `identifiedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:identifiedBy) - Pessoa que identificou
- `dateIdentified` [DWC](https://dwc.tdwg.org/terms/#dwc:dateIdentified) - data da identificação
- `identificationRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:identificationRemarks) - notas de identificacao
- `locationName` - nome da localidade em que o indivíduo estava quando foi coletado (quando o indivíduo tem mais de uma localidade, a localidade da data mais próxima)
- `higherGeography` [DWC](https://dwc.tdwg.org/terms/#dwc:higherGeography) - o caminho completo até LocationName '|' separated (e.g. Brasil | Amazonas | Rio Preto da Eva | Fazenda Esteio | Reserva km37);
- `decimalLatitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLatitude) - depende do tipo de localidade e dos atributos de posição relativa do Indivíduo ao qual o Voucher pertence. Se o indivíduo tiver vários locais (uma ave monitorado), o local mais próximo da data em que o voucher foi obtido
- `decimalLongitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLongitude) - mesmo que para decimalLatitude
- `occurrenceRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:occurrenceRemarks) - notas para este registro
- `associatedMedia` [DWC](https://dwc.tdwg.org/terms/#dwc:associatedMedia) - urls para arquivos de mídia associados a este registro
- `datasetName` - nome do Conjunto de Dados ao qual o indivíduo pertence [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - a política de acesso do Conjunto de dados - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - a citação para Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - a licensa para o Conjunto de Dados - [DWC](https://dwc.tdwg.org/terms/#dwc:license)

***
## Jobs Endpoint

> O endpoint `jobs` interage com o modelo [UserJobs] (/docs/concepts/data-access/#user-jobs). O uso básico é obter uma lista de trabalhos de importação, junto com uma mensagem de status e logs.

### Parâmetros GET
- `id` - id do job (id local)
- `status = string` retorna apenas jobs para o status especificado: "Submitted", "Processing", "Success", "Failed" ou "Cancelled";

#### Campos de resposta
- `id` - id do job (id local)
- `status` - o status do trabalho:" Enviado "," Processando "," Sucesso "," Falha "ou" Cancelado ";
- `dispatcher` - o tipo de trabalho, por exemplo, ImportTaxons;
- `log` - as mensagens de log do job, geralmente indicando se os recursos foram importados com sucesso ou se ocorreram erros;
outros.


***
## Possíveis erros

> Esta é uma pequena lista de códigos de erro que você pode receber ao usar a API. Se você receber qualquer outro código de erro, envie um [issue](https://github.com/opendatabio/opendatabio/issues) para o repositório.

- Error 401 - Unauthenticated. Currently not implemented. You may receive this error if you attempt to access some protected resources but didn't provide an API token.
- Error 403 - Unauthorized. You may receive this error if you attempt to import or edit some protected resources, and either didn't provide an API token or your user does not have enough privileges.
- Error 404 - The resource you attempted to see is not available. Note that you *can* receive this code if your user is not allowed to see a given resource.
- Error 413 - Request Entity Too Large. You may be attempting to send a very large import, in which case you might want to break it down in smaller pieces.
- Error 429 - Too many attempts. Wait one minute and try again.
- Error 500 - Internal server error. This indicates a problem with the server code. Please file a [bug report](https://github.com/opendatabio/opendatabio/issues) with details of your request.
