---
title: "Instalação padrão"
linkTitle: "Instalação padrão"
weight: 1
description: >
  Como instalar o OpenDataBio?
---
> Estas instruções são para uma instalação baseada em [apache](https://httpd.apache.org), mas podem ser facilmente ajustadas para funcionar com [nginx](https://www.nginx.com).

## Requisitos do servidor

1. A versão mínima do PHP é 7.4
1. O servidor da web pode ser [apache](https://httpd.apache.org) ou [nginx](https://www.nginx.com). Para nginx, verifique a configuração nos arquivos do docker para ajustar essas instruções, que são para o apache.
1. Requer um banco de dados SQL, [MySQL](https://www.mysql.com/) e [MariaDB](https://mariadb.org/) foram testados, mas também pode funcionar com Postgres. Testado com MYSQL.v8 e MariaDB.v15.1.
1. Extensões PHP necessárias 'openssl', 'pdo', 'pdo_mysql', 'mbstring', 'tokenizer', 'xlm', 'dom', 'gd', 'exif', 'bcmath'
1. [Pandoc](https://pandoc.org/) é usado para traduzir o código LaTeX usado nas referências bibliográficas. Não é necessário para a instalação, mas é sugerido para uma melhor experiência do usuário.
1. Requer [Supervisor](http://supervisord.org/), que é necessário para os [jobs de usuário](/docs/concepts/data-access/#user-job)

## Criar usuário dedicado

A maneira recomendada de instalar o OpenDataBio para produção é usando um **usuário de sistema dedicado**. Nestas instruções, esse usuário é **odbserver**.


## Baixar OpenDataBio

Faça login como seu `Usuário dedicado` e baixe ou clone este software para onde deseja instalá-lo.
Aqui assumimos que é `/home/odbserver/opendatabio` para que os arquivos de instalação residam neste diretório. Se este não for o seu caminho, altere abaixo sempre que aplicável.

{{< github_button button="view" user="opendatabio" repo="opendatabio" large="true" button_text="Baixar ou clonar o OpenDataBio" >}}

## Prepare o Servidor

Primeiro, instale os softwares  Apache, MySQL, PHP, Pandoc e Supervisor. Em um sistema Debian, você também precisa instalar algumas extensões PHP e ativá-las:

```bash
sudo apt-get install apache2 mysql-server php7.4 libapache2-mod-php7.4 php7.4-mysql \
		php7.4-cli pandoc php7.4-mbstring php7.4-xml php7.4-gd php7.4-bcmath php7.4-curl php7.4-zip\
		supervisor
     \
a2enmod php7.4
phpenmod mbstring
phpenmod xml
phpenmod dom
phpenmod gd
#To check if they are installed:
php -m | grep -E 'mbstring|cli|xml|gd|mysql|pandoc|supervisord|bcmath|pcntl|zip'
```

Adicione o seguinte à sua configuração do Apache.

* Mude `/home/odbserver/opendatabio` para o seu caminho (os arquivos devem estar acessíveis pelo apache)
* Você pode criar um novo arquivo na pasta sites-available: `/etc/apache2/sites-available/opendatabio.conf` e colocar o seguinte código nele.

```bash
<IfModule alias_module>
        Alias /opendatabio      /home/odbserver/opendatabio/public/
        Alias /fonts /home/odbserver/opendatabio/public/fonts
        Alias /images /home/odbserver/opendatabio/public/images
        <Directory "/home/odbserver/opendatabio/public">
                Require all granted
                AllowOverride All
        </Directory>
</IfModule>
```

Isso fará com que o Apache redirecione todas as solicitações de `/` para a pasta correta, e também permitirá que o arquivo `.htaccess` fornecido controle as regras de reescrita, de forma que os URLs sejam bonitos. Se desejar acessar o arquivo apontando o navegador para a raiz do servidor, adicione também a seguinte diretiva:

```bash
RedirectMatch ^/$ /
```

Configure seu arquivo **php.ini**. O instalador pode reclamar da falta de extensões do PHP, então lembre-se de ativá-las nos arquivos **cli** (`/etc/php/7.4/cli/php.ini` e web **ini** (`/etc/php/7.4/apache2/php.ini`) para PHP!

Atualize os valores para as seguintes variáveis:
```bash
Encontre os arquivos
php -i | grep 'Configuration File'

Mudar:
	memory_limit should be at least 512M
	post_max_size should be at least 30M
	upload_max_filesize should be at least 30M

```
Algo como:

```bash
[PHP]
allow_url_fopen=1
memory_limit = 512M

post_max_size = 100M
upload_max_filesize = 100M

```

Habilite os módulos Apache 'mod_rewrite' e 'mod_alias' e reinicie o servidor:

```bash
sudo a2enmod rewrite
sudo a2ensite
sudo systemctl restart apache2.service
```

## Mysql Charset e Collation

1. Você deve adicionar o seguinte ao seu arquivo de configuração do SQL (mariadb.cnf ou my.cnf), ou seja, o conjunto de caracteres e o agrupamento que você escolher para sua instalação devem corresponder aos do `config/database.php`

```bash
[mysqld]
character-set-client-handshake = FALSE  #without this, there is no effect of the init_connect
collation-server      = utf8mb4_unicode_ci
init-connect          = "SET NAMES utf8mb4 COLLATE utf8mb4_unicode_ci"
character-set-server  = utf8mb4
log-bin-trust-function-creators = 1
sort_buffer_size = 4294967295  #this is needed for geometry (bug in mysql:8)


[mariadb]
max_allowed_packet=100M
innodb_log_file_size=300M  #no use for mysql

```

2. Se estiver usando MariaDB e você ainda tiver problemas do tipo **#1267 Illegal mix of collations**, então [verifique aqui](https://github.com/phpmyadmin/phpmyadmin/issues/15463) sobre como consertar isso.

## Configurar  o supervisord

Configure o Supervisor, que é necessário para trabalhos. Crie um nome de arquivo **opendatabio-worker.conf** na pasta de configuração do Supervisor `/etc/supervisor/ conf.d/opendatabio-worker.conf` com o seguinte conteúdo, ajustando o caminho conforme a sua instalação:

```bash
;--------------
[program:opendatabio-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /home/odbserver/opendatabio/artisan queue:work --sleep=3 --tries=1 --timeout=0
autostart=true
autorestart=true
user=odbserver
numprocs=8
redirect_stderr=true
stdout_logfile=/home/odbserver/opendatabio/storage/logs/supervisor.log
;--------------
```

## Permissões de arquivos e pastas

* As pastas  `storage` e `bootstrap/cache` precisam ter permissão de escrita para o usuário do servidor (geralmente www-data). Defina `0775` para esses diretórios.
* [Este link](https://linuxhint.com/how-to-set-up-file-permissions-for-laravel) mostra diferentes métodos de definir permissões para um aplicativo Laravel.

Este é o método recomendado:

```bash
cd /home/odbserver

#defia as permissões tanto par ao seu usuário (aqui odbserver) como para o do apache
sudo chown -R odbserver:www-data opendatabio
sudo find ./opendatabio -type f -exec chmod 644 {} \;
sudo find ./opendatabio -type d -exec chmod 775 {} \;  

cd /home/odbserver/opendatabio
sudo chgrp -R www-data storage bootstrap/cache
sudo chmod -R ug+rwx storage bootstrap/cache

```

### Instale OpenDataBio

1. Muitas distribuições Linux (Ubuntu e Debian) têm arquivos **php.ini diferentes** para a interface de linha de comando e para o Apache. Recomenda-se usar o arquivo de configuração do Apache ao executar o script de instalação, para que ele possa apontar corretamente as extensões ou configurações ausentes. Para fazer isso, encontre o caminho correto para o arquivo **.ini** e exporte-o **antes de usar o comando de instalação `php install` **.

    Por exemplo,
    ```bash
    export PHPRC=/etc/php/7.4/apache2/php.ini
    ```

2. O script de instalação baixará o gerenciador de dependências [Composer](https://getcomposer.org/) e todas as bibliotecas PHP necessárias listadas no arquivo `composer.json`. No entanto, se o seu servidor estiver atrás de um proxy, você deve instalar e configurar o Composer independentemente. Implementamos a configuração do PROXY, mas não a estamos mais usando e não testamos corretamente (se você precisar de ajustes, coloque um issue no Github).

3. O script solicitará opções de configuração, que são armazenadas no arquivo de ambiente `.env` na pasta raiz do aplicativo.

    Você pode, opcionalmente, configurar este arquivo antes de executar o instalador:

    - Crie um arquivo `.env` com o conteúdo do` cp .env.example .env` fornecido
    - Leia os comentários neste arquivo e ajuste de acordo

4. **Execute o instalador**:

```bash
cd /home/odbserver/opendatabio
php install
```
5. **Seed data** - o script irá pergunar se você quer instalar Localidades e Taxons distribuídos com  aplicativo. Esses dados são específicos de cada versão do OpenDataBio. Ver [as notas das versões no repositório desses dados](https://github.com/opendatabio/data).

{{< alert color="success" title="Pronto para usar">}}
Se o script de instalação for concluído com sucesso, você está pronto para prosseguir! Aponte seu navegador para `http://localhost/opendatabio`. As migrações de banco de dados incluem uma conta de administrador, com login `admin@example.org` e senha` password1`. Altere a senha após a instalação.
{{< /alert >}}


## Problemas de instalação

Existem inúmeras maneiras possíveis de instalar o aplicativo, mas podem envolver mais etapas e configurações.

* se o navegador retornar **500|SERVER ERROR** , você deve olhar para o último **error** em `storage/logs/laravel.log`. Se você tiver **ERROR: No application encryption key has been specified** execute:

```bash
chave artesanal php: gerar
php artisan config: cache
```
* Se você receber o erro **failed to open stream: Connection timed out** durante a execução do instalador, isso indica uma configuração incorreta do seu roteamento IPv6. A correção mais fácil é desabilitar o roteamento IPv6 no servidor.
* Se você receber erros durante alimentação aleatória do banco de dados, você pode tentar remover
o banco de dados inteiramente e reconstruí-lo. Claro, não execute isso em uma instalação de produção.

```bash
php artisan migrate: fresh
```

## Configurações pós-instalação

* Se seus [Jobs](/docs/data-access/user-jobs) de importação/exportação não estão sendo processados, certifique-se de que o Supervisor esteja executando `systemctl start supervisord && systemctl enable supervisord` e verifique os arquivos de log em` storage/logs/supervisor.log`.
* Você pode alterar várias variáveis ​​de configuração para o aplicativo. O mais importante deles provavelmente está definido pelo instalador, mas há outras variáveis em `.env` e  no arquivo `config/app.php` que você pode alterar. Em particular, você pode querer alterar as configurações de idioma, fuso horário e e-mail. Execute `php artisan config: cache` após atualizar os arquivos de configuração.
* Para impedir que os rastreadores do mecanismo de pesquisa indexem seu banco de dados, adicione o seguinte ao seu "robots.txt" na pasta raiz do servidor (no Debian, /var/www/html):

```bash
User-agent: *
Disallow: /
```
* As pastas `storage` e` bootstrap/cache` devem ser graváveis ​​pelo usuário do Apache (geralmente www-data). Veja [este link](https://linuxhint.com/how-to-set-up-file-permissions-for-laravel/) para um exemplo de como fazer isso. Defina a permissão `0775` para esses diretórios.

## Armazenamento e backups

Você pode alterar as configurações de armazenamento em `config/filesystem.php`, onde pode definir o armazenamento baseado em nuvem, que pode ser necessário se muitos usuários enviarem arquivos de mídia, exigindo muito espaço em disco.

1. **Downloads de dados** são colocados em fila como [Jobs](/docs/data-access/user-job) e um arquivo é gravado em uma pasta temporária,sendo excluído quando o trabalho é excluído pelo usuário. Esta pasta é definida como `download disk` no arquivo de configuração filesystem.php, que aponta para` storage/app/public/downloads`. Apagar esses arquivos temporários depende dos usuários, portanto, um trabalho de limpeza do cron pode ser aconselhável para implementar em sua instalação;
2. **Arquivos de mídia** são armazenados por padrão no `media disk`, que coloca os arquivos na pasta` storage/app/ public/media`;
3. Para configuração regular **crie** ambos os diretórios `storage/app/public/downloads` e` storage/app/public/media` com permissões graváveis ​​pelo usuário do servidor
4. Lembre-se de incluir a pasta de mídia em um plano de backup;
