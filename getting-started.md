---
title: "Getting started"
header-includes:
    <link rel="Metafacture Icon" type="image/x-icon" href="./img/metafacture-icon.png">
---

## Playground

The easiest way to get started with Metafacture is the Playground. Take a look at an [example](https://metafacture.org/playground/?example=encode-xml) and run it by pressing the !["Process"](img/process.png) button. Check out the other examples (first button, !["Load Examples"](img/load-exmples.png)) for different input sources, transformations, and output formats.

For commands available in the Flux, see [the Flux commands documentation](https://metafacture.org/metafacture-documentation/docs/flux/flux-commands.html).

For functions and usage of the Fix, see [the Fix functions](https://metafacture.org/metafacture-documentation/docs/fix/Fix-functions.html) and [cookbook](https://metafacture.org/metafacture-documentation/docs/fix/Fix-User-Guide.html#cookbook).

For a tutorial, see [metafacture-tutorial](https://metafacture.github.io/metafacture-tutorial/).

## Command line

Check if Java 11 or higher is installed with `java -version` in your terminal. If not, install Java 11 or higher.

To use Metafacture as a command-line tool, download the latest Metafacture release from our [releases page](https://github.com/metafacture/metafacture-core/releases).

Download metafacture-core-$VERSION-dist.tar.gz or the zip version and extract the archive to your choosen folder. In the folder you find the flux.bat and flux.sh

The code below assumes you moved the resulting folder to your home directory and renamed it to "metafacture".

Run a Flux workflow with:

`$ ./metafacture/flux.sh /path/to/your.flux` on Unix/Linux/Mac or
`$ ./metafacture/flux.bat /path/to/your.flux` on Windows.

To get started, you can export a workflow from the Playground (last button, !["Export Workflow"](img/export.png)).

To set up IDE support for editing your Flux and Fix files, see [the IDE extensions page](/ide-extensions.html).

## Using Metafacture as a Java library

If you want to use Metafacture in your own Java projects all you need is to add some dependencies to your project. As of Metafacture 5, the single metafacture-core package has been replaced with a number of domain-specific packages. You can find the list of packages on [Maven Central](https://search.maven.org/search?q=g:org.metafacture).

Alternatively, you can simply guess the package names from the top-level folders in the source code repository -- they are the same. 

For instance, if you want to use the `metafacture-io` library in your project, simply add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>org.metafacture</groupId>
    <artifactId>metafacture-io</artifactId>
    <version>$VERSION</version>
</dependency>
```

or if Gradle is your build tool of choice use:

```groovy
dependencies {
    implementation 'org.metafacture:metafacture-io:$VERSION'
}
```

To use Fix you would declare `metafix` instead of `metafacture-io` as in the example above. Since the beginning of Metafacture Fix is part of Metafacture Core and therefore also available via Maven Central.

Occasionally, we publish snapshot builds on [Sonatype OSS Repository](https://oss.sonatype.org/index.html#nexus-search;gav~org.metafacture~~~~~kw,versionexpand). The version number is derived from the branch name. Snapshot builds from the master branch always have the version `master-SNAPSHOT`. We also provide sometimes pre releases as github packages.

## Next steps

Get familar with [FLUX](https://metafacture.org/metafacture-documentation/docs/flux/Flux-User-Guide.html) and [FIX](https://metafacture.org/metafacture-documentation/docs/fix/Fix-User-Guide.html). And try out some Metafacture workflows.

If you plan to use Metafacture as a Java library or if you wish to add commands to Flux you should get familar with the [Framework](https://metafacture.org/metafacture-documentation/docs/fix/Fix-User-Guide.html).

For a complete introduction to Metafacture in German, check out the latest iteration of [our Metafacture Workshop](https://pad.lobid.org/p/2024-10-08-Bibliothekarische-Metadatenworkflows-mit-Metafacture-erstellen#/). 
For different use cases, e.g. using Metafacture as a library, using the Morph language, and more, see [our documentation collection](https://metafacture.org/metafacture-documentation/).
