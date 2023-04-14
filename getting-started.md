---
title: "Getting started"
header-includes:
    <link rel="Metafacture Icon" type="image/x-icon" href="/img/metafacture-icon.png">
---

## Playground

The easiest way to get started with Metafacture is the Playground. Take a look at the [first example](https://metafacture.org/playground/?flux=PG_DATA%0A%7Cas-lines%0A%7Cdecode-formeta%0A%7Cfix%0A%7Cencode-xml%28rootTag%3D%22collection%22%29%0A%7Cprint%0A%3B&fix=move_field%28_id%2C+id%29%0Amove_field%28a%2C+title%29%0Apaste%28author%2C+b.v%2C+b.n%2C+%27~aus%27%2C+c%29%0Aretain%28id%2C+title%2C+author%29&data=1%7Ba%3A+Faust%2C+b+%7Bn%3A+Goethe%2C+v%3A+JW%7D%2C+c%3A+Weimar%7D%0A2%7Ba%3A+R%C3%A4uber%2C+b+%7Bn%3A+Schiller%2C+v%3A+F%7D%2C+c%3A+Weimar%7D&active-editor=fix) and run it by pressing the !["Process"](img/process.png) button. Check out the other examples (first button, !["Load Examples"](img/load-exmples.png)) for different input sources, transformations, and output formats.

For commands available in the Flux, see [the Flux commands documentation](https://github.com/metafacture/metafacture-documentation/blob/master/flux-commands.md).

For functions and usage of the Fix, see [the Fix functions and cookbook](https://github.com/metafacture/metafacture-fix#functions-and-cookbook).

## Command line

To use Metafacture as a command-line tool, download the latest metafix-runner from our [releases page](https://github.com/metafacture/metafacture-fix/releases). Extract the downloaded archive and change into the newly created directory (e.g. `cd metafacture-runner-0.4.0`). Run a Flux workflow with:

`$ ./bin/metafix-runner /path/to/your.flux` on Unix/Linux/Mac or
`$ ./bin/metafix-runner.bat /path/to/your.flux` on Windows.

To get started, you can export a workflow from the Playground (last button, !["Export Workflow"](img/export.png)).

To set up IDE support for editing your Flux and Fix files, see [the IDE extensions page](/ide-extensions/index.html).

## Next steps

For a complete introduction to Metafacture in German, check out the latest iteration of [our Metafacture Workshop](https://slides.lobid.org/2022-12-metafacture-workshop/#/). For different use cases, e.g. using Metafacture as a library, using the Morph language, and more, see [our documentation collection](https://github.com/metafacture/metafacture-documentation).
