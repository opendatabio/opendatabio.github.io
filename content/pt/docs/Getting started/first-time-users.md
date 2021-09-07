---
title: "Primeira viagem"
linkTitle: "Primeira viagem"
weight: 3
description: >
  Dicas para usuários de primeira viagem!
---

> OpenDataBio é um software para ser usado online. As instalações locais são para teste ou desenvolvimento, embora possam ser usadas para como ambiente local de produção de um único usuário.

## Funções do usuário

* Se você estiver instalando, o primeiro login para uma instalação do OpenDataBio deve ser feito com o usuário super-administrador padrão: `admin@example.org` e` password1`. Essas configurações devem ser alteradas, senão a instalação fica aberta à  qualquer pessoa lendo essas instruções.
* Quando um usuário se auto-cadastra numa instalação do OpenDataBio, ele não tem permissão de edição ou inserção de dados no banco de dados, ele apenas ganha um cadastro e acesso aos conjuntos de dados que são abertos apenas para `usuários registrados` e permite que o usuário faça downloads pela interface, os quais exigem login.
* Apenas **usuários completos** podem contribuir com dados
* E apenas **super-administradores** podem atribuir o papel de `usuário completo` para usuários registrados - diferentes instalações do OpenDataBio podem ter políticas diferentes sobre como você pode obter acesso de usuário completo.

Ver mais em [Usuários](/docs/concepts/data-access/#user).

## Prepare seu perfil de usuário-completo

1. Criar uma [Pessoa](/docs/concepts/auxiliary-objects/#person) com os seus dados e ligar ela ao seu perfil de usuário através das configurações. Assim, os formulários da interface utilizarão essa Pessoa como padrão, facilitando a entrada de dados.
1. Você precisará de pelo menos **1 conjunto de dados** para entrar seus dados
    1. isso não é necessário para importar dados de biblitecas compartilhadas, como Pessoas, Localidades, Taxons e Referências Bibliográficas ([saiba mais aqui](/docs/concepts/auxiliary-objects)).
    1. quando receber atribuição de usuário-completo, um [Conjunto de Dados](/docs/concepts/data-access/#dataset) de acesso restrito e [Projeto](/docs/concepts/data-access/#project) serão automaticamente criados para você com o nome de **Workspace SEU-NOME**. Você pode modificar as configurações desses objetos da forma que desejar.
1. Você poderá criar tantos projetos e conjuntos de dados que precisar. Entenda bem esses [Objetos de Controle de Acesso](/docs/concepts/data-access) do OpenDataBio.

## Entrando dados

Existem três maneiras principais de importar dados para o OpenDataBio:

1. Um por um por meio da interface web, usando um navegador.
1. Usando os [serviços OpenDataBio de POST API] (/docs/api):
    1. importar de um arquivo CSV, XLXS ou ODS usando a interface (Menu Importar)
    1. através do R, usando [OpenDataBio R package](https://github.com/opendatabio/opendatabio-r), que um cliente para essa API.
    1. criando o seu próprio cliente em outra linguagem
1. Ao usar os [serviços da API OpenDataBio] (/docs/api), você deve preparar seus dados ou arquivo para importação de acordo com as opções de campo do [verbo POST] (/docs/post-data) para o 'endpoint' específico que você está tentando importar. Verifique também a [referência rápida da API](/docs/quick-reference).

## Dicas para inserir dados

1. Se for inserir dados pela primeira vez, recomendamos que você utilize a interface pelo seu navegador e crie pelo menos um registro para cada objeto que atenda às suas necessidades. Em seguida, experimente as configurações de privacidade do seu espaço de trabalho [conjunto de dados](/docs/concepts/data-access/#dataset) e verifique se você pode acessar os dados quando estiver conectado ou não. Ou seja, entenda como os dados ficam organizados e veja como obtê-los.
1. Use [Conjunto de dados](/docs/concepts/data-access/#dataset) para um conjunto independente de dados que deve ser distribuído como um grupo. Os conjuntos de dados são **publicações dinâmicas**, têm autor, dados e título.
1. Embora o ODB tente minimizar a redundância, dar flexibilidade aos usuários tem um custo em algumas definições, como por exemplo, [Variáveis](/docs/concepts/trait-objects/#trait) e [Pessoas](/docs/concepts/objetos auxiliares/#person) que podem receber registros duplicados. Portanto, deve-se ter cuidado ao criar tais registros. Os administradores podem criar um **código de conduta** para os usuários de uma instalação ODB para minimizar tal redundância.
1. Siga uma ordem de importação de novos dados, a partir das bibliotecas de uso comum. Por exemplo, você deve primeiro registrar [Localidades](/docs/concepts/core-objects/#location), [Taxons](/docs/concepts/core-objects/#taxon), [Pessoas](/docs/concepts/auxiliary-objects/#person), [Variáveis](/docs/concepts/trait-objects/#trait) e qualquer outra biblioteca comum **antes de importar** [Indivíduos](/docs/concepts/core-objects/#individual) ou [Medições](/docs/concepts/trait-objects/#measurement)
1. OpenDataBio pode ser instalado com dados previamente organizados para Localidades do Brasil [unidades administrativas (Município, Estado, País), Unidades de Conservação federais e Terras Indígenas] e uma base da árvore da vida para Taxons complementada pela filogenia das plantas com sementes até o nível de Ordem conforme a árvore do
[Angiosperm Phylogeny WebSite, version 14](http://www.mobot.org/MOBOT/research/APweb/).
1. Não há **nenhuma necessidade** de importar localidades do tipo `POINT` para importar Indivíduos porque o ODB cria a localidade para você se informar latitude e longitude, e detectará para você a qual são as localidades registradas à qual o indivíduo pertence. Ou seja, ODB detecta as unidades administrativas (Município, Estados, Pais), as Unidades de Conservação e Terras Indígenas e também classes ambientais, dependendo das [Localidades](/docs/concepts/core-objects/#location) registradas na base de dados. Você pode usar a [API de localidades](/docs/api/get-data/#locations-endpoint) com o parâmetro `querytype` para validar previamente as coordenadas dos seus indivíduos.
1. Existem diferentes maneiras de criar localidades do tipo PARCELA e TRANSECTOS - saiba mais em [Localidades](/docs/concepts/core-objects/#location).
1. A criação de [taxons](/docs/api/post-data/#post-taxons) exige apenas a especificação de um `nome` - ODB pesquisará os serviços de nomenclatura para você, encontrará o nome, metadados e taxons pais ou taxons aceitos, se o seu nome for um sinônimo, e importará todos os eles, se necessário até encontrar na base algum taxon na hierarquia ancestral. Por exemplo, se informar _Ocotea guianensis_, mas apenas Laurales já está cadastrado na base, ODB irá buscar e cadastrar para você todo o caminho `Lauraceae >> Ocotea >> Ocotea guianensis`, com autoria, referencia bibliográfica e link ao repositório nomenclatural onde encontrou os dados. Se você estiver importando nomes publicados, basta informar este único atributo. Caso contrário, se o nome não for publicado, é necessário informar campos adicionais. Portanto, separe a importação em lote de nomes publicados e não publicados em dois conjuntos.
