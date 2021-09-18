# OpenDataBio Documentation

[OpenDataBio](https://opendatabio.github.io) is an opensource system designed to help researchers and organizations studying biodiversity in Tropical countries to collect, organize and serve biological data.

This repository contains the full documentation of OpenDataBio and uses the [Docsy](https://github.com/google/docsy) Hugo theme for technical documentation sites.

# How to contribute

See the [OpenDataBio Contribution guidelines](https://opendatabio.github.io/docs/contribution-guidelines).

When cloning this repository or a fork, include the submodule option to also get the included Docsy them repository:

```
git clone --recurse-submodules --depth 1 https://github.com/opendatabio/opendatabio.github.io.git
```

To run in your localhost:

1. Install Hugo. You need a [recent **extended** version](https://github.com/gohugoio/hugo/releases) of [Hugo](https://gohugo.io/) to do local builds and previews of sites that use Docsy. Make sure to get the `extended` Hugo version.
1. Then just run hugo server from within the site folder:

```
cd opendatabio.github.io
hugo serve
```
1. The doc site will be available locally at http://localhost:1313


# License

The documentation site is licensed for use under a GPLv3 license. See the LICENSE file provided.
