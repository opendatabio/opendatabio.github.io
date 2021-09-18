---
title: "Contribution Guidelines"
linkTitle: "Contribution Guidelines"
weight: 6
description: >
  How to contribute to OpenDataBio?
---

## Report bugs & suggest improvements


Post an issue on one of the GitHub repositories below, depending on the issue.  

Before posting, check if an open issue does already contains what you want to report, ask or propose.

Tag your issue with one or more <a href="https://github.com/opendatabio/opendatabio/labels" target="_blank">appropriate labels</a>.

<br>
<a class="btn btn btn-success mr-3 mb-3 text-dark" href="https://github.com/opendatabio/opendatabio/issues" target="_blank" >
<i class="fab fa-github ml-2 "></i> Issues for the main repository</a>
<br>
<a class="btn btn btn-primary  mr-3 mb-4 text-dark" href="https://github.com/opendatabio/opendatabio-r/issues" target="_blank" >
	<i class="fab fa-github ml-2 "></i> Issues for the R package</a>
  <br>  
<a class="btn btn btn-warning  mr-3 mb-4 text-dark" href="https://github.com/opendatabio/opendatabio.github.io/issues" target="_blank" >
  	<i class="fab fa-github ml-2 "></i> Issues for this documentation site</a>

## Collaborate with development, language translations and docs

> We expect this project to grow collaboratively, required for its development and use in the long term. Therefore, developer collaborators are welcome to help fixing and improving OpenDataBio. The <a href="https://github.com/opendatabio/opendatabio/labels" target="_blank">issues list</a> is a place to start to know what is needed.

The following guidelines are recommend if you want to collaborate:

1. Communicate with the [OpenDataBio repository maintainer](/docs/about) indicating which issues you want to work on and join the development team.
1. `Fork` the repository
1. Create a `branch` to commit your modifications or additions
1. When happy with results, make a pull request to ask the project maintainer to review your contribution and merge it to the repository. Consult [GitHub Help](https://help.github.com/articles/about-pull-requests/) for more information on using pull requests.

### Programming directives

1. Use the [docker installation](/docs/getting-started/docker-installatio.md) for development, which shared among all developers facilitates debug. The Laravel-Datatables library is incompatible with `php artisan serve`, so this command should not be used.
1. This software should adhere to Semantic Versioning, starting from version 0.1.0-alpha1. The companion R package and the Documentation (this site) should follow a similar versioning scheme. When changing version, a release tag must be created with the old version.
1. All variables and functions should be named in **English**, with entities and fields related to the database being named in the singular form. All tables (where appropriate) should have an "id" column, and foreign keys should reference the base table with "_id" suffix, except in cases of self-joins (such as "taxon.parent_id") or [polymorphic foreign keys](/docs/contribution-guidelines/#polymorphicrelationships). The id of each table has type INT and should be autoincrementing.
1. Use **laravel migration** class to add any modification to the database structure. Migration should include, if apply, management of existing data.
1. Use **camelCase** for methods (i.e. relationships) and **snake_case** for functions.
1. Document the code with comments and create documentation pages if necessary.
1. There should be a structure to store which **Plugins** are installed on a given database and which are the compatible system versions.
1. This system uses Laravel Mix to compile the SASS and JavaScript code used. If you add or modify these `npm run prod` after making any change to these files.

## Collaborate with the docs

We welcome [Tutorials](/docs/tutorials) for dealing with specific tasks.

To create a tutorial:

1. `Fork` the [documentation repository](https://github.com/opendatabio/opendatabio.github.io). When cloning this repository or a fork, include the submodule option to also get the included [Docsy](https://github.com/google/docsy) theme repository. You will need [Hugo](https://gohugo.io/) to run this site in your localhost.
1. Create a `branch` to commit your modifications or additions
1. Add your tutorial:
  - Create a folder within the `contents/{lang}/docs/Tutorials` using kebab-case for the folder name. Ex. `first-tutorial`
  - You may create a tutorial in single language, or on multiple languages. Just place it in the correct folder   
  - Within the created folder, create a file named `_index.md` and create the markdown content with your tutorial.
  - You may start copying the content of an existing tutorial.
1. When happy with results, make a pull request to ask the project maintainer to review your contribution and merge it to the repository. Consult [GitHub Help](https://help.github.com/articles/about-pull-requests/) for more information on using pull requests.


## Collaborate with translations

You may help with translations for the web-interface or the documentation site. If want to have a new language for your installation, share your translation, creating a pull request with the new files.

### New language for the web-interface:

1. fork and create a branch for the main repository
1. create a folder for the new language using the [ISO 639-1 Code](https://www.loc.gov/standards/iso639-2/php/code_list.php) within the `resources/lang` folder
    ```bash
    cd opendatabio
    cd resources/lang
    cp en es
    ```
1. translate all the values for all the variables within all the files in the new folder (may use google translation to start, just make sure variable names are not translated, otherwise, it will not work)
1. add language to array in `config/languages.php`
1. add language to database language table creating a laravel migration
1. make a pull request

### New language for the documentation site

1. fork and create a branch for the documentation repository
1. create a folder for the new language using the [ISO 639-1 Code](https://www.loc.gov/standards/iso639-2/php/code_list.php) within the `content` folder
    ```bash
    cd opendatabio.github.io
    cd contents
    cp pt es
    ```
1. check all files within the folder and translate where needed (may use google translation to start, just make sure to translate only what can be translated)
1. push to your branch and make a pull request to the main repository


<a name="polymorphicrelationships"></a>
## Polymorphic relations

Some of the foreign relations within OpenDataBio are mapped using [Polymorphic relations](https://laravel.com/docs/8/eloquent-relationships#polymorphic-relations). These are indicated in a model by having a field ending in `_id` and a field ending in `_type`. For instance, all Core-Objects may have [Measurements](/docs/concepts/trait-objects/#measurement), and these relationships are established in the Measurements table by the `measured_id` and the `measured_type`  columns, the first storing the related model unique `id`, the second the measured model class in strings like 'App\Models\Individual', 'App\Models\Voucher', 'App\Models\Taxon', 'App\Models\Location'.

<a name="erd-generator"></a>
## Data model images
Most figures for explaining the [data model](/docs/concepts) were generated using [Laravel ER Diagram Generator](https://github.com/beyondcode/laravel-er-diagram-generator), which allows to show all the methods implemented in each Class or Model and not only the table direct links:

To generate these figures a custom `php artisan` command was generated. These command is defined in file `app/Console/Commands/GenerateOdbErds.php`.

To update the figures follow the following steps:

* Figures are configure in the  `config/erd-generator-odb.php` file. There are many additional options for customizing the figures by changing or adding graphviz variables to the `config/erd-generator-base.php` file.
* The custom command is `php artisan odb:erd {$model}`, where model is the key of the arrays in `config/erd-generator-odb.php`, or the word "all", to regenerate all doc figures.
```bash
cd opendatabio
make ssh
php artisan odb:erd all
```
* Figures will be saved in `storage/app/public/dev-imgs`
* Copy the new images to the [documentation site](https://github.com/opendatabio/opendatabio.github.io). They need to be placed within  `contents/{lang}/concepts/{subfolder}` for all languages and in the respective sub-folders.
