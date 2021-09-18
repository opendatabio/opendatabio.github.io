---
title: "Importar Taxons"
linkTitle: "Taxons"
author: "A. Vicentini & A. Chalom"
weight: 3
date: 2021-09-09
description: >
  Importar Taxons usando o pacote OpenDataBio R
---

{{< alert color="warning" title="Atenção">}}
1. Para uma importação bem-sucedida de nomes taxomicos publicados **você só precisa fornecer um  `name`**, sendo o nome completo para espécies e infra-espécies (por exemplo, *Licaria canella tenuicarpa*, *Ocotea delicata*), da mesma forma que`canonicalName` do GBIF, ou seja, não inclui os ranks infra-específicos;
1. **Não há necessidade de importar táxons pais para nomes publicados**, OpenDataBio faz isso para você. O processo de importação usará o `name` que você informou para recuperar informações de táxons de um repositório de nomenclatura: atualmente [Tropicos](http://tropicos.org), [GBIF BackBone Taxonomy](https://www.gbif.org/dataset/search) e [ZOOBANK](http://zoobank.org). Se o nome do táxon for encontrado, ele obterá automaticamente as informações necessárias e também todos os táxons pais (ou nomes aceitos se estiver marcado como sinônimo) conforme necessário, e armazenará no banco de dados para você. Se o `name` for inválido, ou seja, é sinônimo de outro nome, ele será importado, mas marcado como inválido.
1. Se informar `parent` e for diferente do detectado pela API o pai informado tem preferência.
1. **Para espécies não publicadas** `level`,` parent`, e uma `person` ou `author_id` também devem ser fornecidos;
1. A API externa usará uma consulta difusa durante a pesquisa de GBIF se não encontrar uma correspondência exata, permitindo detectar até mesmo quando está com erros ortográficos. No entanto, erros de grafia nos nomes podem impedir que um táxon seja armazenado.
1. A taxonomia é hierárquica e a estrutura dentro do OpenDataBio pode ser semelhante a uma árvore. Portanto, você pode adicionar, se quiser, qualquer nó da árvore da vida na tabela Taxon. Para isso, você precisa usar o nível do táxon `Clade`. Mas observe que isso pode não ser relevante se você não vincular dados a esses nomes de clados.
1. Como a verificação da nomenclatura é demorada, seja paciente.

OpenDataBio é distribuído com uma tabela semente de Taxons, que inclui a raiz de todos os reinos e alguns nós da filogenia das plantas, ou seja, o os nós da àrvore no nível de `ordem` para Angiospermas - [APWeb Tree](http://www.mobot.org/MOBOT/research/APweb/).
{{< /alert >}}


## Um exemplo simples de nome publicado

Os scripts abaixo foram testados, estando a base já populada com até o nível de Ordem para Angiospermas.

Na tabela de táxons, as famílias Moraceae, Lauraceae e Solanaceae ainda não estavam registradas:

```r
library(opendatabio)
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
exists = odb_get_taxons(params=list(root="Moraceae,Lauraceae,Solanaceae"),odb_cfg=cfg)
```
Retornou:
```
data frame with 0 columns and 0 rows
```
Agora importe algumas espécies e uma infraespécie para as famílias acima, especificando seu nome completo (canonicalName):

```r
library(opendatabio)
base_url="http://localhost:8080/api"
token ="GZ1iXcmRvIFQ"
cfg = odb_config(base_url=base_url, token = token)
spp = c("Ficus schultesii", "Ocotea guianensis","Duckeodendron cestroides","Licaria canella tenuicarpa")
splist = data.frame(name=spp)
odb_import_taxons(splist, odb_cfg=cfg)
```

Agora verifique se foi importado (note o argumento root):

```r
library(opendatabio)
base_url="http://localhost:8080/api"
cfg = odb_config(base_url=base_url)
exists = odb_get_taxons(params=list(root="Moraceae,Lauraceae,Chrysobalanaceae"),odb_cfg=cfg)
head(exists[,c('id','scientificName', 'taxonRank','taxonomicStatus','parentNameUsage')])
```

Retorno:


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

> Observe que embora tenhamos especificado apenas os nomes das espécies e infra-espécies, a API importou também toda a hierarquia parental necessária até a família, porque as ordens já estavam registradas.

## Um exemplo de nome publicado inválido

O nome *Licania octandra pallida* (Chrysobalanaceae) foi recentemente movido com sinônimo de *Leptobalanus octandrus pallidus*.

O roteiro a seguir exemplifica o que acontece nesses casos.


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

Retorno:

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

> Observe que, embora tenhamos especificado apenas um nome de infra-espécie, a API importou também toda a hierarquia pai necessária até a família e, como o nome é inválido, também importou o nome aceito para esta infra-espécie e seus pais.

## Uma espécie ou morfotipo não publicado

É comum ter nomes de espécies locais não publicados (morfotipos) para plantas em parcelas, ou ainda trabalhos taxonômicos ainda não publicados. As designações não publicadas são específicas do projeto e, portanto, DEVEM também fornecer um autor, pois diferentes projetos podem usar o mesmo código 'sp.1' ou 'sp.A' para seus táxons não publicados.

Você pode vincular um nome não publicado como qualquer nível de táxon e não precisa usar a lógica de gênero + espécie para atribuir um morfotipo para o qual o gênero ou taxonomia de nível superior é indefinida. Por exemplo, você pode armazenar um nível de 'espécie' com o nome 'Indet sp.1' e parent_name 'Laurales', se a determinação formal de nível mais baixo que você tem é o nível de ordem. Neste exemplo, não há necessidade de armazenar um gênero Indet e táxons da família Indet apenas para contabilizar este morfotipo não identificado.

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
Verifique o registro importado:

```r
exists = odb_get_taxons(params=list(name='Morphotype sp.1'),odb_cfg = cfg)
exists[,c('id','scientificName', 'taxonRank','taxonomicStatus','parentName','scientificNameAuthorship')]
```
Algumas colunas para o registro importado:

```
id  scientificName taxonRank taxonomicStatus  parentName              scientificNameAuthorship
1 276 Morphotype sp.1   Species     unpublished Angiosperms João Batista da Silva - Silva, J.B.D.
```

## Importar um clado publicado

Você pode adicionar um clado Taxon e pode referenciar uma publicação usando a entrada `bibkey`. Portanto, é possível armazenar de fato todos os nós relevantes de qualquer filogenia na hierarquia do Taxon.

```r
#parent já deve estar armazenado
odb_get_taxons(params=list(name='Pagamea'),odb_cfg = cfg)

#define o clado a ser armazenado
to.odb = data.frame(name='Guianensis core', parent_name='Pagamea', stringsAsFactors=F)
to.odb$level = odb_taxonLevelCodes('clade')

#adicione uma referência à publicação onde está publicado
#importe a referência do bib para o banco de dados de antemão
odb_get_bibreferences(params(bibkey='prataetal2018'),odb_cfg=cfg)
to.odb$bibkey = 'prataetal2018'

#então adicione nomes de espécies válidos como filhos deste clado em vez do nível de gênero
children = data.frame(name = c('Pagamea guianensis','Pagamea angustifolia','Pagamea puberula'),stringsAsFactors=F)
children$parent_name = 'Guianensis core'
children$level = odb_taxonLevelCodes('species')
children$bibkey = NA

#merge
to.odb = rbind(to.odb,children)

#import
odb_import_taxons(to.odb,odb_cfg = cfg)

```
