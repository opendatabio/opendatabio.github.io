---
title: "Instalação com Docker"
linkTitle: "Instalação com Docker"
weight: 2
description: >
  Como instalar usando Docker!
---

> A maneira mais fácil de instalar e executar o OpenDataBio é usando o [Docker](https://www.docker.com/) e os arquivos de configuração do docker fornecidos, que contêm todas as configurações necessárias para executar o ODB. Usa nginx e mysql e supervisor.

{{< alert color="warning" >}}Os arquivos Docker forneceidos são para teste, desenvolvimento e instalações locais{{< /alert >}}

## Arquivos Docker incluídos

```bash
laraverl-app/
----docker/*
----./env.docker
----docker-compose.yml
----Dockerfile
----Makefile
```

Eles foram adaptados [deste link](https://github.com/dimadeush/docker-nginx-php-laravel), onde você também encontra uma configuração de produção.

## Instalação

{{< github_button button="view" user="opendatabio" repo="opendatabio" large="true" button_text="Baixar ou clonar o OpenDataBio" >}}

1. Certifique-se de ter [Docker] (https://www.docker.com/) e [Docker-compose] (https://docs.docker.com/compose/install/) instalados em seu sistema operacional.
1. Verifique se o seu usuário está no [grupo docker](https://github.com/sindresorhus/guides/blob/main/docker-without-sudo.md) criado durante a instalação do docker;
1. Baixe ou clone o OpenDataBio em sua máquina;
1. Certifique-se de que o seu usuário é o proprietário da pasta e do conteúdo criados, caso contrário, altere o proprietário e o grupo recursivamente para o seu usuário
1. Entre no diretório criado `opendatabio`
1. Opcionalmente, pode editar e ajustar o nome do arquivo contendo as configurações de ambiente `.env.docker`
1. Para instalar localmente para desenvolvimento, basta ajustar as seguintes variáveis ​​no arquivo **Dockerfile**, que são necessárias para mapear os proprietários dos arquivos para um usuário do docker;
    1. `UID` o usuário numérico que você está logado e que é o dono de todos os arquivos e diretórios no diretório opendatabio (em geral 1000 ou 1001).
    1. `GDI` o grupo numérico ao qual o usuário pertence, geralmente o mesmo que UID.
1. O arquivo `Makefile` contém atalhos para os comandos _docker-compose_ usados ​​para construir os serviços configurados no` docker-compose.yml` e arquivos auxiliares na pasta `docker`.
1. Crie os contêineres do docker usando os atalhos (leia o Makefile para compreender os comandos)

```bash
make build
```
1. Inicie os serviços docker implementados

```bash
make start

```
1. Veja os contêineres e tente entrar no contêiner laravel

```bash
docker ps
make ssh #para entrar no container do app
make ssh-mysql #para entrar no container do mysql container onde você pode acessar a base de dados usando `mysql -uroot -p`
```
1. Instale as dependencias

```bash
make composer-install
```
1. Criar a base de dados usando Laravel Migration

```bash
make migrate
```
1. Você pode alimentar as tabelas Locations e Taxons usando o [seed data](https://github.com/opendatabio/data):
```bash
make ssh #entrar no container laravel
php seedodb
```

1. Se funcionou, então Opendatabio estará disponível em seu navegador [http::/localhost:8080](http::/localhost:8080).
1. O banco de dados estará disponível através do phpmyadmin em [http://localhost:8082/](http://localhost:8082/)
1. Faça login com o super-usuário `admin@example.org` e a senha` password1`
1. Configurações adicionais nesses arquivos são necessárias para um ambiente de produção e implantação;

## Persistência de dados

As containers criados pelo Docker podem ser excluídos e regerados sem perder os dados
As tabelas mysql são armazenadas em um _volume_, que se apagado irá excluir a base de dados completamente.

```bash
docker volume list
```

## Usando

Leia o conteúdo do arquivo _Makefile_

```bash
make stop
make start
make restart
docker ps
...
```

Se você tiver problemas e alterou os arquivos do docker, pode ser necessário reconstruir:

```bash
#apaga todas as imagens sem apagr a base de dados
docker system prune -a  #aceitar com Yes
make build
make start
```
