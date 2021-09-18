---
title: "Inserir dados - POST"
linkTitle: "Inserir dados - POST"
weight: 3
description: >
  Como importar dados ao OpenDataBio usando a API
---

{{< alert color="warning">}}
- O [pacote OpenDataBio-R](https://github.com/opendatabio/opendatabio-r) é um **cliente** para esta API.
- Importar dados de arquivos (planilhas) pela interface também requer utilizar os Parâmetros POST listados nesta página
- Importação em lote de [Referências Bibliográficas](/docs/concepts/auxiliary-objects/#bibreference) e [Arquivos de Mídia](/docs/concepts/auxiliary-objects/#media) é possível apenas através da interface.
{{< /alert >}}


## POST Individuals

Campos para importar indivíduos:
* `collector = mixed` - **obrigatório** - pessoas - pode ser 'id', 'abbreviation', 'full_name', 'email'; se houver várias pessoas, separe os valores em sua lista com a barra vertical `|` ou `;` porque vírgulas podem estar presentes nos nomes. O coletor principal é o primeiro da lista;
* `tag = string` - **obrigatório** - o seu código ou número para o indivíduo (número da placa, número de coletor, número de anilha, etc)
* `dataset = mixed` - **obrigatório** - nome ou id do Conjunto de Dados para colocar o Indivíduo
* `date = AAAA-MM-DD ou matriz` - data em que o indivíduo foi coletado/etiquetado -  para registros históricos pode-se informar uma string incompleta no formato "1888-05-NA" ou  "1888-NA-NA"quando dia ou mês são desconhecidos. Pode-se informar também como array no formato "data = {'year': 1888, 'month': 5}". OpenDataBio lida com datas incompletas, consulte o [Modelo de dados incompletos](/docs/concepts/auxiliary-objects/#incomplete-date). Pelo menos `year` é **obrigatório**.
* `notes` - qualquer anotação para o Indivíduo;

Campos de localidade (uma ou mais localidades podem ser registradas para 1 indivíduo). Os campos possíveis são:
  * `location` - o nome ou id do local do Indivíduo **obrigatório se a longitude e a latitude não forem informadas**
  * `latitude` e` longitude`- coordenadas geográficas em graus decimais; **obrigatório se location não for informado**
  * `altitude` - a elevação do local onde o Indivíduo foi observado (em metros acima do nível do mar);
  * `location_notes` - qualquer nota para a localidade do Indivíduo;
  * `location_date_time` - se diferente da data do indivíduo, uma data completa ou um valor de data + hora para a localidade do indivíduo. Obrigatório quando importando várias localidades.
  * `x` - se a localidade for do tipo Parcela (adm_level=100) ou Transecto (adm_level=101), a coordenada x do indivíduo na localidade;
  * `y` - se a localidade for do tipo Parcela (adm_level=100) ou Transecto (adm_level=101), a coordenada y do indivíduo na localidade;
  * `distance` - se o localidade for do tipo PONTO, uma distância para a posição do indivíduo em relação ao PONTO (em metros). Útil, com `angle` para, por exemplo, dados de Ponto Quadrante;
  * `angle` - se a localidade for do tipo PONTO, o azimute da posição do Indivíduo em relação à localidade;

Campos de identificação. A identificação taxonômica não é obrigatória, podendo ser informada de duas formas distintas: (1) `identificação-própria` - o indivíduo pode ter sua própria identificação; ou (2) `identificação-dependente` a identificação é  a mesma que a de outro indivíduo (por exemplo, quando múltiplos indivíduos tem sua identificação associada a uma única coleta)
  1. Os seguintes campos podem ser usados ​​para a `identificação-própria` do indivíduo:
    * `taxon = mixed` - nome ou id do táxon identificado, por ex. 'Ocotea delicata' ou seu id
    * `identifier = mixed` - 'id', 'abbreviation', 'full_name' ou 'email' do responsável pela identificação taxonômica;
    * `identification_date` ou` identification_date_year`, `identification_date_month`, e/ou` identification_date_day` - completo ou incompleto. Se vazio, a data de coleta do indivíduo será usada.
    * `modificador` - nome ou número do modificador do nome da identificação. Valores possíveis 's.s.' = 1, 's.l.' = 2, 'cf.' = 3, 'aff.' = 4, 'vel aff.' = 5, o padrão é 0 (nenhum).
    * `identification_notes` - quaisquer notas de identificação
    * `identification_based_on_biocollection` - o nome ou id da Biocoleção se a identificação for baseada em um espécime de referência depositado em uma Biocoleção (registrada ou não na base)
    * `identification_based_on_biocollection_id` - somente preencher se` identification_based_on_biocollection` estiver preenchido;
  2. Para uma `identificação-dependente`
    * `identification_individual` - id ou nome completo (organimsID) do Indivíduo que possui a identificação.

Se o Indivíduo tiver Vouchers **com os mesmos** Coletores, Data e Número do Coletor (Tag) que os do Indivíduo, os seguintes campos e opções permitem armazenar os vouchers durante a importação do registro do Indivíduo (como alternativa, você pode importar o voucher após a importação indivíduos através do [Voucher EndPoint](/docs/api/post-data/#post-vouchers). Os vouchers para o indivíduo podem ser informados de duas maneiras:

1. Ou como campos separados:
  * `biocollection` - uma string com um único valor ou uma lista de valores separados por vírgulas. Os valores podem ser os valores `id` ou` acronym` do [Modelo de Biolcoleções](/docs/concepts/data-access/#biocollection). Ex: "{'biocollection': 'INPA; MO; NY'}" ou "{'biocollection': '1,10,20'}";
  * `biocollection_number` - uma string com um único valor ou uma lista de valores separados por vírgulas com o BiocollectionNumber para o voucher individual. Se for uma lista, então deve ter o mesmo número de valores que `biocollection`;
  * `biocollection_type` - Uma string com um único valor de código numérico ou uma lista de valores separados por vírgulas para [Tipo Nomenclatural](/docs/api/quick-reference/#nomenclature-types) para os Vouchers. O valor padrão é 0 (não é um tipo).
2. Como um único campo `biocollection` contendo uma matriz com cada elemento tendo os campos acima para uma única BioColeção:
  "{
    { 'biocollection_code' : 'INPA', 'biocollection_number' : 59786, 'biocollection_type' : 0},
    { 'biocollection_code' : 'MG', 'biocollection_number' : 34567, 'biocollection_type' : 0}
  }"

***

## POST Individual-locations

O endpoint `individual-locations` permite a importação de várias localidades para Indivíduos previamente registrados. Projetado para importação de múltiplas ocorrências de organismos que se movem.

Possible fields are:
  * `individual` - o id do Indivíduo **obrigatório**
  * `location` - o nome ou id do local do Indivíduo **obrigatório se a longitude e a latitude não forem informadas**
  * `latitude` e` longitude`- coordenadas geográficas em graus decimais; **obrigatório se location não for informado**
  * `altitude` - a elevação do local onde o Indivíduo foi observado (em metros acima do nível do mar);
  * `location_notes` - qualquer nota para a localidade do Indivíduo;
  * `location_date_time` - se diferente da data do indivíduo, uma data completa ou um valor de data + hora para a localidade do indivíduo. Obrigatório quando importando várias localidades.
  * `x` - se a localidade for do tipo Parcela (adm_level=100) ou Transecto (adm_level=101), a coordenada x do indivíduo na localidade;
  * `y` - se a localidade for do tipo Parcela (adm_level=100) ou Transecto (adm_level=101), a coordenada y do indivíduo na localidade;  

***
## POST Locations

> O endpoint `locations` interage com o modelo [Localidades](/docs/concepts/core-objects/#locations). Use para importar novas localidades.

{{<alert color = "warning" title = "Atenção">}}
As localidades são armazenadas com um relacionamento pai-filho, garantindo validações e facilitando  consultas. Os pais serão adivinhados usando a geometria da localidade sendo importada. Se o pai não for informado, o local importado deve estar completamente contido por um pai registrado (a função sql [ST_WITHIN](https://postgis.net/docs/ST_Within.html) será usada para detectar o pai). No entanto, se um pai for informado, a importação também pode testar se a geometria se ajusta a uma versão em buffer da geometria da localidade pai, ignorando assim a sobreposição de geometrias secundárias e bordas compartilhadas. Os países podem ser importados sem relações com os pais. Qualquer outro local deve ser registrado em pelo menos um 'país' como pai. Se o registro for marinho e estiver fora do polígono de um país registrado, um argumento 'ismarine' deve ser indicado para aceitar o relacionamento não espacial com a localidade.

Subparcelas  é a única situação em que a geometria não é necessária. Se não for informado, a geometria será calculada com base na geometria da Parcela pai e com as coordenadas startx e starty da subparcela.
{{</ alert>}}

Certifique-se de que sua projeção geométrica seja **EPSG: 4326** **WGS84**. Use este padrão!

Campos POST disponíveis:
* `nome` - o nome da localidade - **obrigatório** (pai + nome deve ser único no banco de dados)
* `adm_level` - deve ser numérico, veja [aqui](/docs/api/get-data/#locations-endpoint) - **obrigatório**
* geometria **obrigatório**, pode usar:
  * `geom` para uma representação WKT da geometria, POLYGON, MULTIPOLYGON, POINT OU LINESTRING permitido;
  * `lat` e` long` para latitude e longitude em graus decimais (use números negativos para sul/oeste).
* `altitude` - em metros, se for o caso.
* `datum` - o padrão é 'EPSG: 4326-WGS 84' e você é fortemente encorajado a importar apenas dados nesta projeção. Você pode informar uma projeção diferente aqui;
* `parent` - o id ou o nome do local pai. A API detectará o pai com base na geometria informada e o *pai detectado tem prioridade* se for diferente do parent informado. No entanto, apenas quando os pais são informados, a validação também testará se sua localização cai dentro de uma versão em buffer do pai informado, permitindo importar locais que têm um relacionamento pai-filho, mas suas fronteiras se sobrepõem de alguma forma (fronteiras compartilhadas ou diferenças no georreferenciamento) ;
* quando a localização é PARCELA (`adm_level = 100`), os campos opcionais são:
  * `x` e` y` para as dimensões da parcela em metros (define as coordenadas cartesianas)
  * `startx` e` starty` para a posição inicial de uma sub-parcela em relação à localização da parcela pai;
* `notes` - qualquer nota que você deseja adicionar à sua localidade
* `azimuth` - aplica-se apenas para Parcelas e Transectos e **quando registrados** com uma geometria de tipo PONTO - o azimute será usado para construir a geometria. Para Parcelas, as coordenadas do ponto referem-se ao vértice 0,0 do polígono que será construído no sentido horário a partir do ponto informado, do azimute e a dimensão y. Para transectos, as coordenadas do ponto informado são o ponto inicial e uma linha será construída usando este azimute e a dimensão x.
* `ismarine` - para permitir a importação de registros de localidade que não se enquadram em nenhum registro de localidade pai, você pode adicionar `ismarine=1`. Observe, no entanto, que isso permite importar locais sem validação espacial. Use somente se sua localização for realmente um local marítimo que esteja fora da fronteira de qualquer país registrado.

**alternatively**: you may just submit a single column named `geojson` containing a Feature record, with its geometry and having as 'properties' at least tags `name` and `adm_level` (or `admin_level`). See [geojson.org](https://geojson.org/). This is usefull, for example, to import country political boundaries (https://osm-boundaries.com/).

**alternativamente**: você pode apenas enviar uma única coluna chamada `geojson` contendo um registro Feature, com sua geometria e tendo como 'propriedades' pelo menos as tags `name` e `adm_level` (ou` admin_level`). Consulte [geojson.org](https://geojson.org/). Isso é útil, por exemplo, para importar as fronteiras políticas e administrativas de um país usando (https://osm-boundaries.com/).

***

## POST Measurements

O endpoint `measurements` permite importar [medições](/docs/concepts/trait-objects/#measurement).

Os seguintes campos são permitidos para importar medições
* `dataset = number` - o id do Conjunto de Dados onde a medição deve ser colocada **obrigatório**
* `data = AAAA-MM-DD` - a data de observação para a medição, deve ser passada como AAAA-MM-DD **obrigatório**
* `object_type = string` - 'Individual','Location','Taxon' or 'Voucher', ou seja, o objeto ao qual a medição pertence **obrigatório**
* `object_type = number` - o id do objeto medido, seja (individuals.id, locations.id, taxons.id, vouchers.id) **obrigatório**
* `person = number or string` seja o 'id', 'abbreviation', 'full_name' ou 'e-mail' da pessoa que mediu o objeto **obrigatório**
* `trait_id = número ou string` - o id ou export_name para a variável medida. **obrigatório**
* `value = número, lista de strings` - isso dependerá do tipo de variável  **obrigatório**, opcional para variável de tipo LINK**
* `link_id = number` - o id do objeto vinculado para uma variável do tipo Link **obrigatório se o tipo de característica for Link**
* `bibreference = number` - o id ou bibkey de uma referência para a medição. Deve ser usado quando a medição foi tirada de uma publicação
* `notes` - qualquer nota que você desejar. Este é um local útil para armazenar informações relacionadas à medição. Por exemplo, ao medir 3 folhas de um voucher, você pode indicar aqui a qual folha a medição pertence, folha1, folha2, etc. permitindo vincular medidas de diferentes variáveis por este campo.
* `duplicated` - por padrão, a API de importação evitará medições duplicadas para a mesma `variável+objeto+data`; especificar `duplicated = 1` permitirá a importação de medições duplicadas nessas condições.

***
## POST Persons

O endpoint `persons` interage com a tabela [Person](/docs/concepts/auxiliary-objects/#person).

Os seguintes campos são permitidos ao importar pessoas usando a API:
- `full_name` - nome completo da pessoa, **obrigatório**
- `abreviação` - nome abreviado, conforme usado pela pessoa nas publicações, como coletor, etc. (se deixado em branco, uma abreviação padrão será gerada usando o atributo full_name - a abreviação deve ser única dentro de uma instalação OpenDataBio);
- `email` - um endereço de e-mail,
- `instituição` - a que instituição esta pessoa está associada;
- `biocollection` - nome ou acrônimo da BioColeção à qual esta pessoa está associada (se for um taxonomista, por exemplo)


***
## POST Taxons

Use para importar novos [nomes taxonômicos](/docs/concepts/core-objects/#taxon).


{{< alert color="warning" >}}
1. O POST API requer APENAS o **nome completo** do táxon a ser importado, ou seja, para espécies ou infra-espécies, o nome completo deve ser informado, por exemplo, *Ocotea guianensis* ou *Licaria cannela aremeniaca*, sem indicação dos ranks.
1. O script validará o nome, recuperando as informações necessárias restantes através de serviços de API de repositórios nomenclaturais. Ele pesquisará GBIF, Tropicos.org, Zoobank e se encontrar recuperará os dados do nome, os ids nesses repositórios e também o caminho de classificação completo até Reino. Se o esta busca indicar que o nome é um sinônimo, os nomes válidos e aceitos para o seu nome serão também detectados.E tudo isso será importando na base de dados juntamente com seu nome. Se seu nome for reconhecido com sinônimo, ele será inserido como nome `invalid`. O caminho da classificação hierárquica será importado até que um Taxon pai já registrado seja encontrado. Note, no entanto, que você pode não querer a classificação hierárquica, ou querer uma diferente. Neste caso indique `level` e `parent`.
1. Portanto, a menos que você esteja tentando importar nomes não publicados, apenas envie o parâmetro de `name` da lista abaixo.
{{< /alert >}}

Campos possíveis:
* `name` - nome completo do táxon **obrigatório**, por exemplo "Ocotea floribunda" ou "Pagamea plicata glabrescens"
* `level` - pode ser o id numérico ou uma string que descreve o taxonRank **recomendado para nomes não publicados**
* `parent` - o nome completo ou id do pai do táxon - **nota** - se você informar um pai válido e o sistema detectar um pai diferente através da API aos repositórios nomenclaturais, será dada preferência ao pai informado; **obrigatório para nomes não publicados**
* `bibreference` - a referência bibliográfica em que o táxon foi publicado;
* `author` - o nome do autor do táxon;
* `author_id` ou` person` - o nome da pessoa registrada, abreviatura, e-mail ou id, representando o autor de nomes não publicados - **obrigatório para nomes não publicados**
* `valid` - booleano, verdadeiro se o nome do táxon for válido; 0 ou 1
* `mobot` - id do Tropicos.org para este táxon
* `ipni` - id do IPNI para este táxon
* `mycobank` - id do MycoBank para este táxon
* `zoobank` - id do ZOOBANK para este táxon
* `gbif` - nubKey de GBIF para este táxon

***
## POST Traits

> Ao inserir algumas variáveis, **recomendamos fortemente** que você insira variáveis uma a uma usando o formulário da Interface Web, o que reduz a chance de duplicar definições de variáveis, que são objetos compartilhados com todos os usuários da base e devem ter uma definição clara.

{{< alert color="primary" >}}
O endpoint `traits` interage com o modelo [Variáveis](/docs/concepts/trait-objects/#trait). O método POST permite que você importe variáveis em lote para o banco de dados e é projetado para transferir Ontologias para o OpenDataBio de outros sistemas
  1. Conforme observado na descrição do modelo [Variáveis](/docs/concepts/trait-objects/#trait), é importante verificar se uma Variável que você precisa já não está cadastrada e evitar a multiplicação de características redundantes. A interface no navegador facilita esse processo. Por meio da API, OpenDataBio verifica por duplicações apenas em `export_name`, que deve ser único no banco de dados. Observe, no entanto, que as Variáveis também devem ser o mais específicas possível para anotações detalhadas de metadados.
  1. Variáveis usam [Traduções de Usuário](/docs/concepts/auxiliary-objects/#user_translation) para nomes e descrições, permitindo traduções nos idiomas disponíveis na sua instalação.
{{< /alert >}}

Campos permitidos para POST traits:
* `export_name = string` - um nome curto para a variável, que será usado durante as exportações de dados e nos formulários na interface. **export_name deve ser único na base de dados** e não devem ter tradução. Nomes de exportação curtos no estilo  [CamelCase]​​(https://en.wikipedia.org/wiki/Camel_case) são recomendados. Evite sinais diacríticos (acentos), caracteres especiais, pontos e até mesmo espaços em branco **obrigatório**
* `type = number` - um código numérico que especifica o tipo de Variável. Veja o modelo [Variáveis](/docs/concepts/trait-objects/#trait) para uma lista completa. **obrigatório**
* `objects = list` - uma lista dos [Core objects](/docs/concepts/core-objects) para os quais a variável pode ser medida. Os valores possíveis são 'Individual', 'Voucher', 'Location' e/ou 'Taxon', singular e sensível a maiúsculas e minúsculas. Ex:  "{'object': 'Individual,Voucher'}"; **obrigatório**
* `name = json` - veja abaixo sobre traduções; **obrigatório**
* `description = json` - veja abaixo sobre traduções; **obrigatório**
Campos específicos para tipos de variáveis:
  * `unit = string` - exigido apenas para variáveis quantitativas (a unidade de medida). recomendado o uso de padrões de inglês e palavras completas, por exemplo, ('meters' em vez de apenas 'm' ou 'metros')
  * `range_min = number` - opcional para variáveis quantitativas. especifique o valor mínimo permitido para uma [medição](/docs/concepts/trait-objects/#measurement).
  * `range_max = number` - opcional para quantitativo. valor máximo permitido para a variável.
  * `categories = json` - **obrigatório para variáveis categóricas e ordinais**; veja abaixo sobre traduções
  * `wavenumber_min` e` wavenumber_max` - **obrigatório para variáveis espectrais** = número de onda mínimo e máximo dentro do qual os valores de absorbância ou refletância de 'value_length' estão igualmente distribuídos. Pode ser informado em `range_min` e` range_max`, prioridade para o prefixo wavenumber sobre range se ambos informados.
  * `value_length` - **obrigatório para características espectrais** = número de valores no espectro
  * `link_type`- **obrigatório para características de link** - a classe do tipo de link, nome completo ou nome de base: por exemplo. 'Taxon' ou 'App\Models\Taxon'.
* `bibreference = number` - o id de uma [Referência Bibliográfica](/docs/concepts/auxiliary-objects/#bibreference) a partir do qual a definição da variável e/ou categorias  estão baseadas.

#### Categorias e Traduções

* Os campos `name`,` description` devem ter a seguinte estrutura para incorporar [Traduções do usuário](/docs/concepts/auxiliary-objects/#user_translation). Eles devem ser uma lista com o idioma como 'chaves'. Por exemplo, um campo `name` pode receber a seguinte informação:
  * usando o código de idioma como chaves: `{" en ":" Diâmetro na altura do peito "," pt ":" Diâmetro a Altura do Peito "}`
  * ou usando os ids de idioma como chaves: `{" 1 ":" Diâmetro à altura do peito "," 2 ":" Diâmetro a Altura do Peito "}`.
  * ou usando os nomes dos idiomas como chaves: `{" English ":" Diameter at Breast Height "," Portuguese ":" Diâmetro a Altura do Peito "}`.
* O campo `categories` deve incluir para cada categoria + ordem + idioma os seguintes campos:
  * `lang = mixed` - o id, código ou nome do idioma da tradução, **obrigatório**
  * `name = string` - o nome da categoria no idioma **obrigatório** (name + rank + lang deve ser único)
  * `rank = number` - a ordem para variáveis ordinais, semi-quantitativas; para não ordinal, o `rank` também é importante, mas apenas para indicar a mesma categoria em todos os idiomas **obrigatório**
  * `description = string` - opcional para categorias, uma definição da categoria.
  * Exemplo:

  ```JSON
    [
      {"lang":"en","rank":1,"name":"small","description":"smaller than 1 cm"},
      {"lang":"pt","rank":1,"name":"pequeno","description":"menor que 1 cm"}
      {"lang":"en","rank":1,"name":"big","description":"bigger than 10 cm"},
      {"lang":"pt","rank":1,"name":"grande","description":"maior que 10 cm"},
    ]
  ```

* Idiomas válidos podem ser obtidos pelo [Language API](/docs/api/get-data/#languages-endpoint).

***
## POST Vouchers

> O endpoint `vouchers` interage com a tabela [Vouchers](/docs/concepts/core-objects/#vouchers). Note que `vouchers` podem também ser importados diretamente através da importação de registros de [indivíduos](/docs/api/post-data/post-individuals). O POST vouchers requer que informações de indivíduos já esteja cadastradas.

Os seguintes campos são permitidos na importação de dados:
* `individual = mixed` - o id numérico ou organismID do indivíduo ao qual o voucher pertence **obrigatório**;
* `biocollection = mixed` - o id, nome ou acrônimo de uma [BioColeção registrada](/docs/concepts/data-access/#biocollection) a que o Voucher pertence **obrigatório**;
* `biocollection_type = mixed` - o nome ou código numérico de [tipo de nomenclatural](/docs/api/quick-reference /#nomenclature-types), se for o caso. O padrão é 0, ou seja, não é um tipo nomenclatural.
* `biocollection_number = mixed` - o código alfanumérico do voucher na BioColeção (número de tombo ou outro código de uso local);
* `number = string` - o **número do coletor** principal - somente se diferente do valor *tag* do indivíduo ao qual o voucher pertence;
* `collector = mixed` - ids ou abreviações de pessoas. Quando vários valores são informados, o primeiro é o *coletor principal*. Informe apenas se for diferente da lista de coletores do Indivíduo associado.
* `date = AAAA-MM-DD ou array` - necessário apenas se, com coletor e número, for diferente dos valores do Indivíduo associado. A data pode ser um [Modelo Incompleto](/docs/concepts/auxiliary-objects/#incomplete-date).
* `dataset = number` - herda o Conjunto de Dados ao qual o indivíduo pertence, mas você pode fornecer um Conjunto de Dados diferente se preferir
* `notes = string` - qualquer nota de texto para adicionar ao voucher.
