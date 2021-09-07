---
title: "Getting Started"
linkTitle: "Getting Started"
weight: 2
description: >
  Getting and installing OpenDataBio
---

OpenDataBio is a web-based software supported in Debian, Ubuntu and Arch-Linux distributions of Linux and may be implemented in any Linux based machine. We have no plans for Windows support, but it may be easy to install in a windows machine using Docker.

Opendatabio is written in [PHP](https://www.php.net) and developed with the [Laravel framework](https://laravel.com/). It requires a web server (apache or nginx), PHP and a SQL database -- tested only with [MySQL](https://www.mysql.com/) and [MariaDB](https://mariadb.org/).

You may install OpenDataBio easily using the Docker files included in the distribution, but these docker files provided are meant for development only and required tuning to deploy a production site.

If you just want to test OpenDataBio in your computer, follow the Docker Installation.

----
## Prep for installation

<span class="btn  btn-danger mr-3 mb-4 text-dark">
<i class="fas fa-exclamation-triangle"></i> Not yet ready for production
</span>

<br>
{{< github_button button="view" user="opendatabio" repo="opendatabio" large="true" >}}
<br>
<br>

1. You may want to request a [Tropicos.org API key](https://services.tropicos.org/help?requestkey) for OpenDataBio to be able to retrieve taxonomic data from the Tropicos.org database. If not provided, mainly the GBIF nomenclatural service will be used;
1. OpenDataBio sends emails to registered users, either to inform about a [Job](/docs/concepts/data-access/#user-job) that has finished, to send data requests to dataset administrators or for password recovery. You may use a Google Email for this, but will need to change the account security options to allow OpenDataBio to use the account to send emails (you need to turn **on** the `Less secure app access` option in the Gmail My Account Page). Therefore, create a dedicated email address for your installation. Check the "config/mail.php" file for more options on how to send e-mails.
