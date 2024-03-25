---
title: "Welcome"
header-includes:
    <link rel="Metafacture Icon" type="image/x-icon" href="./img/metafacture-icon.png">
---

Welcome to Metafacture.org!

Metafacture is a toolkit for processing semi-structured data with a focus on library metadata. It provides a versatile set of tools for reading, writing and transforming data. Metafacture can be used as a stand-alone application or as a Java library in other applications. The name Metafacture is a portmanteau of the words *meta*data and manu*facture*.

Metafacture includes a [large number of modules](https://github.com/metafacture/metafacture-documentation/blob/master/flux-commands.md) for operating on semi-structured data. These modules can be combined to build pipelines to perform complex metadata processing tasks. The pipelines can be constructed either in Java code or with the domain-specific language **Flux**. One of the core features of Metafacture is the **Metamorph** module. Metamorph is an xml-based language for specifying transformations of semi-structured data. It can be seamlessly integrated into Java code.

At its heart Metafacture is a framework for implementing modules for metadata processing. This makes Metafacture easily extendable with additional modules. The [plugins and tools page](https://github.com/metafacture/metafacture-core/wiki/Plugins-and-Tools) on the wiki shows supplementary packages and projects which extend Metafacture.

Originally, Metafacture was developed as part of the Culturegraph platform but it is developed independently now and used by others, too: [see who uses Metafacture](https://github.com/metafacture/metafacture-core/wiki/Who-uses-Metafacture).

Also [Metafacture Fix](https://github.com/metafacture/metafacture-fix), a non-xml Catmandu inspired language for specifying transformations, is being developed that is not stream-based but operates at record level.

Metafacture is maintained by the Team [Offene Infrastruktur by hbz](https://www.hbz-nrw.de/produkte/linked-open-data).
