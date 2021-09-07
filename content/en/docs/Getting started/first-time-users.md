---
title: "First time users"
linkTitle: "First time users"
weight: 3
description: >
  Tips to first time users!
---

> OpenDataBio is software to be used online. Local installations are for testing or development, although it could be used for a single-user production localhost environment.

## User roles

* If you are installing, the first login to an OpenDataBio installation must be done with the default super-admin user:  `admin@example.org` and `password1`. These settings should be changed or the installation will be open to anyone reading the docs;
* **Self-registrations** only grant access to datasets with privacy set to `registered users` and allows user do download data of open-access, but do not allow the user to edit nor add data;
* Only **full users**  can contribute with data
* But only **super admin can grant `full-user role`** to registered users - different OpenDataBio installations may have different policies as to how you may gain full-user access. Here is not the place to find that info.

See also [User Model](/docs/concepts/data-access/#user).

## Prep your full-user account

1. Register yourself as [Person](/docs/concepts/auxiliary-objects/#person) and assign it as your user default person, creating a link between your user and yourself as collector.
1. You need at least a **dataset** to enter your own data
  1. When becoming a full-user, a restricted-access [Dataset](/docs/concepts/data-access/#dataset) and [Project](/docs/concepts/data-access/#project) will be automatically created for you (your **Workspaces**). You may modify these entities to fit your personal needs.
  1. You may create as many Projects and Datasets as needed. So, understand how they work and which data they control access to.

## Entering data

There three main ways to import data into OpenDataBio:
1. One by one through the web Interface
1. Using the [OpenDataBio POST API services](/docs/api):
    1. importing from a spreadsheet file (CSV, XLXS or ODS) using the web Interface
    1. using the [OpenDataBio R package](https://github.com/opendatabio/opendatabio-r) client
1. When using the [OpenDataBio API services](/docs/api) you must prep your data or file to import according to the field options of the [POST verb](/docs/post-data) for the specific 'endpoint' your are trying to import.  Check also the [API quick reference](/docs/quick-reference).

## Tips for entering data

1. If first time entering data, you should use the web interface and create at least one record for each model needed to fit your needs. Then play with the privacy settings of your Workspace [Dataset](/docs/concepts/data-access/#dataset), and check whether you can access the data when logged in and when not logged in.
1. Use [Dataset](/docs/concepts/data-access/#dataset) for a self-contained set of data that should be distributed as a group. Datasets are **dynamic publications**, have author, data, and title.
1. Although ODB attempt to minimize redundancy, giving users flexibility comes with a cost, and some definitions, like that of [Traits](/docs/concepts/trait-objects/#trait) or [Persons](/docs/concepts/auxiliary-objects/#person) may receive duplicated entries. So, care must be taken when creating such records. Administrators may create a 'code of conduct' for the users of an ODB installation to minimize such redundancy.
1. Follow an order for importation of new data, starting from the libraries of common use. For example, you should first register [Locations](/docs/concepts/core-objects/#location), [Taxons](/docs/concepts/core-objects/#taxon), [Persons](/docs/concepts/auxiliary-objects/#person), [Traits](/docs/concepts/trait-objects/#trait) and any other common library **before importing** [Individuals](/docs/concepts/core-objects/#individual) or [Measurements](/docs/concepts/trait-objects/#measurement)
1. There is **no need** to import POINT locations before importing Individuals because ODB creates the location for you when you inform latitude and longitude, and will detect for you to which parent location your individual belongs to. However, if you want to validate your points (understand where such point location will placed), you may use the [Location API](/docs/api/get-data/#locations-endpoint) with `querytype`  parameter specified for this.
1. There are different ways to create PLOT and TRANSECT locations - see here [Locations](/docs/concepts/core-objects/#location) if that is your case
1. Creating [Taxons](/docs/api/post-data/#post-taxons) require only the specification of a `name` - ODB will search nomenclature services for you, find the name, metadata and parents and import all of the them if needed. If you are importing published names, just inform this single attribute. Else, if the name is unpublished, you need to inform  additional fields. So, separate the batch importation of published and unpublished names into two sets.
