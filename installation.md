% Installation manual

## Getting started

You can either use Metafacture as a stand-alone application or include it as a Java library in your own projects.

### Metafacture as a stand-alone application

If you are only interested in running Flux scripts without doing any Java programming this is the way to go. The instructions assume that you are using a *nix-like shell.

1. Download the latest distribution package from the [metafacture-core/releases](https://github.com/metafacture/metafacture-core/releases) page. Make sure that you do download a distribution package and _not_ a source code package (the file name should include *-dist*).

2. Extract the downloaded archive:
   ```bash
   $ tar xzf metafacture-core-VERSION-dist.tar.gz
   ```
   This will create a new directory containing a ready-to-use metafacture distribution.
3. Change into the newly created directory:
   ```bash
   $ cd metafacture-core-VERSION
   ```
4. Run one of the example scripts:
   ```bash
   $ ./flux.sh examples/read/marc21/read-marc21.flux
   ```
   This example will print a number of marc21 records on standard out.

The _examples_ folder contains many more examples which provide a good starting point for learning metafacture. If you have any questions please join our [mailing list](http://lists.dnb.de/mailman/listinfo/metafacture) or use our issue-based discussion forum over at [metafacture-documentation](https://github.com/metafacture/metafacture-documentation).


### Using Metafacture as a Java libary

If you want use Metafacture in your own Java projects all you need to add some dependencies to your project. As of Metafacture 5 the single metafacture-core package has been replaced with a number of domain-specific packages. You can find the list of packages on [Maven Central](https://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.metafacture%22).

Alternatively, you can simply guess the package names from the top-level folders in the source code repository -- they are the same. For instance, if you want to use Metamorph in your project, simply add the following dependency to your `pom.xml`:

```xml
<dependency>
    <groupId>org.metafacture</groupId>
    <artifactId>metamorph</artifactId>
    <version>VERSION</version>
</dependency>
```

or if Gradle is your build tool of choice use:

```groovy
dependencies {
    implementation 'org.metafacture:metamorph:VERSION'
}
```

Our integration server automatically publishes successful builds of all branches as snapshot versions on [Sonatype OSS Repository](https://oss.sonatype.org/index.html#nexus-search;quick~metafacture). The version number is derived from the branch name. Snapshot builds from the master branch always have the version "master-SNAPSHOT".

<!--
TODO: Link to getting started tutorial
-->

## Building metafacture-core from source

Building metafacture-core from source is easy. All you need is git and JDK 8:

1. Clone the metafacture-core repository and change into the directory:

```bash
$ git clone https://github.com/metafacture/metafacture-core.git
$ cd metafacture-core
```

2. Invoke the gradle-wrapper to download Gradle and build metafacture-core (on Windows call `gradlew.bat install`):

```bash
$ ./gradlew install
```

The resulting distribution can be found in `metafacture-core/metafacture-runner/build/distributions/`.

See [Code Quality and Style](https://github.com/metafacture/metafacture-core/wiki/Code-Quality-and-Style) on the wiki for further information on the sources.

<!--
TODO: Include a link to a page which explains how to write plugins
-->

## Stay updated

For support and discussion join the [mailing list](http://lists.dnb.de/mailman/listinfo/metafacture).
