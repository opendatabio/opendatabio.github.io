---
title: "POST data"
linkTitle: "POST data"
weight: 3
description: >
  How to import data into OpenDataBio!
---

{{< alert color="warning">}}
- The [OpenDataBio R package](https://github.com/opendatabio/opendatabio-r) is a  **client** for this API.

- Importing data from files through the web-interface require specifying the POST parameters here specified

- Batch imports of  [Bibliographic References](/docs/concepts/auxiliary-objects/#bibreference) and [MediaFiles](/docs/concepts/auxiliary-objects/#media) are possible only through the web interface.

- Authentication token is required for all POST

{{< /alert >}}


## POST Individuals


Request fields allowed when importing individuals:

* `collector=mixed` - **required**  - persons  'id', 'abbreviation', 'full_name', 'email'; if multiple persons, separate values in your list with  pipe `|` or `;` because commas may be present within names. Main collector is the first on the list;
* `tag=string` -  **required** -  the individual number or code (if the individual identifier is as MainCollector+Number, this is the field for Number);
* `dataset=mixed` - **required** - name or id of the Dataset;
* `date=YYYY-MM-DD or array` - the date the individual was recorded/tagged, for historical records you may inform an incomplete string in the form "1888-05-NA" or "1888-NA-NA" when day and/or month are unknown. You may also inform as an array in the form "date={ 'year' : 1888, 'month': 5}". OpenDataBio deals with incomplete dates, see the [IncompleteDate Model](/docs/concepts/auxiliary-objects/#incomplete-date). At least `year` is **required**.
* `notes` - any annotation for the Individual;

Location fields (one or multiple locations may be informed for the individual). Possible fields are:
  * `location` - the Individual's location name or id **required if longitude and latitude are not informed**
  * `latitude` and `longitude`- geographical coordinates in decimal degrees; **required if location is not informed**
  * `altitude` - the Individual location elevation (altitude) in meters above see level;
  * `location_notes` - any note for the individual location;
  * `location_date_time` - if different than the individual's date, a complete date or a date+time value for the individual first location. Mandatory for multiple locations;
  * `x` - if location is of Plot type, the x coordinate of the individual in the location;
  * `y` - if location is of Plot type, the y coordinate of the individual in the location;
  * `distance` - if location is of POINT type, the individual distance in meters from the location;
  * `angle` - if location is of POINT type, the individual azimuth (angle) from the location;

Identification fields. Identification is not mandatory, and may be informed in two different ways: (1) self identification - the individual may have its own identification; or (2), other identification - the identification is the same as that of another individual (for example, from an individual having a voucher in some biocollection).
  1. The following fields may be used for (self) individual's identification (taxon and identifier are mandatory):
    * `taxon=mixed` - name or id of the identified taxon, e.g. 'Ocotea delicata' or its id
    * `identifier=mixed` - 'id', 'abbreviation', 'full_name' or  'email' of the person responsible for the taxonomic identification;
    * `identification_date` or `identification_date_year`, `identification_date_month`, and/or `identification_date_day` - complete or incomplete. If empty, the individual's date is used;
    * `modifier` - name or number for the identification modifier. Possible values 's.s.'=1, 's.l.'=2, 'cf.'=3, 'aff.'=4, 'vel aff.'=5, defaults to 0 (none).
    * `identification_notes` - any identification notes
    * `identification_based_on_biocollection` - the biocollection name or id if the identification is based on a reference specimen deposited in an biocollection
    * `identification_based_on_biocollection_id` - only fill if `identification_based_on_biocollection` is present;
  2. If the identification is other:
    * `identification_individual` - id or fullname (organimsID) of the Individual having the identification.

If the Individual has Vouchers **with the same** Collectors, Date and CollectorNumber (Tag) as those of the Individual, the following fields and options allow to store the vouchers while importing the Individual record (alternatively, you may import voucher after importing individuals using the [Voucher EndPoint](/docs/api/post-data/#post-vouchers). Vouchers for the individual may be informed in two ways:
1. As separate string fields:
  * `biocollection` - A string with a single value or a comma separated list of values. Values may be the `id` or `acronym` values of the [Biocollection Model](/docs/concepts/data-access/#biocollection). Ex:  "{ 'biocollection' : 'INPA;MO;NY'}" or "{ 'biocollection' : '1,10,20'}";
  * `biocollection_number` - A string with a single value or a comma separated list of values with the BiocollectionNumber for the Individual Voucher. If a list, then must have the same number of values as `biocollection`;
  * `biocollection_type` - A string with a single numeric code value or a comma separated list of values for Nomenclatural Type for the Individual Vouchers. The default value is 0 (Not a Type). See [nomenclatural types list](/docs/api/quick-reference/#nomenclatural-types).
2. AS a single field `biocollection` containing an array with each element having the fields above for a single Biocollection:
  "{
    { 'biocollection_code' : 'INPA', 'biocollection_number' : 59786, 'biocollection_type' : 0},
    { 'biocollection_code' : 'MG', 'biocollection_number' : 34567, 'biocollection_type' : 0}
  }"

***

## POST Individual-locations

The `individual-locations` endpoint allows importing multiple locations for registered individuals. Designed for occurrences of organisms that move and have multiple locations.

Possible fields are:
  * `individual` - the Individual's id **required**
  * `location` - the Individual's location name or id **required OR longitude+latitude**
  * `latitude` and `longitude`- geographical coordinates in decimal degrees; **required if location is not informed**
  * `altitude` - the Individual location elevation (altitude) in meters above see level;
  * `location_notes` - any note for the individual location;
  * `location_date_time` - if different than the individual's date, a complete date or a date+time (hh:mm:ss) value for the individual location. **required**
  * `x` - if location is of Plot type, the x coordinate of the individual in the location;
  * `y` - if location is of Plot type, the y coordinate of the individual in the location;
  * `distance` - if location is of POINT type (or latitude and longitude are informed), the individual distance in meters from the location;
  * `angle` - if location is of POINT type, the individual azimuth (angle) from the location



***
## POST Locations

The `locations` endpoints interact with the [locations](/docs/concepts/core-objects/#location) table. Use to import new locations.

{{< alert color="warning" title="Attention">}}
ODB Locations are stored with a parent-child relationship, assuring validations and facilitating queries. Parents will be guessed using the location geometry. If parent is not informed, the imported location must be completely contained by a registered parent (using sql [ST_WITHIN](https://postgis.net/docs/ST_Within.html) function to  detect parent).  However, if a parent is informed, the importation may also test if the geometry fits a buffered version of the parent geometry, thus ignoring minor geometries overlap and shared borders. Countries can be imported without parent relations. Any other location must be registered within at least a 'country' as parent. If the record is marine, and falls outside of a registered country polygon, a 'ismarine' argument must be indicated to accept the non-spatial parent relationship.
{{< /alert >}}

Make sure your geometry projection is **EPSG:4326** **WGS84**. Use this standard!

Available POST fields:
* `name` -  the location name  - **required** (parent+name must be unique in the database)
* `adm_level` - must be numeric, see [location get api](/docs/api/get-data/#locations-endpoint) - **required**
* geometry use either: **required**
  * `geom` for a WKT representation of the geometry, POLYGON, MULTIPOLYGON, POINT OR LINESTRING allowed;
  * `lat` and `long` for latitude and longitude in decimal degrees (use negative numbers for south/west).  
* `altitude` - in meters
* `datum` - defaults to 'EPSG:4326-WGS 84' and your are strongly encourage of importing only data in this projection. You may inform a different projection here;
* `parent` - either the id or name of the parent location. The API will detect the parent based on the informed geometry and the *detected parent has priority* if informed is different. However, only when parent is informed, validation will also test whether your location falls within a buffered version of the informed parent, allowing to import locations that have a parent-child relationship but their borders overlap somehow (either shared borders or differences in georeferencing);
* when location is plot (`adm_level=100`), optional fields are:
  * `x` and `y` for the plot dimensions in meters(defines the Cartesian coordinates)
  * `startx` and `starty` for start position of a subplot in relation to its parent plot location;
* `notes` - any note you wish to add to your location;
* `ismarine` - to permit the importation of location records that not fall within any register parent location you may add ismarine=1. Note, however, that this allows you to import misplaced locations. Only use if your location is really a marine location that fall outside any Country border;

**alternatively**: you may just submit a single column named `geojson` containing a Feature record, with its geometry and having as 'properties' at least tags `name` and `adm_level` (or `admin_level`). See [geojson.org](https://geojson.org/). This is usefull, for example, to import country political boundaries (https://osm-boundaries.com/).

***
## POST Measurements

The `measurements` endpoint allows to import [measurements](/docs/concepts/trait-objects/#measurement).

The following fields are allowed in a post API:
* `dataset=number` -  the id of the dataset where the measurement should be placed **required**
* `date=YYYY-MM-DD` - the observation date for the measurement, must be passed as YYYY-MM-DD  **required**
* `object_type=string` - either 'Individual','Location','Taxon' or 'Voucher', i.e. the object from where the measurement was taken **required**
* `object_type=number` - the id of the measured object, either (individuals.id, locations.id, taxons.id, vouchers.id) **required**
* `person=number or string` either the 'id', 'abbreviation', 'full_name' or 'email' of the person that measured the object **required**
* `trait_id=number or string` - either the id or export_name for the measurement. **required**
* `value=number, string list` - this will depend on the trait type, see tutorial **required, optional for trait type LINK**
* `link_id=number` - the id of the linked object for a Trait of type Link **required if trait type is Link**
* `bibreference=number` - the id of the BibReference for the measurement. Should be use when the measurement was taken from a publication
* `notes` - any note you whish. In same cases this is a usefull place to store measurement related information. For example, when measuring 3 leaves of a voucher, you may indicate here to which leaf the measurement belongs, leaf1, leaf2, etc. allowing to link measurements from different traits by this field.
* `duplicated` - by default, the import API will prevent duplicated measurements for the same trait, object and date; specifying `duplicated=1` will allow to import such duplicated measurements. Will assume 0 if not informed and will then check for duplicated values.



***
## POST Persons

The `persons` endpoint interact with the [Person](/docs/concepts/auxiliary-objects/#person) table. The following fields are allowed when importing persons using the post API:
- `full_name` - person full name, **required**
- `abbreviation` - abbreviated name, as used by the person in publications, as collector, etc. (if left blank, a standard abbreviation will be generated using the full_name attribute -- abbreviation must be unique within a ODB installation);
- `email` - an email address,
- `institution` - to which institution this person is associated;
- `biocollection` - name or acronym of the Biocollection to which this person is associated.

***
## POST Taxons

Use to import new [taxon](/docs/concepts/core-objects/#taxon) names.

The POST API requires ONLY the **full name** of the taxon to be imported, i.e. for species or below species taxons the complete name must be informed (e.g. *Ocotea guianensis*  or *Licaria cannela aremeniaca*). The script will validate the name retrieving the remaining required info from the nomenclatural databases using their API services. It will search GBIF and Tropicos if the case and retrieve taxon info, their ids in these repositories and also the full classification path and senior synonyms (if the case) up to when it finds a valid record name in this ODB database. So, unless you are trying to import unpublished names, just submit the name parameter of the list below.

Possible fields:
* `name` - taxon full name **required**, e.g. "Ocotea floribunda"  or "Pagamea plicata glabrescens"
* `level` - may be the numeric id or a string describing the taxonRank **recommended for unpublished names**
* `parent` - the taxon's parent full name or id - **note** - if you inform a valid parent and the system detects a different parent through the API to the nomenclatural databases, preference will be given to the informed parent; **required for unpublished names**
* `bibreference` - the bibliographic reference in which the taxon was published;
* `author` - the taxon author's name;
* `author_id` or `person` - the registered Person name, abbreviation, email or id, representing the author of unpublished names -  **required for unpublished names**
* `valid` - boolean, true if this taxon name is valid; 0 or 1
* `mobot` - Tropicos.org id for this taxon
* `ipni` - IPNI id for this taxon
* `mycobank` - MycoBank id for this taxon
* `zoobank` - ZOOBANK id for this taxon
* `gbif` - GBIF nubKey for this taxon


***
## POST Traits

> When entering few traits, it is **strongly recommended** that you enter traits one by one using the Web Interface form, which reduces the chance of duplicating trait definitions.

The `traits` endpoint interact with the [Trait](/docs/concepts/trait-objects/#trait) table. The POST method allows you to batch import traits into the database and is designed for transferring data to OpenDataBio from other systems, including Trait Ontologies.  
  1. As noted under the [Trait Model](/docs/concepts/trait-objects/#trait) description, it is important that one really checks whether a needed Trait is not already in the DataBase to avoid multiplication of redundant traits. The Web Interface facilitates this process. Through the API, OpenDataBio only checks for identical `export_name`, which must be unique within the database. Note, however, that Traits should also be as specific as possible for detailed metadata annotations.  
  1. Traits use [User Translations](/docs/concepts/auxiliary-objects/#user-translation) for names and descriptions, allowing a multiple-languages

Fields allowed for the traits/ post API:

* `export_name=string` - a short name for the Trait, which will be used during data exports, are more easily used in trait selection inputs in the web-interface and also during data analyses outside OpenDataBio. **Export names must be unique** and have no translation. Short and [CamelCase](https://en.wikipedia.org/wiki/Camel_case) export names are recommended. Avoid diacritics (accents), special characters, dots and even white-spaces. **required**
* `type=number` - a numeric code specifying the trait type. See the [Trait Model](/docs/concepts/trait-objects/#trait) for a full list. **required**
* `objects=list` - a list of the [Core objects](docs/concepts/core-objects) the trait is allowed to be measured for. Possible values are 'Individual', 'Voucher', 'Location' and/or 'Taxon', singular and case sensitive. Ex:  "{'object': 'Individual,Voucher'}"; **required**
* `name=json` - see translations below; **required**
* `description=json` - see translations below; **required**
* **Trait specific fields**:
  * `units=string` - required for quantitative traits only (the unit o measurement). recommened to use English standards and full words, e.g. ('meters' instead of just 'm')
  * `range_min=number` - optional for quantitative traits. specify the minimum value allowed for a [Measurement](/docs/concepts/trait-objects/#measurement).
  * `range_max=number` - optional for quantitative. maximum allowed value for the trait.
  * `categories=json` - **required for categorical and ordinal traits**; see translations below
  * `wavenumber_min` and `wavenumber_max` - **required for spectral traits** = minimum and maximum WaveNumber within which the 'value_length' absorbance or reflectance values are equally distributed. May be informed in `range_min` and `range_max`, priority for prefix wavenumber over range if both informed.
  * `value_length` - **required for spectral traits** = number of values in spectrum
  * `link_type`- **required for Link traits** - the class of link type, fullname or basename:  eg. 'Taxon' or 'App\Models\Taxon'.
* `bibreference=number` - the id of a [BibReference](/docs/concepts/auxiliary-objects/#bibreference) object from which the trait definition and or trait categories are based upon.

#### Translations

* Fields `name`, `description` must have the following structure to account for  [User Translations](/docs/concepts/auxiliary-objects/#user-translation). They should be a list with the language as 'keys'. For example a `name` field may be informed as:
  * using the Language code as keys: `{"en":"Diameter at Breast Height","pt":"Di\u00e2metro a Altura do Peito"}`
  * or using the Language ids as keys:  `{"1":"Diameter at Breast Height","2":"Di\u00e2metro a Altura do Peito"}`.
  * or using the Language names as keys:  `{"English":"Diameter at Breast Height","Portuguese":"Di\u00e2metro a Altura do Peito"}`.
* Field `categories` must include for each category+rank+lang the following fields:
  * `lang=mixed` - the id, code or name of the language of the translation, **required**
  * `name=string` - the translated category name  **required** (name+rank+lang must be unique)
  * `rank=number` - the rank for ordinal traits; for non-ordinal, rank is important to indicate the same category across languages, so may just use 1 to number of categories in the order you want. **required**
  * `description=string` - optional for categories, a definition of the category.
  * Example:
    ```JSON
      [
        {"lang":"en","rank":1,"name":"small","description":"smaller than 1 cm"},
        {"lang":"pt","rank":1,"name":"pequeno","description":"menor que 1 cm"}
        {"lang":"en","rank":1,"name":"big","description":"bigger than 10 cm"},
        {"lang":"pt","rank":1,"name":"grande","description":"maior que 10 cm"},
      ]
    ```

* Valid languages may be retrieved with the [Language API](/docs/api/get-data/#languages-endpoint).

***
## POST Vouchers

The `vouchers` endpoints interact with the [Voucher](/docs/concepts/core-objects/#voucher) table.

The following fields are allowed in a post API:
* `individual=mixed` - the numeric id or organismID of the Individual the Voucher belongs to **required**;
* `biocollection=mixed` - the id, name or acronym of a registered [Biocollection](/docs/concepts/data-access/#biocollection) the Voucher belongs to **required**;
* `biocollection_type=mixed` - the name or numeric code representing the kind of nomenclature type the Voucher represents in the Biocollection. If not informed, defaults to 0 = 'Not a Type'. See [nomenclature types](/docs/api/quick-reference/#nomenclature-types) for a full list of options;
* `biocollection_number=mixed` - the alpha numeric code of the voucher in the biocollection;
* `number=string` - the main *collector number*  -only if different from the *tag* value of the Individual the voucher belongs to;
* `collector=mixed` - either ids or abbreviations of persons. When multiple values are informed the first is the *main collector*. Only if different from the Individual collectors list;
* `date=YYYY-MM-DD or array` - needed only if, with collector and number, different from Individual values.  Date may be an [IncompleteDate Model](/docs/concepts/auxiliary-objects/#incomplete-date).
* `dataset=number` - inherits the project the Individual belongs too, but you may provide a different project if needed
* `notes=string` - any text note to add to the voucher.
