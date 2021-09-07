
---
title: "API services"
linkTitle: "API services"
weight: 3
description: >
  How to import and get data!
---

> Every OpenDataBio installation provide a API service, allowing users to GET data programmatically, and collaborators to POST new data into its database. The service is open access to public data, requires user authentication to POST data or GET data of restricted access.

The OpenDataBio API ([Application Programming Interface -API](https://en.wikipedia.org/wiki/API)) allows users to interact with an OpenDataBio database for exporting and importing data without using the web-interface.

<a href="https://www.r-project.org/logo/"><img src="Rlogo.png" style="margin: 0px 5px 10px 0px; float: left; width:45px; border:0" /></a> The [OpenDataBio R package](https://github.com/opendatabio/opendatabio-r) is a  **client** for this API, allowing the interaction with the data repository directly from R and illustrating the API capabilities so that other clients can be easily built.

The OpenDataBio API allows querying of the database and data importation through a [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) inspired interface. All API requests and responses are formatted in [JSON](https://en.wikipedia.org/wiki/JSON).

## The API call

A simple call to the OpenDataBio API has four independent pieces:

{{< alert color="success" >}}[HTTP-verb] + base-URL +  endpoint + request-parameters {{< /alert >}}

1. **HTTP-verb** -  either `GET` for exports  or  `POST` for imports.
1. **base-URL** - the URL used to access your OpenDataBio server + plus `/api/v0`. For, example, `http:// opendatabio.inpa.gov.br/api/v0`
1. **endpoint** - represents the object or collection of objects that you want to access, for example, for querying taxonomic names, the endpoint is "taxons"
1. **request-parameters** - represent filtering and processing that should be done with the objects, and are represented in the API call after a question mark. For example, to retrieve only valid taxonomic names (non synonyms) end the request with `?valid=1`.

The API call above can be entered in a browser to GET public access data. For example, to get the list of valid taxons from an OpenDataBio installation the API request could be:

```bash
http://opendb.inpa.gov.br/api/v0/taxons?valid=1&limit=10
```

When using the [OpenDataBio R package](https://github.com/opendatabio/opendatabio-r) this call would be `odb_get_taxons(list(valid=1))`.

A response would be something like:

```JSON
{
  "meta":
  {
    "odb_version":"0.9.1-alpha1",
    "api_version":"v0",
    "server":"http://opendb.inpa.gov.br",
    "full_url":"http://opendb.inpa.gov.br/api/v0/taxons?valid=1&limit1&offset=100"},
    "data":
    [
      {
        "id":62,
        "parent_id":25,
        "author_id":null,
        "scientificName":"Laurales",
        "taxonRank":"Ordem",
        "scientificNameAuthorship":null,
        "namePublishedIn":"Juss. ex Bercht. & J. Presl. In: Prir. Rostlin: 235. (1820).",
        "parentName":"Magnoliidae",
        "family":null,
        "taxonRemarks":null,
        "taxonomicStatus":"accepted",
        "ScientificNameID":"http:\/\/tropicos.org\/Name\/43000015 | https:\/\/www.gbif.org\/species\/407",
        "basisOfRecord":"Taxon"
    }]}
```


***

## API Authentication

1. **Not required** for getting any data with public access in the ODB database, which by default includes locations, taxons, bibliographic references, persons and traits.
1. **Authentication Required** to GET any data that is not of public access.
  - Authentication is done using an `API token`, that can be found under your user profile on the web interface. The token is assigned to a single database user, and should not be shared, exposed, e-mailed or stored in version controls.
  - To authenticate against the OpenDataBio API, use the token in the "Authorization" header of the API request. When using the R client, pass the token to the `odb_config` function `cfg = odb_config(token="your-token-here")`.

Users will only have access to the data for which the user has permission and to, or any data with public access in the database, which by default includes locations, taxons, bibliographic references, persons and traits. Measurements, individuals, and Vouchers access depends on permissions understood by the users token.


***

## API versions

The OpenDataBio API follows its own version number. This means that the client can expect to use the same code and get the same answers regardless of which OpenDataBio version that the server is running. All changes done within the same API version (>= 1) should be backward compatible. Our API versioning is handled by the URL, so to ask for a specific API version, use the version number between the base URL and endpoint:

```bash
http://opendatabio.inpa.gov.br/opendatabio/api/v1/taxons

http://opendatabio.inpa.gov.br/opendatabio/api/v2/taxons

```

{{< alert color="warning" title="Version v0">}}The API version 0 (v0) is an unstable version. The first stable version will be the API version 1.
{{< /alert >}}
