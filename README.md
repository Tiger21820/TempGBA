PokéGBA alpha version 3 (that means it may or may not work)

A Game Boy Advance emulator for the Supercard DSTWO.

Based on:
* gameplaySP (gpSP) 0.9 by Exophase, with help from various GBA developers
* gpSP Kai unofficial 3.3 by Takka
* The CATSFC GUI by ShadauxCat and Nebuleon (improving over BAGSFC)
  with language files contributed by the GBAtemp community for CATSFC

# Installing

(If you got the source code and want to compile it, see the `Compiling` section
 at the end of the file.)

To install the plugin to your storage card, copy `pokegba.plg`, `pokegba.ini`
and `pokegba.bmp` from the release archive to the card's `_dstwoplug`
directory. Then, copy the `POKEGBA` subdirectory to the root of the card.

# Improving game compatibility with game_config.txt

Included with this release is a file called game_config.txt. The file is the
same as the one included in gpSP Kai. It goes in the `POKEGBA` directory on
your storage card when you extract the release.

This file contains information to enhance game compatibility in two ways.
* Firstly, it tells PokéGBA about the save type used by a certain game, so that
  battery-backed saves may be read and written correctly.
* Secondly, it tells PokéGBA about idle loops in a certain game, so that it may
  stop emulating when the game reaches them. It runs the game faster.

game_config.txt receives updates from the community occasionally.
For the latest version, see:
<http://filetrip.net/nds-downloads/flashcart-files/latest-x-f12630.html>

# Cheats

There is currently no cheat support in PokéGBA, pending the discovery of a
large enough cheat pack in a decent user-editable format.

# Frame skipping

- - - - CUT HERE - - - - (Section invalid for now as frame skip doesn't work)

In the Video & audio menu, the **Frame skipping** option allows you to select
a number of frames to skip between rendered frames.

- - - - CUT HERE - - - -

# The font

The font used by PokéGBA is similar to the Pictochat font. To modify it,
see `source/font/README.txt`.

# Translations

Translations for PokéGBA may be submitted to the author(s) under many forms,
one of which is the Github pull request. To complete a translation, you will
need to do the following:

* Open `TEMPGBA/system/language.msg`.
* Copy what's between `STARTENGLISH` and `ENDENGLISH` and paste it at the end
  of the file.
* Change the tags. For example, if you want to translate to Japanese, the tags
  will become `STARTJAPANESE` and `ENDJAPANESE`.
* Translate each of the messages, using the lines starting with `#MSG_` as a
  guide to the context in which the messages will be used.

If you are not comfortable editing C code, or cannot compile PokéGBA after
changes, you may instead test your translation in the English block and submit
it. That allows you to look for message length issues and to align the option
names and values to your liking with spaces.

If you wish to also hook your language into the user interface, you will need
to do the following:

* Edit `source/nds/message.h`. Find `enum LANGUAGE` and add the name of your
  language there. For the example of Japanese, you would add this at the end of
  the list:
  ```
	,
	JAPANESE
  ```
* Still in `source/nds/message.h`, just below `enum LANGUAGE`, you will find
  `extern char* lang[` *some number* `]`. Add 1 to that number.
* Edit `source/nds/gui.c`. Find `char *lang[` *some number* `] =`.
  Add the name of your language, in the language itself. For the example of
  Japanese, you would add this at the end of the list:
  ```
	,
	"日本語"
  ```
* Still in `source/nds/gui.c`, find `char* language_options[]`, which is below
  the language names. Add an entry similar to the others, with the last number
  plus 1. For example, if the last entry is `, (char *) &lang[7]`, yours would
  be `, (char *) &lang[8]`.
* Still in `source/nds/gui.c`, find `case CHINESE_SIMPLIFIED`. Copy the lines
  starting at the `case` and ending with `break`, inclusively. Paste them
  before the `}`. Change the language name and tags. For the example of
  Japanese, you would use:
  ```
	case JAPANESE:
		strcpy(start, "STARTJAPANESE");
		strcpy(end, "ENDJAPANESE");
		break;
  ```

Compile again, copy the plugin and your new `language.msg` to your card
under `POKEGBA/system`, and you can now select your new language in PokéGBA!

# Compiling

Compiling PokéGBA is best done on Linux. Make sure you have access to a Linux
system to perform these steps.

## The DS2 SDK
To compile PokéGBA, you need to have the Supercard team's DS2 SDK.
The Makefile expects it at `/opt/ds2sdk`, but you can move it anywhere,
provided that you update the Makefile's `DS2SDKPATH` variable to point to it.

For best results, download version 0.13 of the DS2 SDK, which will have the
MIPS compiler (`gcc`), extract it to `/opt/ds2sdk`, follow the instructions,
then download version 1.2 of the DS2 SDK and extract its files into
`opt/ds2sdk`, overwriting version 0.13.

Additionally, you will need to add the updated `zlib`, DMA
(Direct Memory Access) and filesystem access routines provided by BassAceGold
and recompile `libds2a.a`. To do this:

> sudo rm -r /opt/ds2sdk/libsrc/{console,core,fs,key,zlib,Makefile} /opt/ds2sdk/include
> sudo cp -r sdk-modifications/{libsrc,include} /opt/ds2sdk
> sudo chmod -R 600 /opt/ds2sdk/{libsrc,include}
> sudo chmod -R a+rX /opt/ds2sdk/{libsrc,include}
> cd /opt/ds2sdk/libsrc
> sudo rm libds2a.a ../lib/libds2a.a
> sudo make

## The MIPS compiler (`gcc`)
You also need the MIPS compiler from the DS2 SDK.
The Makefile expects it at `/opt/mipsel-4.1.2-nopic`, but you can move it
anywhere, provided that you update the Makefile's `CROSS` variable to point to
it.

## Making the plugin
To make the plugin, `pokegba.plg`, use the `cd` command to change to the
directory containing your copy of the PokéGBA source, then type
`make clean; make`. `pokegba.plg` should appear in the same directory.
