---
title: "Getting started"
header-includes:
    <link rel="Metafacture Icon" type="image/x-icon" href="./img/metafacture-icon.png">
---

## Playground

The easiest way to get started with Metafacture is the Playground. Take a look at an [example](https://metafacture.org/playground/?example=encode-xml) and run it by pressing the !["Process"](img/process.png) button. Check out the other examples (first button, !["Load Examples"](img/load-exmples.png)) for different input sources, transformations, and output formats.

For commands available in the Flux, see [the Flux commands documentation](https://github.com/metafacture/metafacture-documentation/blob/master/flux-commands.md).

For functions and usage of the Fix, see [the Fix functions and cookbook](https://github.com/metafacture/metafacture-documentation/blob/master/Fix-function-and-Cookbook.md).

For a tutorial, see [metafacture-tutorial](https://metafacture.github.io/metafacture-tutorial/).

## Command line

To use Metafacture as a command-line tool, download the latest metafix-runner from our [releases page](https://github.com/metafacture/metafacture-fix/releases). Extract the downloaded archive and change into the newly created directory (e.g. `cd metafacture-runner-0.4.0`). Run a Flux workflow with:

`$ ./bin/metafix-runner /path/to/your.flux` on Unix/Linux/Mac or
`$ ./bin/metafix-runner.bat /path/to/your.flux` on Windows.

To get started, you can export a workflow from the Playground (last button, !["Export Workflow"](img/export.png)).

To set up IDE support for editing your Flux and Fix files, see [the IDE extensions page](/ide-extensions/index.html).

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

To use Fix you would declare `metafix` instead of `metafacture-io` as in the example above. Note that `metafix` is not published to maven central but only to [github releases](https://github.com/metafacture/metafacture-fix/releases).

Occasionally, we publish snapshot builds on [Sonatype OSS Repository](https://oss.sonatype.org/index.html#nexus-search;gav~org.metafacture~~~~~kw,versionexpand). The version number is derived from the branch name. Snapshot builds from the master branch always have the version `master-SNAPSHOT`. We also provide sometimes pre releases as github packages.

## Next steps

Get familar with [FLUX](https://github.com/metafacture/metafacture-documentation/blob/master/Flux-User-Guide.md) and [FIX](https://github.com/metafacture/metafacture-documentation/blob/master/Fix-User-Guide.md). And try out some Metafacture workflows.

If you plan to use Metafacture as a Java library or if you wish to add commands to Flux you should get familar with the [Framework](https://github.com/metafacture/metafacture-documentation/blob/master/Framework-User-Guide.md).

For a complete introduction to Metafacture in German, check out the latest iteration of [our Metafacture Workshop](https://slides.lobid.org/2022-12-metafacture-workshop/#/). 
For different use cases, e.g. using Metafacture as a library, using the Morph language, and more, see [our documentation collection](https://github.com/metafacture/metafacture-documentation).
