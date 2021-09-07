---
title: "Overview"
linkTitle: "Overview"
weight: 1
description: >
  What is OpenDataBio?
---

OpenDataBio is an opensource web-based platform designed to help researchers and organizations studying biodiversity in Tropical regions to collect, store, related and serve data. It is designed to accommodate many data types used in biological sciences and their relationships, particularly biodiversity and ecological studies, and serves as a data repository that allow users to download or request well-organized and documented research data.

## Why?

Biodiversity studies frequently require the integration of a large amount of data, which require standardization for data use and sharing, and also continuous management and updates, particularly in Tropical regions where biodiversity is huge and poorly known.

OpenDataBio was designed based on the need to organize and integrate historical and current data collected in the Amazon region, taking into account field practices and data types used by ecologists and taxonomists.

Graduate students, NGOs and Government institutions still largely use spreadsheets for organizing data, unfit to the required level of data normalization and standardization such data entail, or use proprietary software.

OpenDataBio aim to facilitate the standardization and normalization of data, utilizing different API services available online, giving flexibility to user and user groups, and creating the necessary links among Locations, Taxons, Individuals, Vouchers and the Measurements and Media-files associated with them, while offering accessibility to the data through an API service, facilitating data distribution and analyses.

## Main features

1. **Custom variables** - the ability to define custom [Traits](/docs/concepts/trait-objects), i.e. user defined variables of different types, including some special cases like Spectral Data, Colors and Links. [Measurements](/docs/concepts/trait-objects/#measurement) for such traits can be recorded for [Individuals](/docs/concepts/core-objects/#individual), [Vouchers](/docs/concepts/core-objects/#voucher), [Taxons](/docs/concepts/core-objects/#taxon) or [Locations](/docs/concepts/core-objects/#location).
1. [Taxons](/docs/concepts/core-objects/#taxon) can be **published or unpublished names** (e.g. a morphotype), synonyms or valid names, and any node of the tree of life may be stored. Taxon insertion are checked against different nomenclature data sources (*Tropicos, IPNI, MycoBank,ZOOBANK, GBIF*), minimizing your search for correct spelling, authorship and synonyms.
1. [Locations](/docs/concepts/core-objects/#location) are stored with their **spatial Geometries**, allowing location parent detection and spatial queries. Special location types, such as **Plots** and **Transects** can be defined, facilitating commonly used methods in biodiversity studies
1. **Data access control** - data are organized in [Datasets](/docs/concepts/data-access/#dataset) that permits to define an access policy (public, non-public) and a license for distribution of public datasets, becoming a self-contained dynamic data publication, versioned by the last edit date.
1. Different research groups may use a single OpenDataBio installation, having total control over their particular research data edition and access, while sharing common libraries such as Taxonomy, Locations, Bibliographic References and Trait definitions.
1. **API to access data programatically** - Tools for data exports and imports are provided through [API services](/docs/api) along with a API client in the R language, the [OpenDataBio-R package](https://github.com/opendatabio/opendatabio-r).
1. **Autiting** - the [Activity Model](/docs/concepts/auxiliary-objects/#auditing) audits changes in any record and downloads of full datasets, which are logged for history tracking.
1. A mobile data collector is planned with [ODK](https://getodk.org/) or [ODK-X](https://odk-x.org/)

## Learn more

* [Getting Started](/docs/getting-started/): install OpenDataBio!
* [FAQs](/docs/apis/): Check out some example code!
