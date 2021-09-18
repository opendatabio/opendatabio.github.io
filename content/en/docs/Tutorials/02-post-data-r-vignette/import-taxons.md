---
title: "Import Taxons"
linkTitle: "Taxons"
author: "A. Vicentini & A. Chalom"
weight: 3
date: 2021-09-09
description: >
  Import Taxons using the OpenDataBio R client
---

{{< alert color="warning" title="Attention">}}
1. For a successful importation of published names, **you just need to provide a `name` attribute**, being the fullname for species and infraspecies (e.g. *Licaria canella tenuicarpa*, *Ocotea delicata*), same form as `GBIF's canonicalName`, i.e. do not include infraspecific ranks;
1. **No need to import parent taxons for published names**, OpenDataBio does that for you. The import process will use the `name` you informed to retrieve taxon info from a nomenclature repository: currently [Tropicos](http://tropicos.org), [GBIF BackBone Taxonomy](https://www.gbif.org/dataset/search) and [ZOOBANK](http://zoobank.org). A fuzzy search will be performed on the GBIF service if needed. If the taxon name is found, it will automatically get the needed information and also all parent taxa (or accepted names if its tagged as a synonym) as needed and will store in the database for you. If your name is invalid, i.e. it is a synonym of a another name, it will be imported but tagged as invalid.
1. If you inform `parent` and it is different from the one detected by the API the informed parent has preference.
1. **For unpublished species** `level`, `parent`, and a `person` or `author_id` must also be provided;
1. The external API will use a fuzzy query while searching GBIF if it does not find an exact match, allowing to detect even when misspelled.  However, spelling errors in names may prevent a taxon from being stored.
1. Taxonomy is hierarchical and the structure within the OpenDataBio may be treelike. So, you may add, if you want, any node of the tree of life into the Taxon table. For these you need to use the `Clade` taxon level. But note, this may not be relevant if you are not going link data to such clade names.
1. Because the nomenclature check is time consuming, be patient.


OpenDataBio is distributed with a Taxon seed table, that include the root of all kingdoms and some high-level nodes for Plants, i.e. the [APWeb Order level tree for Angiosperms](http://www.mobot.org/MOBOT/research/APweb/).
{{< /alert >}}

## A simple published name example

The scripts below were tested on top of the OpenDataBio Seed Taxon table, which contains only down to the order level for Angiosperms.

In the taxons table, families Moraceae, Lauraceae and Solanaceae were not yet registered:
```r
library(opendatabio)
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
exists = odb_get_taxons(params=list(root="Moraceae,Lauraceae,Solanaceae"),odb_cfg=cfg)
```
Returned:
```
data frame with 0 columns and 0 rows
```

Now import some species and one infraspecies for the families above, specifying their fullname (canonicalName):

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ"
cfg = odb_config(base_url=base_url, token = token)
spp = c("Ficus schultesii", "Ocotea guianensis","Duckeodendron cestroides","Licaria canella tenuicarpa")
splist = data.frame(name=spp)
odb_import_taxons(splist, odb_cfg=cfg)
```
Now check with the same code for taxons under those children:
```r
library(opendatabio)
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
exists = odb_get_taxons(params=list(root="Moraceae,Lauraceae,Chrysobalanaceae"),odb_cfg=cfg)
head(exists[,c('id','scientificName', 'taxonRank','taxonomicStatus','parentNameUsage')])
```

Which will return:

```
id                    scientificName  taxonRank taxonomicStatus      parentName
1  252                          Moraceae     Family        accepted         Rosales
2  253                             Ficus      Genus        accepted        Moraceae
3  254                  Ficus schultesii    Species        accepted           Ficus
4  258                        Solanaceae     Family        accepted       Solanales
5  259                     Duckeodendron      Genus        accepted      Solanaceae
6  260          Duckeodendron cestroides    Species        accepted   Duckeodendron
7  255                         Lauraceae     Family        accepted        Laurales
8  256                            Ocotea      Genus        accepted       Lauraceae
9  257                 Ocotea guianensis    Species        accepted          Ocotea
10 261                           Licaria      Genus        accepted       Lauraceae
11 262                   Licaria canella    Species        accepted         Licaria
12 263 Licaria canella subsp. tenuicarpa Subspecies        accepted Licaria canella
```

> Note that although we specified only the species and infraspecies names, the API imported also all the needed parent hierarchy up to family, because orders where already registered.

## An invalid published name example

The name *Licania octandra pallida* (Chrysobalanaceae) has been recently turned into a synonym of *Leptobalanus octandrus pallidus*.

The script below exemplify what happens in such cases.
```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ"
cfg = odb_config(base_url=base_url, token = token)

#lets check
exists = odb_get_taxons(params=list(root="Chrysobalanaceae"),odb_cfg=cfg)
exists
#in this test returns an empty data frame
#data frame with 0 columns and 0 rows

#now import
spp = c("Licania octandra pallida")
splist = data.frame(name=spp)
odb_import_taxons(splist, odb_cfg=cfg)

#see the results
exists = odb_get_taxons(params=list(root="Chrysobalanaceae"),odb_cfg=cfg)
exists[,c('id','scientificName', 'taxonRank','taxonomicStatus','parentName')]
```

Which will return:

```
id                         scientificName  taxonRank taxonomicStatus             parentName
1 264                       Chrysobalanaceae     Family        accepted           Malpighiales
2 265                           Leptobalanus      Genus        accepted       Chrysobalanaceae
3 267                 Leptobalanus octandrus    Species        accepted           Leptobalanus
4 269 Leptobalanus octandrus subsp. pallidus Subspecies        accepted Leptobalanus octandrus
5 266                                Licania      Genus        accepted       Chrysobalanaceae
6 268                       Licania octandra    Species         invalid                Licania
7 270        Licania octandra subsp. pallida Subspecies         invalid       Licania octandra
```

> Note that although we specified only one infraspecies name, the API imported also all the needed parent hierarchy up to family, and because the name is invalid it also imported the acceptedName for this infraspecies and its parents.

## An unpublished species or morphotype

It is common to have unpublished local species names (morphotypes) for plants in plots, or yet to be published taxonomic work. Unpublished designation are project specific and therefore MUST also provide an author as different projects may use the same 'sp.1' or 'sp.A' code for their unpublished taxons.

You may link an unpublished name as any taxon level and do not need to use genus+species logic to assign a morphotype for which the genus or upper level taxonomy is undefined. For example, you may store a 'species' level with name 'Indet sp.1' and parent_name 'Laurales', if the lowest level formal determination you have is the order level. In this example, there is no need to store a Indet genus and Indet family taxons just to account for this unidentified morphotype.

```r
##assign an unpublished name for which you only know belongs to the Angiosperms and you have this node in the Taxon table already
library(opendatabio)
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)

#check that angiosperms exist
odb_get_taxons(params=list(name='Angiosperms'),odb_cfg = cfg)

#if it is there, start creating a data.frame to import
to.odb = data.frame(name='Morphotype sp.1', parent='Angiosperms', stringsAsFactors=F)

#get species level numeric code
to.odb$level=odb_taxonLevelCodes('species')

#you must provide an author that is a Person in the Person table. Get from server
odb.persons = odb_get_persons(params=list(search='João Batista da Silva'),odb_cfg=cfg)
#found
head(odb.persons)

#add the author_id to the data.frame
#NOTE it is not author, but author_id or person)
#this makes odb understand it is an unpublished name
to.odb$author_id = odb.persons$id

#import
token ="GZ1iXcmRvIFQ" #this must be your token not this value
cfg = odb_config(base_url=base_url, token = token)
odb_import_taxons(to.odb,odb_cfg = cfg)
```

Check the imported record:
```r
exists = odb_get_taxons(params=list(name='Morphotype sp.1'),odb_cfg = cfg)
exists[,c('id','scientificName', 'taxonRank','taxonomicStatus','parentName','scientificNameAuthorship')]
```
Some columns for the imported record:

```
id  scientificName taxonRank taxonomicStatus  parentName              scientificNameAuthorship
1 276 Morphotype sp.1   Species     unpublished Angiosperms João Batista da Silva - Silva, J.B.D.
```

## Import a published clade

You may add a clade Taxon and may reference its publication using the `bibkey` entry. So, it is possible to actually store all relevant nodes of any phylogeny in the Taxon hierarchy.

```r
#parent must be stored already
odb_get_taxons(params=list(name='Pagamea'),odb_cfg = cfg)

#define clade Taxon
to.odb = data.frame(name='Guianensis core', parent_name='Pagamea', stringsAsFactors=F)
to.odb$level = odb_taxonLevelCodes('clade')

#add a reference to the publication where it is published
#import bib reference to database beforehand
odb_get_bibreferences(params(bibkey='prataetal2018'),odb_cfg=cfg)
to.odb$bibkey = 'prataetal2018'

#then add valid species names as children of this clade instead of the genus level
children = data.frame(name = c('Pagamea guianensis','Pagamea angustifolia','Pagamea puberula'),stringsAsFactors=F)
children$parent_name = 'Guianensis core'
children$level = odb_taxonLevelCodes('species')
children$bibkey = NA

#merge
to.odb = rbind(to.odb,children)

#import
odb_import_taxons(to.odb,odb_cfg = cfg)

```
