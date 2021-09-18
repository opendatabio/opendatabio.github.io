---
title: "Visão geral"
linkTitle: "Visão geral"
weight: 1
description: >
  O que é o OpenDataBio?
---

OpenDataBio é uma plataforma-web de código aberto projetada para ajudar pesquisadores e organizações que estudam a biodiversidade em regiões tropicais a coletar, armazenar, relacionar e servir dados. Ele é projetado para acomodar muitos tipos de dados usados ​​em ciências biológicas e suas relações, particularmente para estudos de ecologia e biodiversidade e serve como um repositório de dados que permite a usuários baixar ou solicitar dados de pesquisa bem organizados e documentados.

## Porque?

Estudos de biodiversidade freqüentemente requerem a integração de uma grande quantidade de dados, exigindo padronização para uso e compartilhamento. Pesquisadores também precisam gerenciar e manter os seus dados de forma contínua e para além de depositá-los de forma estática em repositórios públicos ou data papers.

O OpenDataBio foi desenhado com base na necessidade de organizar e integrar dados históricos e atuais coletados na região amazônica, levando em consideração as práticas de campo e os tipos de dados utilizados por ecologistas e taxonomistas.

O OpenDataBio visa facilitar a padronização e normalização dos dados, utilizando diferentes serviços disponíveis online, dando flexibilidade aos usuários e grupos de usuários, e criando os links necessários entre Localidades, Táxons, Indivíduos, Vouchers e as Medições e os Arquivos de Mídia associados a eles, ao mesmo tempo em que oferece acessibilidade aos dados por meio de um serviço de API próprio, facilitando a distribuição e análise dos dados.

## Características relevantes

1. **Variáveis ​​personalizadas** - flexibilidade para o usuário definir [variáveis](/docs/concepts/trait-objects) incluindo alguns casos especiais como variáveis Espectrais, para Cores e para relações. Essa variávies são compartilhadas entre usários e requerem definições extadas como metadados. [Medições](/docs/concepts/trait-objects/#measurement) para tais características podem ser registradas para [Indivíduos](/docs/concepts/core-objects/#individual), [Vouchers](/docs/concepts/core-objects/#voucher), [Taxons](/docs/concepts/core-objects/#taxon) ou [Localidades](/docs/concepts/core-objects/#location).
1. [Taxons](/docs/concepts/core-objects/#taxon) podem ser **nomes publicados ou não publicados** (por exemplo, um morfotipo), sinônimos ou nomes válidos e qualquer nó da árvore da vida pode ser armazenado. A inserção de táxons é verificada em diferentes bases nomenclaturais (*Tropicos, IPNI, MycoBank, ZOOBANK, GBIF*), minimizando sua busca por erros ortográficos, autores, e validação de sinônimos
1. [Localidades](/docs/concepts/core-objects/#location) são armazenados com suas **geometrias espaciais**, permitindo consultas espaciais. Localidades como **Parcelas** e **Transectos** podem ser definidos, facilitando a organização de dados de métodos comumente usados ​​em estudos de biodiversidade
1. **Controle de acesso aos dados** - os dados são organizados em [Conjuntos de dados](/docs/concepts/data-access/#dataset) que permite definir uma política de acesso (público, não público) e uma licença para distribuição de conjuntos de dados públicos, tornando-se uma publicação de dados dinâmicos, cuja versão é a data da última edição.
1. Diferentes grupos de pesquisa podem usar uma única instalação OpenDataBio, tendo total controle sobre a edição e acesso de seus dados de pesquisa particulares, enquanto compartilham bibliotecas comuns como Taxonomia, Localidades, Referências bibliográficas e Váriáveis.
1. **API para acessar dados programaticamente** - Ferramentas para exportação e importação de dados são fornecidas por meio de [serviços de API](/docs/api) junto com um cliente API na linguagem R, o [pacote OpenDataBio-R](https://github.com/opendatabio/opendatabio-r).
1. **Auditoria** - o [modelo de atividade](/docs/concepts/auxiliary-objects/#auditing) audita alterações em qualquer registro e downloads de conjuntos de dados completos, que são registrados para rastreamento.
1. Um coletor de dados móveis está planejado com [ODK](https://getodk.org/) ou [ODK-X](https://odk-x.org/)

## Saiba mais

* [Primeiros passos](/docs/getting-started/): instale o OpenDataBio
