---
title: "Como contribuir"
linkTitle: "Como contribuir"
weight: 6
description: >
  Como você pode contribuir com o OpenDataBio
---

## Reportar bugs e sugerir melhorias

Criar um `issue` em um dos repositórios GitHub abaixo, dependendo do problema.

Antes de postar, verifique se o que você quer relatar, perguntar ou propor já não está num `issue aberto`.

Identifique seu problema com uma ou mais <a href="https://github.com/opendatabio/opendatabio/labels" target="_blank">etiquetas</a>.

<br>
<a class="btn btn btn-success mr-3 mb-3 text-dark" href="https://github.com/opendatabio/opendatabio/issues" target="_blank" >
<i class="fab fa-github ml-2 "></i> Issues para software</a>
<br>
<a class="btn btn btn-primary  mr-3 mb-4 text-dark" href="https://github.com/opendatabio/opendatabio-r/issues" target="_blank" >
	<i class="fab fa-github ml-2 "></i> Issues para o pacote do R</a>
  <br>  
<a class="btn btn btn-warning  mr-3 mb-4 text-dark" href="https://github.com/opendatabio/opendatabio.github.io/issues" target="_blank" >
  	<i class="fab fa-github ml-2 "></i> Issues para este site de documentação</a>

## Colabore com o desenvolvimento, traduções de idiomas e documentos

> Esperamos que este projeto cresça de forma colaborativa, necessário para o seu desenvolvimento e utilização no longo prazo. Portanto, colaboradores são bem-vindos para ajudar a corrigir e melhorar o OpenDataBio. A <a href="https://github.com/opendatabio/opendatabio/labels" target="_blank">lista de problemas ou melhorias</a> é um lugar para começar a saber o que é necessário fazer. Você pode também contribuir com Tutorias, melhorando a documentação.

As seguintes diretrizes são recomendadas se você deseja colaborar:

1. Comunique-se com o [administrador do repositório OpenDataBio](/docs/about) indicando em quais questões deseja trabalhar e junte-se à equipe de desenvolvimento.
1. Faça um `Fork` do repositório
1. Boa prática criar um `branch` para guardar suas modificações ou adições
1. Quando estiver satisfeito com os resultados, faça uma solicitação de `pull-request` ao mantenedor do projeto para revisar sua contribuição e mesclá-la com o código do repositório. Consulte a [Ajuda do GitHub](https://help.github.com/articles/about-pull-requests/) para obter mais informações sobre pull requests.

### Diretivas de programação

1. Use a [instalação do docker](/docs/getting-started/docker-installatio.md) para desenvolvimento, que compartilhada entre todos os desenvolvedores facilita a depuração. A biblioteca Laravel-Datatables é incompatível com `php artisan serve`, então este comando não deve ser usado.
1. Este software deve aderir ao Controle de Versão Semântico, a partir da versão 0.1.0-alpha1. O pacote R complementar e a Documentação (este site) devem seguir um esquema de controle de versão semelhante. Ao alterar a versão, uma tag de lançamento (release) deve ser criada com a versão antiga.
1. Todas as variáveis ​​e funções devem ser nomeadas em **Inglês**, com as entidades e campos relacionados ao banco de dados sendo nomeados no singular. Todas as tabelas (quando apropriado) devem ter uma coluna "id" e as chaves estrangeiras devem fazer referência à tabela base com o sufixo "_id", exceto em casos de autojunções (como "taxon.parent_id") ou [chaves estrangeiras polimórficas](/docs/contribution-guidelines/#polymorphic-relationships). O id de cada tabela tem tipo INT e deve ser autoincrementado.
1. Use a classe **laravel migration** para adicionar qualquer modificação à estrutura do banco de dados. A migração deve incluir, se aplicável, a manipulação de dados existentes, permitindo upgrades.
1. Use **camelCase** para métodos (ou seja, relacionamentos) e **snake_case** para funções.
1. Documente o código com comentários e crie páginas de documentação neste site, se necessário.
1. Deve haver uma estrutura para armazenar quais **plugins** estão instalados em uma determinada versão do banco de dados quais são as versões de sistema compatíveis.
1. Este sistema usa o Laravel Mix para compilar o código SASS e JavaScript usado. Se você adicionar ou modificar esses arquivos, utilize `npm run prod` depois de fazer qualquer alteração nesses arquivos.

## Colabore com a documentação

[Tutoriais](/docs/tutorials) para lidar com tarefas específicas são bem vindos!

Para criar um tutorial:
1. `Fork` o [repositório de documentação](https://github.com/opendatabio/opendatabio.github.io). Ao clonar este repositório ou do seu fork inclua a opção de submódulo para obter também o repositório de tema [Docsy](https://github.com/google/docsy) incluído. Você precisará de [Hugo](https://gohugo.io/) para executar este site em seu localhost.
1. Crie um `branch` para confirmar suas modificações ou adições
1. Adicione seu tutorial:
  - Crie uma pasta dentro de `contents/{lang}/docs/Tutorials` usando kebab-case para o nome da pasta. Ex. `primeiro-tutorial`
  - Você pode criar um tutorial em um único idioma ou em vários idiomas. Basta colocá-lo na pasta correta
  - Dentro da pasta criada, crie um arquivo chamado `_index.md` e crie o conteúdo de markdown com seu tutorial.
  - Você pode começar copiando o conteúdo de um tutorial já incluído ou veja a documentação do [Docsy](https://github.com/google/docsy) 
1. Quando estiver satisfeito com os resultados, faça uma solicitação de pull para pedir ao mantenedor do projeto para revisar sua contribuição e mesclá-la com o repositório. Consulte a [Ajuda do GitHub](https://help.github.com/articles/about-pull-requests/) para obter mais informações sobre o uso de solicitações pull.

## Colabore com traduções

Você pode ajudar com as traduções da interface do aplicativo ou deste site com a documentação. Se quiser ter um novo idioma para sua instalação, compartilhe sua tradução, criando um **pull request** com os novos arquivos.

### Novo idioma para a interface da web:

1. faça um **fork** e crie um branch para o repositório principal
1. crie uma pasta para o novo idioma usando o [Código ISO 639-1](https://www.loc.gov/standards/iso639-2/php/code_list.php) dentro da pasta `resources/lang`

```bash
cd opendatabio
cd resources/lang
cp en es
```
1. traduza todos os valores para todas as variáveis ​​dentro de todos os arquivos na nova pasta (pode usar a tradução do google para começar, apenas certifique-se de que os nomes das variáveis ​​não sejam traduzidos, caso contrário, não funcionará).
1. adicione o idioma ao array em `config/languages.php`
1. adicionar o idioma à tabela de languages do banco de dados criando uma migração laravel
1. solicite um _pull request_

### Novo idioma para o site de documentação

1. faça um **fork** e crie um branch para o repositório de documentação
1. crie uma pasta para o novo idioma usando o [Código ISO 639-1](https://www.loc.gov/standards/iso639-2/php/code_list.php) dentro da pasta `content`

```
bash
cd opendatabio.github.io
cd content
cp pt es
```
1. verifique todos os arquivos dentro da pasta e traduza onde necessário (pode usar a tradução do google, apenas certifique-se de traduzir apenas o que pode ser traduzido)
1. Veja se funciona bem na sua máquina local (precisa installar Hugo e servir digitando `hugo serve` na pasta do site, que ficará visível pelo navegador no endereço http://localhost:1313/.
1. empurre para o seu branch e faça um _pull request_

<a name="polymorphic-relationships"></a>
## Relações polimóficas

Algumas das relações dentro da OpenDataBio são mapeadas usando [Relações polimórficas](https://laravel.com/docs/8/eloquent-relationships#polymorphic-relations). Elas são indicadas em um modelo por ter um campo terminando em `_id` e um campo terminando em `_type`. Por exemplo, todos os Objetos Centrais podem ter [Medições](/docs/concepts/trait-objects/#measurement), e essas relações são estabelecidas na tabela measurements pelas colunas `measured_id` e `measured_type`, o primeiro armazenando a id do modelo relacionado, o segundo é a classe do modelo medido em strings como 'App\Models\Individual', 'App\Models\Voucher', 'App\Models\Taxon', 'App\Models\Location'.

<a name="erd-generator"></a>
## Imagens do modelo conceitual
A maioria das figuras para explicar o [modelo de dados](/docs/concepts) foram geradas usando [Laravel ER Diagram Generator](https://github.com/beyondcode/laravel-er-diagram-generator), que permite mostrar todos os métodos implementados em cada modelo e não apenas os links diretos da tabela:

Para gerar essas figuras, um comando personalizado `php artisan` foi gerado. Esse commando está definido no arquivo `app/Console/Commands/GenerateOdbErds.php`.

Para atualizar as figuras siga os seguintes passos:

* As figuras são configuradas no arquivo `config/erd-generator-odb.php`. Existem muitas opções adicionais para personalizar as figuras alterando ou adicionando variáveis ​​graphviz ao arquivo `config/erd-generator-base.php`.
* O comando personalizado é `php artisan odb: erd {$ model}`, onde model é a chave dos arrays em `config / erd-generator-odb.php`, ou a palavra" all ", para regenerar todas as figuras doc.
`` `bash
cd opendatabio
fazer ssh
php artisan odb: erd all
`` `
* As figuras serão salvas em `storage / app / public / dev-imgs`
* Copie as novas imagens para a pasta do site de documentação. Eles precisam ser colocados em `contents / {lang} / concepts / {subfolder}` para todos os idiomas e nas respectivas subpastas.
