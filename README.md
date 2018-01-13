# Gothic Mod Build Tool  
[![GitHub release](https://img.shields.io/github/release/szmyk/gmbt.svg)](https://github.com/Szmyk/gmbt/releases/latest) [![License](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/Szmyk/gmbt/blob/master/LICENSE) [![Codacy Badge](https://api.codacy.com/project/badge/Grade/12d93d0465b14dc284444a78062b5688)](https://www.codacy.com/app/Szmyk/gmbt?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=Szmyk/gmbt&amp;utm_campaign=Badge_Grade) [![Github Releases](https://img.shields.io/github/downloads/szmyk/gmbt/total.svg)](https://github.com/Szmyk/gmbt/releases/latest)
> *Gothic Mod Build Tool* is a simple tool designed to help in testing and building Gothic and Gothic 2 Night of the Raven mods.

This project was developed primarily for the purpose of assisting the SoulFire Team with the development of [The History of Khorinis].

## Table of Contents

* [How does it work?](#how-does-it-work)
* [Status](#status)
* [Download](#download)
* [Installation & Requirements](#installation--requirements)
* [Configuration](#configuration)
    * [Example](#example)
* [Usage](#usage)
    *  [Common parameters](#common-parameters)
    *  [Verb commands](#verb-commands)
    *  [Examples](#examples)
* [Example project](#example-project)
* [License](#license)
* [Used software](#used-software)
* [Used libraries](#used-libraries)
* [Contributing / issues / questions / contact](#contributing)

# How does it work?

Let's start with some background: the Gothic Mod Build Tool is kind of a breakthrough in Gothic modding, because it is one of the few successful attempts to create a build system which fully automates the process that was previously done manually, every modder had to manually compile assets like textures, meshes and animations and send them to their co-modders. Now, working with version control systems is possible, because each modder has the same version of assets at the same time and at any time can launch the game without need to build a *.mod* and not run into errors or discrepancies due to a lack of or mismatching assets.

This tool serves two very important purposes, to merge and to compile everything. It launches the Gothic game executable and compiles assets ingame.

## Modes

There are 3 modes of use:

* **Quick test** - merges assets directories, compiles the necessary assets to run the game, and launches the game. Not everything is compiled, so lag/stuttering can occur because of compiling textures, animations and 3D models "on the fly", in game. Used mainly to check if scripts are parsing when you are not using IDE/syntax checker. It could also be used if a full test is completed.
* **Full test** - compiles everything. This takes more time, but then you can play without problems like lag and stuttering.
* **Make VDF** - compiles everything and builds a *.mod*.

## Speed

On a mid-range PC with an HDD, the VDF build of a huge addon [The History of Khorinis] with around 800 MB of textures, 50 MB of animations and 3D models and no sounds, takes about 9 - 10 minutes. Similar time with a full test (subtract about a half minute of packing the *.mod*).

# Status

The most important features of the tool are finalized, but of course you use it at your own risk. The tool has not yet been thoroughly tested.

# Download

| Latest release | Pre-release 		| Development [(dev)]
|:-------------: | :-------------: 	| :-------------:
[![GitHub release](https://img.shields.io/github/release/szmyk/gmbt.svg)](https://github.com/Szmyk/gmbt/releases/latest) | [![GitHub (pre-)release](https://img.shields.io/github/release/szmyk/gmbt/all.svg)](https://github.com/Szmyk/gmbt/releases/latest)  |[![Build status (dev)](https://ci.appveyor.com/api/projects/status/0h4avwoh684c3tg2/branch/dev?svg=true)](https://ci.appveyor.com/project/Szmyk/gmbt/branch/dev/artifacts) 

# Installation & Requirements

* [.NET Framework 4.7](https://www.microsoft.com/en-us/download/details.aspx?id=55170) is required to run the tool.

* A clean installation of vanilla Gothic or Gothic 2 NotR on your PC. You must have a **COMPLETELY** clean copy of game, with no mods, textures packs and other such.

* [Gothic 1 Player Kit - v1.08k](https://www.worldofgothic.de/dl/download_34.htm) or [Gothic 2 Player Kit 2.6f](https://www.worldofgothic.de/dl/download_168.htm).

* Of course, you can also install [SystemPack](https://forum.worldofplayers.de/forum/threads/1340357-Release-Gothic-Ă‚Ëť-Ă‚â€”-SystemPack-%28ENG-DEU%29) if you have problems with the game on your PC.

After installation, you can run Gothic **ONLY** via GMBT. Of course, you can use eg. Spacer, but you have to complete a full test before (the scripts have to be compiled because Spacer needs eg. `GOTHIC.DAT` and `CAMERA.DAT`).

Next you have to [configure paths](#configuration) and run the tool with the command you want ([usage guide](#usage)).

# Configuration

You have to configure a [YAML] config:

* **gothicRoot** - _string_
    > Path to game root directory, eg. relative path (`..\..`) or absolute (`C:\Program Files\JoWood\Gothic 2 Gold Edition`)
* **modFiles**  - _structure_
    * **assets**  - _strings list_
        > Paths to assets directories which have to be placed in `_work/Data` directories. You have to prepare right structure inside these directories (same as in `_work/Data`: _\<dir\>\Anims_, _\<dir\>\Scripts_ and so on).
    * **exclude** - _strings list_
        > Exclude files from merging. Only files paths, not directories and wildcarts.
    * **defaultWorld**  - _string_
        > Name (not path) of ZEN, eg. _VADUZWORLD.ZEN_
* **modVdf**  - _structure_
    *   **output**  - _string_
        > Path to save *.mod* file.
    *   **comment**  - _string_
        > VDF volume comment.
        >
        > Available special characters:
        >  -  _%%N_ - new line
        >  -  _%%D_ - date and time in UTC
    * **include** - _strings list_
        >  Include some files or directories (wildcarts enabled) to VDF. Path's root is game root, eg. `_work\Data\Scripts\Content\*.d`
    * **exclude** - _strings list_
        >  Exclude some files or directories (wildcarts enabled) from VDF. Path's root is game root, eg. `_work\Data\Worlds\Test\*.zen`
*  **gothicIniOverrides**  - _dictionary_
    > Keys of GOTHIC.INI you want to override when running test or build.
    >
    > Syntax: _['section.key', 'value']_ or _'section.key': 'target'_, np. '`GAME.playLogoVideos' : '0'`

*  **install**  - _dictionary_
    > Optional files you want to install.
    >
    > Syntax: _[source, target]_ or _source: target_

### Example

Below is an example structure and configurations used in [The History of Khorinis] project. Also, the same structure you can see in the [example project](#example-project) I have prepared.

Our developers have to clone modification repository to `_Work` directory, so their local repository is located in `_Work/TheHistoryOfKhorinis`. We have a complex structure for our files. There are four directories:
* **mdk** - there are eg. original scripts, animations (if needed eg. to compile some new animations)
* **mdk-overrides** - there are overrides of original assets to maintain transparency and organization
* **mod** - own new assets, scripts, music and so on. There are only completely new files. Overrides of originals are in the `mdk-overrides`
* **thirdparty** - some resources from thirdparty libraries and projects on which we have license to use

We have got this config in root of the local repository (`_Work/TheHistoryOfKhorinis/build.yml`).

```
gothicRoot: ..\..

modFiles:
  assets:
    - mdk
    - mdk-overrides
    - thirdparty
    - mod

  defaultWorld: VADUZWORLD.ZEN

modVdf:
  output:  ..\..\Data\ModVDF\DK.mod
  comment: Gothic 2 - The History of Khorinis (%%D)%%N(C) 2018 SoulFire

  exclude:
    - _work\Data\Worlds\DK_SUBZENS\*

gothicIniOverrides:
  - 'GAME.playLogoVideos' : '0'
  - 'SKY_OUTDOOR.zSkyDome' : '1'
```

As you can see, there are only relative paths because this file is in our remote Git repository and every collaborator does not have to configure everything by themselves. And this is the main idea of this tool, to enable easy collaboration in version control systems and large teams.

# Usage

At this moment the only way to use the tool is command line interface. GUI application is planned, but [below](#examples) you can find simple examples of Windows Batch files.

## Common parameters

| Parameter 								| Description 																																| Default
| ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ---------
| `-C <path>, --config=<path>`				| Path of a config file. </br> Guide how to configure this file is [here](#configuration). 													| `.gmbt.yml` in working directory
| `--texturecompile=<normal\|quick>`		| Mode of textures compile.																													| _normal_
| `--noupdatesubtitles`						| Do not update (convert to OU.csl) of dialogues subtitles. 																				| N/A
| `--show-compiling-assets`					| Print all compiling by game assets in the console.																						| N/A
| `-L, --lang`								| Set language of console output.																											| Control Panel -> Regional Settings
| `--help`									| Print short descriptions of parameters.																									| N/A

## Verb commands

### `test`

> Starts test.

| Parameter 									| Description 																														| Default
| --------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------
| `-F, --full`                           		| Full test mode. Information about modes is [here](#modes). 																		| N/A
| `--merge=<none\|all\|scripts\|worlds\|sounds>`| Merge option. <br/> Enter eg. `scripts` if you just want to debug scripts and you do not want to copy all assets every time. Also, nice option to use is `scripts, worlds` if you want to debug some changes only related to scripts and worlds | _all_
| `-W, --world=<zen>` 					 		| Run game in a specific world. 																									| Set in [config file](#configuration)
| `--windowed` 						 			| Run game in window. 																												| N/A
| `--noaudio` 					     			| Run game without any audio. 																										| N/A
| `--zspy=<none\|low\|medium\|high>` 			| Level of zSpy logging.																											| _none_
| `--maxfps=<number>` 				 			| Max frames per second.																											| _60_
| `--ingametime=<hh:mm>`						| Ingame time. <br/>Syntax: **hour:minute**, eg. _15:59_. 																			| _08:00_
| `--nodx11` 							 		| If [D3D11-Renderer for Gothic] is installed, this command allow you to off this wrapper. 											| N/A
| `--nomenu` 									| Run game without menu showing (start new game immediately). 																		| N/A
| `-R, --reinstall`								| Reinstall before start.																											| N/A

### `build`

> Starts a *.mod* build.

| Parameter 		| Description								    | Default
| ----------------- | --------------------------------------------- | ------------------------------------
|`-O, --output`  	| Path of VDF volume (`.mod`) output. 			| Set in [config file](#configuration)
|`--nopacksounds`  	| Do not to pack sounds (WAVs) to mod package.  | N/A

## Examples

Example Batch scripts are in [example project].

Below are some examples I am using developing [The History of Khorinis] project:

* **GMBT_QuickTest.bat**

    `gmbt test --windowed --noaudio`

* **GMBT_QuickTest_ScriptsDebug.bat**

  You can run something like this if you are debbuging scripts only and you do not want to copy all assets every time. Gothic is running windowed, without audio and without main menu.

    `gmbt test --windowed --noaudio --merge=scripts`

* **GMBT_QuickTest_ScriptsAndWorldsDebug.bat**

  Similar to previous with additional merging of worlds.

    `gmbt test --windowed --noaudio --merge=scripts, worlds`

* **GMBT_FullTest.bat**

  Full test. Gothic is running windowed.

    `gmbt test -F --windowed`

* **GMBT_BuildVDF.bat**

  Builds a *.mod*.

    `gmbt build`

# Example project

There is an [example repository] which uses this tool. There are some assets from [World of Gothic DE Modderdatenbank], just for the test. You could test some features there to get acquainted with the tool. The repository has the same structure of files as in [example configuration](#example).

# License

```
MIT License

Copyright (c) 2017 Szymon 'Szmyk' Zak <szymonszmykzak@gmail.com>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

# Used libraries 

* [**YamlDotNet**](https://github.com/aaubry/YamlDotNet) Copyright (c) 2008, 2009, 2010, 2011, 2012, 2013, 2014 Antoine Aubry and contributors
* [**I18N-Portable8**](https://github.com/xleon/I18N-Portable) Copyright (c) 2016 Diego Ponce de León
* [**NLog**](https://github.com/NLog/NLog) Copyright (c) 2004-2017 Jaroslaw Kowalski, Kim Christensen, Julian Verdurmen
* [**CommandLineParser**](https://github.com/commandlineparser/commandline) Copyright (c) 2005 - 2015 Giacomo Stelluti Scala & Contributors

Licenses and disclaimers are in the [ThirdPartyNotices.md](https://github.com/Szmyk/gmbt/tree/master/ThirdPartyNotices.md) file.

# Used software

* **GothicVDFS 2.6** Copyright (c) 2001-2003, Nico Bendlin
* **Virtual Disk File System (VDFS)** Copyright (c) 1994-2002, Peter Sabath / TRIACOM Software GmbH
* **DDS2ZTEX Converter 1.0** Copyright (c) 2005 Nico Bendlin
* **NVIDIA Legacy Texture Tools** Copyright 2007 NVIDIA Corporation
* **zSpy 2.05** Copyright (c) 1997-2000 Bert Speckels, Mad Scientists 1997
* **NSIS (Nullsoft Scriptable Install System)** Copyright (C) 1999-2018 Nullsoft and Contributors

Licenses and disclaimers are in the [Resources] directory.

<a id= "contributing"/>

# Contributing / issues / contact

* [![Contributing](https://img.shields.io/badge/contributing-guidelines-blue.svg)](https://github.com/Szmyk/gmbt/tree/master/.github/CONTRIBUTING.md)
* [![GitHub issues](https://img.shields.io/github/issues/szmyk/gmbt.svg)](https://github.com/Szmyk/gmbt/issues) [![GitHub closed issues](https://img.shields.io/github/issues-closed/szmyk/gmbt.svg)](https://github.com/Szmyk/gmbt/issues)
* [![Join the chat](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/gothic-mod-build-tool?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge) 

[Resources]: https://github.com/Szmyk/gmbt/tree/master/src/gmbt/Resources
[Reporting issues]: https://github.com/Szmyk/gmbt/issues
[Contributing]: https://github.com/Szmyk/gmbt/tree/master/.github/CONTRIBUTING.md
[example project]: https://github.com/Szmyk/gmbt-example-mod/tree/master/tools/build-tool
[The History of Khorinis]: http://thehistoryofkhorinis.com
[D3D11-Renderer for Gothic]: https://forum.worldofplayers.de/forum/threads/1441897-D3D11-Renderer-fÄ‚Ä˝r-Gothic-2-(alpha)-15
[(master)]: https://github.com/Szmyk/gmbt/tree/master
[(dev)]: https://github.com/Szmyk/gmbt/tree/dev
[YAML]: https://en.wikipedia.org/wiki/YAML
[GothicVDFS 2.6]: http://www.bendlins.de/nico/gothic2/GothicVDFS.zip
[tool made by Nico Bendlin]: http://www.bendlins.de/nico/gothic2/ztextools.zip
[NVIDIA Legacy Texture Tools]: https://developer.nvidia.com/legacy-texture-tools
[World of Gothic DE Modderdatenbank]: https://www.worldofgothic.de/?go=moddb
[example repository]: https://github.com/Szmyk/gmbt-example-mod