---
title: "GET data"
linkTitle: "GET data"
weight: 2
description: >
  How to GET data with the OpenDataBio API!
---


{{< alert color="warning">}}
- The [OpenDataBio R package](https://github.com/opendatabio/opendatabio-r) is a  **client** for this API.
- No authentication required to access data with a public access policy
- Authentication token required only to get data with a non-public access policy
{{< /alert >}}

## BibReferences Endpoint

> The `bibreferences` endpoint interact with the [bibreference](/docs/concepts/auxiliary-objects/#bibreferences) table. Their basic usage is getting the registered Bibliographic References.

### GET request-parameters
- `id=list` return only references having the id or ids provided (ex `id=1,2,3,10`)
- `bibkey=list` return only references having the bibkey or bibkeys (ex `bibkey=ducke1953,mayr1992`)
- `taxon=list of ids` return only references linked to the taxon informed.
- `limit` and `offset` limit query. see [Common parameters](/docs/api/quick-reference/#shared-get-parameters).

### Response fields

  - `id`- the id of the BibReference in the bibreferences table (a local database id)
  - `bibkey` - the bibkey used to search and use of the reference in the web system
  - `year` - the publication year
  - `author` - the publication authors
  - `title` - the publication title
  - `doi` - the publication DOI if present
  - `url` - an external url for the publication if present
  - `bibtex` - the reference citation record in [BibTex](http://www.bibtex.org/) format


***

## Datasets Endpoint

> The `datasets` endpoint interact with the [Datasets](/docs/concepts/data-access/#datasets) table. Their basic usage is getting the registered Datasets, but not the data in the datasets (use the web interface for getting the complete data for a dataset or retrive the data using the dataset object specific apis. Usefull only for getting dataset_ids for importing Measurements dynamically.

### GET request-parameters
- `id=list` return only datasets having the id or ids provided (ex `id=1,2,3,10`)

### Response fields
  - `id` - the id of the Dataset in the datasets table (a local database id)
  - `name` - the name of the dataset
  - `privacyLevel` - the access level for the dataset
  - `contactEmail` - the dataset administrator email
  - `description` - a description of the dataset
  - `policy` - the data policy if specified
  - `measurements_count` - the number of measurements in the dataset
  - `taggedWidth` - the list of tags applied to the dataset


***
## Biocollections Endpoint

> The `biocollections` endpoint interact with the [biocollections](/docs/concepts/data-access/#biocollections) table. Their basic usage is getting the list of the Biological Collections registered in the database. Using for getting `biocollection_id` or validating your codes for importing data with the [Vouchers](#vouchers-endpoint) or [Individuals](#individuals-endpoint) endpoints.

### GET request-parameters
- `id=list` return only 'biocollections' having the id or ids provided (ex `id=1,2,3,10`)
- `acronym` return only 'biocollections' having the acronym or acronym provided (ex `acronym=INPA,SP,NY`)

### Response fields
  - `id` - the id of the repository or museum in the biocollections table (a local database id)
  - `name` - the name of the repository or museum
  - `acronym` - the repository or museum acronym
  - `irn` - only for Herbaria, the number of the herbarium in the [Index Herbariorum](http://sweetgum.nybg.org/science/ih/)

***
## Individuals Endpoint

> The `individuals` endpoints interact with the [Individual](/docs/concepts/core-objects/#individuals) table. Their basic usage is getting a list of individuals.

### GET request-parameters
- `id=number or list` - return individuals that have id or ids ex: `id=2345,345`
- `location=mixed` - return by location id or name or ids or names ex: `location=24,25,26` `location=Parcela 25ha` of the locations where the individuals
- `location_root` - same as location but return  also from the descendants of the locations informed
- `taxon=mixed` - the id or ids, or canonicalName taxon names (fullnames) ex: `taxon=Aniba,Ocotea guianensis,Licaria cannela tenuicarpa` or `taxon=456,789,3,4`
- `taxon_root` - same as taxon but return also all the individuals identified as any of the descendants of the taxons informed
- `project=mixed` - the id or ids or names of the project, ex: `project=3` or `project=OpenDataBio`
- `tag=list` - one or more individual tag number\/code, ex: `tag=individuala1,2345,2345A`
- `dataset=mixed` - the id or ids or names of the datasets, return individuals having measurements in the datasets informed
- `limit` and `offset` are SQL statements to limit the amount of data when trying to download a large number of individuals, as the request may fail due to memory constraints. See [Common parameters](/docs/api/quick-reference/#shared-get-parameters).

{{< alert color="primary" title="Note">}}
Notice that all search fields (taxon, location and dataset) may be specified as names (eg, "taxon=Euterpe edulis") or as database ids. If a list is specified for one of these fields, all items of the list must be of the same type, i.e. you cannot search for 'taxon=Euterpe,24'. Also, location and taxon have priority over location_root and taxon_root if both informed.
{{< /alert >}}

### Response fields

  - `id` - the ODB id of the Individual in the individuals table (a local database id)
  - `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - will be always 'organism' [dwc organism]([DWC](https://dwc.tdwg.org/terms/#organism)
  - `organismID` [DWC](https://dwc.tdwg.org/terms/#dwc:organismID) - a local unique combination of record info, composed of recordNumber,recordedByMain,locationName
  - `recordedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedBy) - pipe "|" separated list of registered Persons abbreviations
  - `recordedByMain` - the first person in recordedBy, the main collectors
  - `recordNumber` [DWC](https://dwc.tdwg.org/terms/#dwc:recordNumber) - an identifier for the individual, may be the code in a tree aluminum tag, a bird band code, a collector number
  - `recordedDate` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedDate) - the record date
  - `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName)  - the current taxonomic identification of the individual (no authors) or "unidentified"
  - `scientificNameAuthorship` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificNameAuthorship) - the taxon authorship. For **taxonomicStatus unpublished**: will be a ODB registered Person name
  - `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family)
  - `genus` [DWC](https://dwc.tdwg.org/terms/#dwc:genus)
  - `identificationQualifier` [DWC](https://dwc.tdwg.org/terms/#dwc:identificationQualifier) - identification name modifiers cf. aff. s.l., etc.
  - `identifiedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:identifiedBy) - the Person identifying the scientificName  of this record
  - `dateIdentified` [DWC](https://dwc.tdwg.org/terms/#dwc:dateIdentified) - when the identification was made (may be incomplete, with 00 in the month and or day position)
  - `identificationRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:identificationRemarks) - any notes associated with the identification
  - `locationName` - the location name (if plot the plot name, if point the point name, ...)
  - `locationParentName` - the immediate parent locationName, to facilitate use when location is subplot
  - `higherGeography` [DWC](https://dwc.tdwg.org/terms/#dwc:higherGeography) - the parent LocationName '|' separated (e.g. Brasil | Amazonas | Rio Preto da Eva | Fazenda Esteio | Reserva km37 | Manaus ForestGeo-PDBFF Plot | Quadrat 100x100 );
  - `decimalLatitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLatitude) - depends on the location adm_level and the individual X and Y, or Angle and Distance attributes, which are used to calculate these global coordinates for the record; if individual has multiple locations (a monitored bird), the last location is obtained with this get API
  - `decimalLongitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLongitude) - same as for decimalLatitude
  - `x` - the individual X position in a Plot location
  - `y` - the individual Y position in a Plot location
  - `gx` - the individual global X position in a Parent Plot location, when location is subplot (ForestGeo standards)
  - `gy` - the individual global Y position in a Parent Plot location, when location is subplot (ForestGeo standards)
  - `angle` - the individual azimuth direction in relation to a POINT reference, either when adm_level is POINT or when X and Y are also provided for a Plot location this is calculated from X and Y positions  
  - `distance` - the individual distance direction in relation to a POINT reference, either when adm_level is POINT or when X and Y are also provided for a Plot location this is calculated from X and Y positions  
  - `organismRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:organismRemarks) - any note associated with the Individual record
  - `associatedMedia` [DWC](https://dwc.tdwg.org/terms/#dwc:associatedMedia) - urls to ODB media files associated with the record
  - `datasetName` - the name of the ODB Dataset to which the record belongs to [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
  - `accessRights` - the ODB Dataset access privacy setting - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
  - `bibliographicCitation` - the ODB Dataset citation - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
  - `license` - the ODB Dataset license - [DWC](https://dwc.tdwg.org/terms/#dwc:license)

***

## Individual-locations Endpoint

> The `individual-locations` endpoint interact with the [individual_location](/docs/concepts/core-objects/#individuals) table. Their basic usage is getting location data for individuals, i.e. occurrence data for organisms. Designed for occurrences of organisms that move and have multiple locations, else the same info is retrieved with the [Individuals endpoint](#individuals-endpoint).

### GET request-parameters
- `individual_id=number or list` - return locations for individuals that have id or ids ex: `id=2345,345`
- `location=mixed` - return by location id or name or ids or names ex: `location=24,25,26` `location=Parcela 25ha` of the locations where the individuals
- `location_root` - same as location but return  also from the descendants of the locations informed
- `taxon=mixed` - the id or ids, or canonicalName taxon names (fullnames) ex: `taxon=Aniba,Ocotea guianensis,Licaria cannela tenuicarpa` or `taxon=456,789,3,4`
- `taxon_root` - same as taxon but return also all the the locations for individuals identified as any of the descendants of the taxons informed
- `dataset=mixed` - the id or ids or names of the datasets, return individuals belonging to the datasets informed
- `limit` and `offset` are SQL statements to limit the amount of data when trying to download a large number of individuals, as the request may fail due to memory constraints. See [Common parameters](/docs/api/quick-reference/#shared-get-parameters).

{{< alert color="success" title="Note">}}
Notice that all search fields (taxon, location and dataset) may be specified as names (eg, "taxon=Euterpe edulis") or as database ids. If a list is specified for one of these fields, all items of the list must be of the same type, i.e. you cannot search for 'taxon=Euterpe,24'. Also, location and taxon have priority over location_root and taxon_root if both informed.
{{< /alert >}}


### Response fields

- `individual_id` - the ODB id of the Individual in the individuals table (a local database id)
- `location_id` - the ODB id of the Location in the locations table (a local database id)
- `basisOfRecord` - will be always 'occurrence' - [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) and [dwc occurrence]([DWC](https://dwc.tdwg.org/terms/#occurrence);
- `occurrenceID` - the unique identifier for this record, the individual+location+date_time  - [DWC](https://dwc.tdwg.org/terms/#dwc:occurrenceID)  
- `organismID` - the unique identifier for the Individual [DWC](https://dwc.tdwg.org/terms/#dwc:organismID)
- `recordedDate` - the occurrence date+time observation - [DWC](https://dwc.tdwg.org/terms/#dwc:recordedDate)
- `locationName` - the location name (if plot the plot name, if point the point name, ...)
- `higherGeography` - the parent LocationName '|' separated (e.g. Brasil | Amazonas | Rio Preto da Eva | Fazenda Esteio | Reserva km37 | Manaus ForestGeo-PDBFF Plot | Quadrat 100x100 ) - [DWC](https://dwc.tdwg.org/terms/#dwc:higherGeography)
- `decimalLatitude` - depends on the location adm_level and the individual X and Y, or Angle and Distance attributes, which are used to calculate these global coordinates for the record - [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLatitude)
- `decimalLongitude` - same as for decimalLatitude - [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLongitude)
- `georeferenceRemarks` - will contain the explanation of the type of decimalLatitude - [DWC](https://dwc.tdwg.org/terms/#dwc:georeferenceRemarks)
- `x` - the individual X position in a Plot location
- `y` - the individual Y position in a Plot location
- `angle` - the individual azimuth direction in relation to a POINT reference, either when adm_level is POINT or when X and Y are also provided for a Plot location this is calculated from X and Y positions  
- `distance` - the individual distance direction in relation to a POINT reference, either when adm_level is POINT or when X and Y are also provided for a Plot location this is calculated from X and Y positions  
- `minimumElevation` - the altitude for this occurrence record if any - [DWC](https://dwc.tdwg.org/terms/#dwc:minimumElevation)
- `occurrenceRemarks` - any note associated with this record - [DWC](https://dwc.tdwg.org/terms/#dwc:occurrenceRemarks)
- `scientificName` - the current taxonomic identification of the individual (no authors) or "unidentified" - [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName)  
- `family` - the current taxonomic family name, if apply - [DWC](https://dwc.tdwg.org/terms/#dwc:family)
- `datasetName` - the name of the ODB Dataset to which the record belongs to - [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - the ODB Dataset access privacy setting - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - the ODB Dataset citation - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - the ODB Dataset license [DWC](https://dwc.tdwg.org/terms/#dwc:license)

***

## Measurements Endpoint

> The `measurements` endpoint interact with the [measurements](/docs/concepts/auxiliary-objects/#measurements) table. Their basic usage is getting Data linked to Individuals, Taxons, Locations or Vouchers, regardless of datasets, so it is useful when you want particular measurements from different datasets that you have access for. If you want a full dataset, you may just use the web interface, as it prepares a complete set of the dataset measurements and their associated data tables for you.

### GET request-parameters
- `id=list of ids` return only the measurement or measurements having the id or ids provided (ex `id=1,2,3,10`)
- `taxon=list of ids or names` return only the measurements related to the Taxons, both direct taxon measurements and indirect taxon measurements from their individuals and vouchers (ex `taxon=Aniba,Licaria`). Does not consider descendants taxons for this use `taxon_root` instead. In the example only measurements directly linked to the genus and genus level identified vouchers and individuals will be retrieved.
- `taxon_root=list of ids or names` similar to `taxon`, but get also measurements for descendants taxons of the informed query (ex `taxon=Lauraceae` will get measurements linked to Lauraceae and any taxon that belongs to it;
- `dataset=list of ids` return only the  measurements belonging to the datasets informed (ex `dataset=1,2`) - allows to get all data from a dataset.
- `trait=list of ids or export_names` return only the  measurements for the traits informed (ex `trait=DBH,DBHpom` or `dataset=2?trait=DBH`) - allows to get data for a particular trait
- `individual=list of individual ids` return only the  measurements for the individual ids informed (ex `individual=1000,1200`)
- `voucher=list of voucher ids` return only the  measurements for the voucher ids informed (ex `voucher=345,321`)
- `location=list of location ids` return only measurements for the locations ids informed (ex `location=4,321`)- does not retrieve measurements for individuals and vouchers in those locations, only measured locations, like plot soil surveys data.
- `limit` and `offset` are SQL statements to limit the amount of data when trying to download a large number of measurements, as the request may fail due to memory constraints. See [Common parameters](/docs/api/quick-reference/#shared-get-parameters).

### Response fields

- `id` - the Measurement ODB id in the measurements table (local database id)
- `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - will be always 'MeasurementsOrFact' [dwc measurementorfact]([DWC](https://dwc.tdwg.org/terms/#measurementorfact)
- `measured_type` - the measured object, one of 'Individual', 'Location', 'Taxon' or 'Voucher'
- `measured_id` - the id of the measured object in the respective object table (individuals.id, locations.id, taxons.id, vouchers.id)
- `measurementID` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) -  a unique identifier for the Measurement record - combine measured resourceRelationshipID, measurementType and date
- `measurementType` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - the export_name for the ODBTrait measured
- `measurementValue` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - the value for the measurement - will depend on kind of the measurementType (i.e. ODBTrait)
- `measurementUnit` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - the unit of measurement for quantitative traits
- `measurementDeterminedDate` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - the Measurement measured date
- `measurementDeterminedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:measurementDeterminedBy) - Person responsible for the measurement
- `measurementRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:measurementRemarks) - text note associated with this Measurement record
- `resourceRelationship` [DWC](https://dwc.tdwg.org/terms/#dwc:resourceRelationship) - the measured object (resource) - one of 'location','taxon','organism','preservedSpecimen'
- `resourceRelationshipID` [DWC](https://dwc.tdwg.org/terms/#dwc:resourceRelationshipID) - the id of the resourceRelationship
- `relationshipOfResource` [DWC](https://dwc.tdwg.org/terms/#dwc:relationshipOfResource) - will always be 'measurement of'
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName)  - the current taxonomic identification (no authors) or 'unidentified' if the resourceRelationship object is not 'location'
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family) - taxonomic family name if applies
- `datasetName` - the name of the ODB Dataset to which the record belongs to - [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - the ODB Dataset access privacy setting - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - the ODB Dataset citation - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - the ODB Dataset license - [DWC](https://dwc.tdwg.org/terms/#dwc:license)


***
## Media Endpoint

> The `media` endpoint interact with the [media](/docs/concepts/auxiliary-objects/#mediafiles) table. Their basic usage is getting the metadata associated with MediaFiles and the files URL.

### GET request-parameters
- `individual=number or list` - return media associated with the individuals having id or ids ex: `id=2345,345`
- `voucher=number or list` - return media associated with the vouchers having id or ids ex: `id=2345,345`
- `location=mixed` - return media associated with the locations having id or name or ids or names ex: `location=24,25,26` `location=Parcela 25ha`
- `location_root` - same as location but return also media associated with the descendants of the locations informed
- `taxon=mixed` - the id or ids, or canonicalName taxon names (fullnames) ex: `taxon=Aniba,Ocotea guianensis,Licaria cannela tenuicarpa` or `taxon=456,789,3,4`
- `taxon_root` - same as taxon but return also all the locations for media related to any of the descendants of the taxons informed
- `dataset=mixed` - the id or ids or names of the datasets, return belonging to the datasets informed
- `limit` and `offset` are SQL statements to limit the amount of data when trying to download a large number of individuals, as the request may fail due to memory constraints. See [Common parameters](/docs/api/quick-reference/#shared-get-parameters).

{{< alert color="success" title="Note">}}
Notice that all search fields (taxon, location and dataset) may be specified as names (eg, "taxon=Euterpe edulis") or as database ids. If a list is specified for one of these fields, all items of the list must be of the same type, i.e. you cannot search for 'taxon=Euterpe,24'. Also, location and taxon have priority over location_root and taxon_root if both informed.
{{< /alert >}}

### Response fields

- `id` - the Measurement ODB id in the measurements table (local database id)
- `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - will be always 'MachineObservation' [DWC](https://dwc.tdwg.org/terms/#machineobservation)
- `model_type` - the related object, one of 'Individual', 'Location', 'Taxon' or 'Voucher'
- `model_id` - the id of the related object in the respective object table (individuals.id, locations.id, taxons.id, vouchers.id)
- `resourceRelationship` [DWC](https://dwc.tdwg.org/terms/#dwc:resourceRelationship) - the related object (resource) - one of 'location','taxon','organism','preservedSpecimen'
- `resourceRelationshipID` [DWC](https://dwc.tdwg.org/terms/#dwc:resourceRelationshipID) - the id of the resourceRelationship
- `relationshipOfResource` [DWC](https://dwc.tdwg.org/terms/#dwc:relationshipOfResource) - will be the dwcType
- `recordedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedBy) - pipe "|" separated list of registered Persons abbreviations
- `recordedDate` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedDate) - the media file date
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName)  - the current taxonomic identification of the individual (no authors) or "unidentified"
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family)
- `dwcType` [DWC](https://dwc.tdwg.org/terms/#type) - one of StillImage, MovingImage, Sound
- `datasetName` - the name of the ODB Dataset to which the record belongs to [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - the ODB Dataset access privacy setting - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - the ODB Dataset citation - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - the ODB Dataset license - [DWC](https://dwc.tdwg.org/terms/#dwc:license)
- `file_name` - the file name
- `file_url` - the url to the file


***
## Languages EndPoint

> The `languages` endpoint interact with the [Language](/docs/concepts/auxiliary-objects/#user_translations) table. Their basic usage is getting a list of registered Languages to import [User Translations](/docs/concepts/auxiliary-objects/#user_translations) like [Trait](#endpoint_traits) and TraitCategories names and descriptions.

### Response fields
  - `id` - the id of the language in the languages table (a local database id)
  - `code` - the language string code;
  - `name` - the language name;


***
## Locations Endpoint

> The `locations` endpoints interact with the [locations](/docs/concepts/core-objects/#locations) table. Their basic usage is getting a list of registered countries, cities, plots, etc, or importing new locations.


### GET request-parameters
- `id=list` return only locations having the id or ids provided (ex `id=1,2,3,10`)
- `adm_level=number` return only locations for the specified level or type:
  - **2** for country; **3** for first division within country (province, state); **4** for second division (e.g. municipality)... up to adm_level 10 as administrative areas (Geometry: polygon, MultiPolygon);
  - **97** is the code for Environmental Layers (Geometry: polygon, multipolygon);
  - **98** is the code for Indigenous Areas (Geometry: polygon, multipolygon);
  - **99** is the code for Conservation Units (Geometry: polygon, multipolygon);
  - **100** is the code for plots and subplots (Geometry: polygon or point);
  - **101** for transects (Geometry: point or linestring)
  - **999** for any 'POINT' locations like GPS waypoints (Geometry: point);
- `name=string` return only locations whose name matches the search string. You may use asterisk as a wildcard. Example: `name=Manaus` or `name=*Ducke*` to find name that has the word Ducke;
- `parent_id=list` return the locations for which the direct parent is in the list (ex: parent_id=2,3)
- `root=number` number is the location `id` to search, returns the location for the specified id along with all of its descendants locations; example: find the id for Brazil and use its id as root to get all the locations belonging to Brazil;
- `querytype` one of "exact", "parent" or "closest" and must be provided with `lat` and `long`:
    - when `querytype=exact` will find a point location that has the exact match of the `lat` and `long`;
    - when `querytype=parent` will find the most inclusive parent location within which the coordinates given by `lat` and `long` fall;
    - when `querytype=closest` will find the closest location to the coordinates given by `lat` and `long`; It will only search for closest locations having `adm_level > 99`, see above.
    - `lat` and `long` must be valid coordinates in decimal degrees (negative for South and West);
- `fields=list` specify which fields you want to get with your query (see below for field names), or use options 'all' or 'simple', to get full set and the most important columns, respectively
- `project=mixed` - id or name of project (may be a list) return the locations belonging to one or more Projects
- `dataset=mixed` - id or name of a dataset (may be a list) return the locations belonging to one or more Datasets

Notice that `id`, `search`, `parent` and `root` should probably not be combined in the same query.


### Response fields


  - `id` - the ODB id of the Location in the locations table (a local database id)
  - `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - will always contain 'location' [dwc location]([DWC](https://dwc.tdwg.org/terms/#location)
  - `locationName` - the location name (if country the country name, if state the state name, etc...)
  - `adm_level` - the numeric value for the ODB administrative level (2 for countries, etc)
  - `levelName` - the name of the ODB administrative level
  - `parent_id` - the ODB id of the parent location
  - `parentName` - the immediate parent locationName
  - `higherGeography` [DWC](https://dwc.tdwg.org/terms/#dwc:higherGeography) - the parent LocationName '|' separated (e.g. Brasil | São Paulo | Cananéia);
  - `footprintWKT` [DWC](https://dwc.tdwg.org/terms/#dwc:footprintWKT) - the [WKT](https://en.wikipedia.org/wiki/Well-known_text) representation of the location; if adm_level==100 (plots) or adm_level==101 (transects) and they have been informed as a POINT location, the respective polygon or linestring geometries, the footprintWKT will be that generated using the location's x and y dimensions.
  - `x` and `y` - (meters) when location is a plot (100 == adm_level) its X and Y dimensions, if a transect (101 == adm_level), x may be the length and y may be a buffer dimension around the linestring.
  - `startx` and `starty` - (meters) when location is a subplot (100 == adm_level with parent also adm_level==100), the X and Y start position in relation to the 0,0 coordinate of the parent plot location, which is either a Point, or the first coordinate of a Polygon geometry type;
  - `distance` - only when querytype==closest, this value will be present, and indicates the distance, in meters, the locations is from your queried coordinates;
  - `locationRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:locationRemarks) - any notes associated with this Location record
  - `decimalLatitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLatitude) - depends on the adm_level: if adm_level<=99, the latitude of the centroid; if adm_level == 999 (point), its latitude; if adm_level==100 (plot) or 101 (transect), but is a POINT geometry, the POINT latitude, else if POLYGON geometry, then the first point of the POLYGON or the LINESTRING geometry.
  - `decimalLongitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLongitude) - same as for decimalLatitude
  - `georeferenceRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:georeferenceRemarks) - will contain the explanation about decimalLatitude
  - `geodeticDatum` -[DWC](https://dwc.tdwg.org/terms/#dwc:geodeticDatum)  the geodeticDatum informed for the geometry (ODB does not treat map projections, assumes data is always is WSG84)

***
## Persons Endpoint

> The `persons` endpoint interact*** with the [Person](/docs/concepts/auxiliary-objects/#persons) table. The basic usage is getting a list of registered people (individuals and vouchers collectors, taxonomic specialists or database users).

### GET request-parameters
- `id=list` return only persons having the id or ids provided (ex `id=1,2,3,10`)
- `name=string` - return people whose name matches the specified string. You may use asterisk as a wildcard. Ex: `name=*ducke*`
- `abbrev = string`, return people whose abbreviation matches the specified string. You may use asterisk as a wildcard.
- `email=string`, return people whose e-mail matches the specified string. You may use asterisk as a wildcard.
- `search=string`, return people whose name, abbreviation or e-mail matches the specified string. You may use asterisk as a wildcard.
- `limit` and `offset` are SQL statements to limit the amount of data when trying to download a large number of measurements, as the request may fail due to memory constraints. See [Common parameters](/docs/api/quick-reference/#shared-get-parameters).

### Response fields

- `id` - the id of the person in the persons table (a local database id)
- `full_name` - the person name;
- `abbreviation` - the person name (this are UNIQUE values in a OpenDataBio database)
- `email` - the email, if registered or person is user
- `institution` - the persons institution, if registered
- `notes` - any registered notes;
- `biocollection` - the name of the Biological Collection (Biocollections, etc) that the person is associated with; not included in simple)


***
## Projects EndPoint

> The `projects` endpoint interact with the [projects](/docs/concepts/data-access/#projects) table. The basic usage is getting the registered Projects.

### GET request-parameters
- `id=list` return only projects having the id or ids provided (ex `id=1,2,3,10`)

### Response fields

  - `id` - the id of the Project in the projects table (a local database id)
  - `fullname` - project name
  - `privacyLevel` - the access level for individuals and vouchers in Project
  - `contactEmail` - the project administrator email
  - `individuals_count` - the number of individuals in the project
  - `vouchers_count` - the number of vouchers in the project


***
## Taxons Endpoint

> The `taxons` endpoint interact with the [taxons](/docs/concepts/core-objects/#taxons)  table. The basic usage is getting a list of registered taxonomic names.

### GET request-parameters
- `id=list` return only taxons having the id or ids provided (ex `id=1,2,3,10`)
- `name=search` returns only taxons with fullname (no authors) matching the search string. You may use asterisk as a wildcard.
- `root=number` returns the taxon for the specified id along with all of its descendants
- `level=number` return only taxons for the specified [taxon level](/docs/api/quick-reference/#taxon-levels).
- `valid=1` return only valid names
- `external=1` return the Tropicos, IPNI, MycoBank, ZOOBANK or GBIF reference numbers. You need to specify `externalrefs` in the field list to return them!
- `project=mixed` - id or name of project (may be a list) return the taxons belonging to one or more Projects
- `dataset=mixed` - id or name of a dataset (may be a list) return the taxons belonging to one or more Datasets
- `limit` and `offset` are SQL statements to limit the amount. See [Common parameters](/docs/api/quick-reference/#shared-get-parameters).

Notice that `id`, `name` and `root` should not be combined.

### Response fields


- `id` - this ODB id for this Taxon record in the taxons table
- `senior_id` - if invalid this ODB identifier of the valid synonym for this taxon (acceptedNameUsage) - only when taxonomicStatus == 'invalid'
- `parent_id` - the id of the parent taxon
- `author_id` - the id of the person that defined the taxon for unpublished names (having an author_id means the taxon is unpublished)
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName) - the full taxonomic name without authors (i.e. including genus name and epithet for species name)
- `scientificNameID` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificNameID) - nomenclatural databases ids, if any external reference is stored for this Taxon record
- `taxonRank` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName)  - the string value of the taxon rank
- `level` - the ODB numeric value of the taxon rank
- `scientificNameAuthorship` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificNameAuthorship) - the taxon authorship. For **taxonomicStatus unpublished**: will be a ODB registered Person name
- `namePublishedIn` - unified bibliographic reference (i.e. either the short format or an extract of the bibtext reference assigned). This will be mostly retrieved from nomenclatural databases; Taxon linked references can be extracted with the [BibReference endpoint](/docs/api/get-data/#bibreferences-endpoint).
- `taxonomicStatus` [DWC](https://dwc.tdwg.org/terms/#dwc:taxonomicStatus) - one of  'accepted', 'invalid' or 'unpublished'; if invalid, fields senior_id and acceptedNameUsage* will be filled
- `parentNameUsage` [DWC](https://dwc.tdwg.org/terms/#dwc:parentNameUsage) - the name of the parent taxon, if species, the genus, if genus, family, and so on
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family) - the family name if taxonRank family or below
- `higherClassification` [DWC](https://dwc.tdwg.org/terms/#dwc:higherClassification) - the full taxonomic hierarchical classification, pipe separated (will include only Taxons registered in this database)
- `acceptedNameUsage` [DWC](https://dwc.tdwg.org/terms/#dwc:acceptedNameUsage) - if taxonomicStatus invalid the valid scientificName for this Taxon
- `acceptedNameUsageID` [DWC](https://dwc.tdwg.org/terms/#dwc:acceptedNameUsageID) - if taxonomicStatus invalid the scientificNameID ids of the valid Taxon
- `taxonRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:taxonRemarks) - any note the taxon record may have
- `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - will always be 'taxon'
- `externalrefs` - the Tropicos, IPNI, MycoBank, ZOOBANK or GBIF reference numbers


***
## Traits Endpoint

> The `traits` endpoint interact with the [Trait](Trait-Objects#traits) table. The basic usage is getting a list of variables and variables categories for importing [Measurements](Trait-Objects#measurements).

### GET request-parameters
- `id=list` return only traits having the id or ids provided (ex `id=1,2,3,10`);
- `name=string` return only traits having the `export_name` as indicated (ex `name=DBH`)
- `categories` - if true return the categories for categorical traits
- `language=mixed` return name and descriptions of both trait and categories in the specified language. Values may be 'language_id', 'language_code' or 'language_name';
- `bibreference=boolean` - if true, include the [BibReference](/docs/concepts/auxiliary-objects/#bibreference) associated with the trait in the results;
- `limit` and `offset` are SQL statements to limit the amount. See [Common parameters](/docs/api/quick-reference/#shared-get-parameters).

### Response fields
- `id` - the id of the Trait in the odbtraits table (a local database id)
- `type` - the numeric code defining the Trait type
- `typename` - the name of the Trait type
- `export_name` - the export name value
- `measurementType` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - same as export_name for DWC compatibility
- `measurementMethod` [DWC](https://dwc.tdwg.org/terms/#measurementorfact) - combine name, description and categories if apply (included in the Measurement GET API, for DWC compatibility)
- `measurementUnit` - the unit of measurement for Quantitative traits
- `range_min` - the minimum allowed value for Quantitative traits
- `range_max` - the maximum allowed value for Quantitative traits
- `link_type` - if Link type trait, the class of the object the trait links to (currently only Taxon)
- `name` - the trait name in the language requested or in the default language
- `description` - the trait description in the language requested or in the default language
- `value_length` - the length of values allowed for Spectral trait types
- `objects` - the types of object the trait may be used for, separated by pipe '|'
- `categories` - each category is given for Categorical and Ordinal traits, with the following fields (the category `id`, `name`, `description` and `rank`). Ranks are meaningfull only for ORDINAL traits, but reported for all categorical traits.
-  `bibreference` - the BibReference record associated with the Trait definition

***
## Vouchers Endpoint

> The `vouchers` endpoints interact with the [Voucher](/docs/concepts/core-objects/#vouchers) table. Their basic usage is getting data from Voucher specimens

### GET parameters
* `id=list` return only vouchers having the id or ids provided (ex `id=1,2,3,10`)
* `number=string` returns only vouchers for the informed collector number (but is a string and may contain non-numeric codes)
* `collector=mixed` one of id or ids or abbreviations, returns only vouchers for the informed **main collector**
* `dataset=list` - one of id or ids list, name or names list, return all vouchers directly or indirectly related to the datasets informed.
* `project=mixed` one of ids or names, returns only the vouchers for datasets belonging to the Project informed.
* `location=mixed` one of ids or names; (1) if `individual_tag` is also requested returns only vouchers for those individuals (or use *"individual=\*"* to get all vouchers for any individual collected at the location); (2) if `individual` and `individual_tag` are not informed, then returns vouchers linked to locations and to the individuals at the locations.
* `location_root=mixed` - same as location, but include also the vouchers for the descendants of the locations informed. e.g. *"location_root=Manaus"* to get any voucher collected within the Manaus administrative area;
* `individual=mixed` either a individual_id or a list of ids, or `*` - returns only vouchers for the informed individuals; when *"individual=\*"* then location must be informed, see above;
* `taxon=mixed` one of ids or names, returns only vouchers for the informed taxons. This could be either vouchers referred as parent of the requested taxon or vouchers of individuals of the requested taxons.
* `taxon_root=mixed` - same as taxon, but will include in the return also the vouchers for the descendants of the taxons informed. e.g. *"taxon_root=Lauraceae"* to get any Lauraceae voucher;

Notice that some search fields (taxon, location, project and collector) may be specified as names - abbreviation, fullnames and emails in the case of collector - (eg, "taxon=Euterpe edulis") or as database ids. If a list is specified for one of these fields, **all items of the list must be of the same type**.


### Response fields


- `id` - the Voucher ODB id in the vouchers table (local database id)
- `basisOfRecord` [DWC](https://dwc.tdwg.org/terms/#dwc:basisOfRecord) - will be always 'preservedSpecimen' [dwc location]([DWC](https://dwc.tdwg.org/terms/#preservedspecimen)
- `occurrenceID` [DWC](https://dwc.tdwg.org/terms/#dwc:occurrenceID) - a unique identifier for the Voucher record - combine organismID with biocollection info
- `organismID` [DWC](https://dwc.tdwg.org/terms/#dwc:organismID) - a unique identifier for the Individual the Voucher belongs to
- `individual_id` - the ODB id for the Individual the Voucher belongs to
- `collectionCode` [DWC](https://dwc.tdwg.org/terms/#dwc:collectionCode) - the Biocollection acronym where the Voucher is deposited
- `catalogNumber` [DWC](https://dwc.tdwg.org/terms/#dwc:catalogNumber) - the Biocollection number or code for the Voucher
- `typeStatus` [DWC](https://dwc.tdwg.org/terms/#dwc:typeStatus) - if the Voucher represent a nomenclatural type
- `recordedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedBy) - collectors pipe "|" separated list of registered Persons abbreviations that collected the vouchers
- `recordedByMain` - the first person in recordedBy, the main collector
- `recordNumber` [DWC](https://dwc.tdwg.org/terms/#dwc:recordNumber) - an identifier for the Voucher, generaly the Collector Number value
- `recordedDate` [DWC](https://dwc.tdwg.org/terms/#dwc:recordedDate) - the record date, collection date
- `scientificName` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificName)  - the current taxonomic identification of the individual (no authors) or "unidentified"
- `scientificNameAuthorship` [DWC](https://dwc.tdwg.org/terms/#dwc:scientificNameAuthorship) - the taxon authorship. For **taxonomicStatus unpublished**: will be a ODB registered Person name
- `family` [DWC](https://dwc.tdwg.org/terms/#dwc:family)
- `genus` [DWC](https://dwc.tdwg.org/terms/#dwc:genus)
- `identificationQualifier` [DWC](https://dwc.tdwg.org/terms/#dwc:identificationQualifier) - identification name modifiers cf. aff. s.l., etc.
- `identifiedBy` [DWC](https://dwc.tdwg.org/terms/#dwc:identifiedBy) - the Person identifying the scientificName  of this record
- `dateIdentified` [DWC](https://dwc.tdwg.org/terms/#dwc:dateIdentified) - when the identification was made (may be incomplete, with 00 in the month and or day position)
- `identificationRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:identificationRemarks) - any notes associated with the identification
- `locationName` - the location name for the organismID the voucher belongs to  (if plot the plot name, if point the point name, ...)
- `higherGeography` [DWC](https://dwc.tdwg.org/terms/#dwc:higherGeography) - the parent LocationName '|' separated (e.g. Brasil | Amazonas | Rio Preto da Eva | Fazenda Esteio | Reserva km37);
- `decimalLatitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLatitude) - depends on the location adm_level and the individual X and Y, or Angle and Distance attributes, which are used to calculate these global coordinates for the record; if individual has multiple locations (a monitored bird), the location closest to the voucher date is obtained
- `decimalLongitude` [DWC](https://dwc.tdwg.org/terms/#dwc:decimalLongitude) - same as for decimalLatitude
- `occurrenceRemarks` [DWC](https://dwc.tdwg.org/terms/#dwc:occurrenceRemarks) - text note associated with this record
- `associatedMedia` [DWC](https://dwc.tdwg.org/terms/#dwc:associatedMedia) - urls to ODB media files associated with the record
- `datasetName` - the name of the ODB Dataset to which the record belongs to [DWC](https://dwc.tdwg.org/terms/#dwc:datasetName)
- `accessRights` - the ODB Dataset access privacy setting - [DWC](https://dwc.tdwg.org/terms/#dwc:accessRights)
- `bibliographicCitation` - the ODB Dataset citation - [DWC](https://dwc.tdwg.org/terms/#dwc:bibliographicCitation)
- `license` - the ODB Dataset license - [DWC](https://dwc.tdwg.org/terms/#dwc:license)

***
## Jobs Endpoint

> The `jobs` endpoints interact with the [UserJobs](/docs/concepts/data-access/#user-jobs) table. The basic usage is getting a list of submitted data import jobs, along with a status message and logs.

### GET parameters
- `status=string` return only jobs for the specified status: "Submitted", "Processing", "Success", "Failed" or "Cancelled";
- `id=list` - the job id or ids ;

#### Response fields
- `id` - the job id
- `status` - the status of the job
- `dispatcher` - the type of the job, e.g, ImportTaxons;
- `log` - the job log messages, usually indicating whether the resources were successfully imported, or whether errors occurred;
others.

***
## Possible errors

> This should be an extensive list of error codes that you can receive while using the API. If you receive any other error code, please file a [bug report](https://github.com/opendatabio/opendatabio/issues)!

- Error 401 - Unauthenticated. Currently not implemented. You may receive this error if you attempt to access some protected resources but didn't provide an API token.
- Error 403 - Unauthorized. You may receive this error if you attempt to import or edit some protected resources, and either didn't provide an API token or your user does not have enough privileges.
- Error 404 - The resource you attempted to see is not available. Note that you *can* receive this code if your user is not allowed to see a given resource.
- Error 413 - Request Entity Too Large. You may be attempting to send a very large import, in which case you might want to break it down in smaller pieces.
- Error 429 - Too many attempts. Wait one minute and try again.
- Error 500 - Internal server error. This indicates a problem with the server code. Please file a [bug report](https://github.com/opendatabio/opendatabio/issues) with details of your request.
