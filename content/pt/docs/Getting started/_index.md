---
title: "Primeiros passos"
linkTitle: "Primeiros passos"
weight: 2
description: >
  Obtenha e instale o OpenDataBio
---

OpenDataBio é um web-software para Linux nas distribuições Debian, Ubuntu e Arch-Linux e pode ser implementado em qualquer máquina baseada em Linux. Não temos planos de suporte ao Windows, mas pode ser fácil de instalar em uma máquina Windows usando o Docker.

Opendatabio é escrito em [PHP](https://www.php.net) e desenvolvido com o [framework Laravel](https://laravel.com/). Requer um servidor web (apache ou nginx), PHP e um banco de dados SQL - testado apenas com [MySQL](https://www.mysql.com/) e [MariaDB](https://mariadb.org/) .

Você pode instalar o OpenDataBio facilmente usando os arquivos Docker incluídos na distribuição, mas esses arquivos docker fornecidos destinam-se apenas ao desenvolvimento e precisam de ajuste para implementar um site em produção.

Se você deseja testar o OpenDataBio, ajudar no desenvolvimento ou ou ter uma instalação individual no seu computador, siga a instalação do Docker.

----
## Prepare para instalação

<br>
<span class="btn  btn-danger mr-3 mb-4 text-dark">
<i class="fas fa-exclamation-triangle"></i> Não está pronto para produção
</span>
<br>

{{< github_button button="view" user="opendatabio" repo="opendatabio" large="true" >}}
<br>
<br>

1. Você pode solicitar uma [chave API Tropicos.org](https://services.tropicos.org/help?requestkey) para que o OpenDataBio possa recuperar dados taxonômicos do banco de dados Tropicos.org. Se não for fornecido, principalmente o serviço de nomenclatura do GBIF será usado;
1. OpenDataBio envia e-mails para usuários registrados, seja para informar sobre um [job](/docs/concepts/data-access/#user-job) que foi concluído, para enviar solicitações de dados para administradores de Conjuntos de Dados, ou para recuperação de senha. Você pode usar um e-mail do Google para isso, mas precisará alterar as opções de segurança da conta para permitir que o OpenDataBio use a conta para enviar e-mails (você precisa ativar a opção de `Acesso a aplicativos menos seguros` nas configurações da conta do gmail). Portanto, crie um endereço de e-mail dedicado para sua instalação. Verifique o arquivo "config/mail.php" para mais opções sobre como enviar e-mails.
