---
title: "IDE Extensions"
header-includes:
    <link rel="Metafacture Icon" type="image/x-icon" href="./img/metafacture-icon.png">
---

## Overview
Here you will find different IDE extensions and plugins related to the [Metafacture project](https://metafacture.org) in order to support working with the framework. At the current state we provide VS Code / Codium Extensions for the Fix language and the Flux language. Both IDE extensions are aiming to support working with Fix and Flux by syntax highlighting, simple auto completion and validation of the Flux/Fix language.
As IDE extensions are based on the [Language Server Protocol (LSP)](https://microsoft.github.io/language-server-protocol/), extensions and plugins for other IDEs than VS Code / Codium can easily be added in the future.

## Setup a VS Code / Codium extension
There are different ways to install extensions for VS Code / Codium.

1. The first one is to install from a marketplace. If you use VS Code, the default marketplace is [Visual Studio Marketplace](https://marketplace.visualstudio.com/vscode), for VS Codium it's [Open VSX Registry](https://open-vsx.org/). All extensions which are presented here are published to both marketplaces. Use the **Search Extensions in Marketplace** search box to search for **metafacture** in the Extensions view.

2. The second way to install is to download a VSIX file and install from it. You can download the latest version of the VSIX files for [Fix](https://www.metafacture.org/ide-extensions/fix.vsix) and [Flux](https://www.metafacture.org/ide-extensions/flux.vsix). Use the **Install from VSIX** command in the Extensions view drop-down menu and point to the downloaded VSIX.


## Usage

### Flux

After installing the IDE extension to VS Code / Codium open a Flux file, for example [this one](https://gitlab.com/oersi/oersi-etl/-/blob/master/data/production/digill/digill-to-oersi.flux). It should look like this:

<img src="./img/digill-to-oersi_flux.JPG" alt="Flux editor" style="max-width:100%"/>

If you delete for example a necessary bracket you will see in the Problems section that the extension detects problems with the Flux file:

<img src="./img/digill-to-oersi_flux_problem.JPG" alt="Flux editor with problems" style="max-width:100%"/>

### Fix

After installing the IDE extension to VS Code / Codium open a Flux file, for example [this one](https://gitlab.com/oersi/oersi-etl/-/blob/master/data/production/digill/digill.fix). It should look like this:

<img src="./img/digill_fix.JPG" alt="Fix editor" style="max-width:100%"/>

If you remove a closing `end` you will see in the Problems section that the extension detects the missing `end`:

<img src="./img/digill_fix_problem.JPG" alt="Fix editor with problems" style="max-width:100%"/>

## GitHub

To report issues, see the code, and contribute to the projects, visit: [Fix Repository](https://github.com/metafacture/metafacture-fix), [Flux Repository](https://github.com/metafacture/metafacture-flux).
