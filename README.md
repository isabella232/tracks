# tracks

Tracks is a tool to rename audio files generated by [Waves Tracks Live][tracks_live] based on channel or output names from an [Avid VENUE™][avid_venue] (version 4.5.x or earlier) patch file.

[avid_venue]: http://avid.force.com/pkb/articles/en_US/Download/VENUE-Standalone-Software-Updates
[tracks_live]: https://www.waves.com/mixers-racks/tracks-live

## Overview

Waves Tracks is an invaluable tool for doing a multi-track recording during a live event. Unfortunately, once the event is over, getting those files into Pro Tools or another DAW can be a pain for several reasons.

- The file names by default have the format "Track XX-Y.wav" (XX=track number, and Y=session number), giving no indication of the original channel name.
- Tracks are sorted alphabetically by track number then session number, making it difficult to pull out all tracks relating to just a single session.
- The alphabetic sorting of 32 tracks looks like: 1 10 11 12 … 2 20 21 22 … 3 31 32 4 5. Dragging all the tracks sorted this way into Pro Tools requires manually resorting the tracks by hand.

This software is designed to solve the above problems.

- Files are renamed according to their original channel names, or the Pro Tools output patch names.
- Session numbers are placed before track numbers, which groups the sessions together.

## Project detail

[![GoDoc](https://godoc.org/github.com/kward/tracks?status.svg)](https://godoc.org/github.com/kward/tracks)
[![Travis CI](https://travis-ci.org/kward/tracks.png?branch=master)](https://travis-ci.org/kward/tracks)
[![GoCover](https://gocover.io/_badge/github.com/kward/tracks?0 "Code coverage.")](https://gocover.io/github.com/kward/tracks)
[![Go Report Card](https://goreportcard.com/badge/github.com/kward/tracks "Report card.")](https://goreportcard.com/report/github.com/kward/tracks)

The software is distributed as source code. To compile the code, the [Go programming language](https://golang.org/) must be installed, and the source code and relative libraries installed. See the [Installation](#installation) section below for the necessary steps.

:bulb: Waves Tracks Live is a heavily striped down version of the open source DAW software [Ardour](https://ardour.org/). If you are are on a budget, and need something that will do more than recording and playback, give Ardour a try.

## Usage
The example below will make a new copy of all files in a new location. You will need enough space in your destination location to hold these files.

:bulb: In the examples below, you will frequently see the character `~` used in paths. This character is a Unix shell alias for your home directory. On macOS (or Mac OS X), this is equivalent to your :house: folder, e.g. `/Users/kward`. On Windows 10 or Windows 7, it is very similar, e.g. `\Users\kward`.

### Saving sessions with Waves Tracks Live

Save your Tracks Live sessions as you normally would. The following screenshot shows that I have saved my session as `~/Music/Tracks Live/20170916 ICF Ladies Night`.

![Waves Tracks Live saved session](https://github.com/kward/tracks/raw/master/resources/Waves%20Tracks%20Live%20-%20Saved%20session.png)

### Exporting a patch list from VENUE

Use the VENUE Patchlist export function, or the Info export function, to write your channel information to a USB stick.

**Export patch list**

![Avid VENUE - Patchbay - Export patch list](https://github.com/kward/tracks/raw/master/resources/Avid%20VENUE%20-%20Patchbay%20-%20Export%20patch%20list.png)

**Export info**

![Avid VENUE - Options - System - Info](https://github.com/kward/tracks/raw/master/resources/Avid%20VENUE%20-%20Options%20-%20System%20-%20Info.png)

VENUE normally writes the patch file to the root of the USB stick with a filename like `Avid VENUE Patch List Sep 10 2017, 4-51 PM.html`. Feel free to copy, move, or rename that file to a location useful to you.

:bulb: Consider moving the file off the USB drive to the same folder as the final location of the renamed files, and give it the name of the session. E.g. `~/Music/Sessions/20170916 ICF Ladies Night.html`

Perform a test run of renaming all tracks. These steps must be done from the Terminal.

:bulb: If you aren't at your console, you can use the standalone VENUE software (Windows only) to export the channel information as well.

### Copy the session files

In this example, we will make a copy of all the files into a newly created `~/Music/Sessions/20170916 ICF Ladies Night Stems` folder. Once we are sure the files are copied as we like, the original `~/Music/Tracks Live/20170916 ICF Ladies Night` folder will be removed.

:warning: The examples below assume that the `tracks` binary was compiled according to the [Installation](#installation)

:bulb: Why add the term `Stems` to the end of the new folder? This isn't required, but when a Pro Tools session is created later, the best name for it will likely be `20170916 ICF Ladies Night`. If that folder already exists, Pro Tools will complain, and require a different name to be chosen. Adding `Stems` allows a Pro Tools folder to be created with the session name, while keeping the stems separate. It is definitely an option to create the Pro Tools session *before* doing these steps so that the files can be copied directly into the appropriate Pro Tools session folder.

:warning: If you chose to rename the files in place by not providing a `dest_dir` flag, it is **highly recommended** that you make a backup of your files before running the software.

Create the new folder. (This could also be done in Finder or Windows Explorer).

```console
$ mkdir "~/Music/Sessions/20170916 ICF Ladies Night Stems"
```

Do a dry run of copying the files. :memo: No files will be changed because the `--dry_run` flag was given.

```console
$ tracks \
  --dry_run \
  move \
    --src_dir "~/Music/Tracks Live/20170906 ICF Ladies Night/interchange/20170916 ICF Ladies Night/audiofiles" \
    --dest_dir "~/Music/Sessions/20170906 ICF Ladies Night Stems" \
    --patch_file "~/Music/Sessions/20170906 ICF Ladies Night.html"
```

Check the output produced. Below is some sample output (scroll to the right).

```text
Moving:
  "/Users/kward/Music/Tracks Live/20170906 ICF Ladies Night/interchange/20170906 ICF Ladies Night/audiofiles/Track 01-1.wav" --> "/Users/kward/Music/Sessions/20170906 ICF Ladies Night Stems/01-01 Cajon (direct out).wav"
  "/Users/kward/Music/Tracks Live/20170906 ICF Ladies Night/interchange/20170906 ICF Ladies Night/audiofiles/Track 02-1.wav" --> "/Users/kward/Music/Sessions/20170906 ICF Ladies Night Stems/01-02 Shaker (direct out).wav"
  "/Users/kward/Music/Tracks Live/20170906 ICF Ladies Night/interchange/20170906 ICF Ladies Night/audiofiles/Track 03-1.wav" --> "/Users/kward/Music/Sessions/20170906 ICF Ladies Night Stems/01-03 aRuben (direct out).wav"
  "/Users/kward/Music/Tracks Live/20170906 ICF Ladies Night/interchange/20170906 ICF Ladies Night/audiofiles/Track 04-1.wav" --> "/Users/kward/Music/Sessions/20170906 ICF Ladies Night Stems/01-04 aToby-L (direct out).wav"
...
```

Perform the actual copy by running the command again without the `--dry_run` flag (notice the trailing `\` character on the `--patch_file` line was also removed).

```console
$ tracks \
  move \
    --src_dir "~/Music/Tracks Live/20170906 ICF Ladies Night/interchange/20170916 ICF Ladies Night/audiofiles" \
    --dest_dir "~/Music/Sessions/20170906 ICF Ladies Night Stems" \
    --patch_file "~/Music/Sessions/20170906 ICF Ladies Night.html"
```

The output should be the same as above, but this time the files were actually copied.

### Get info about a file

If you are curious about what type of file information a `.wav` file has, you can use the `info` command.

```console
$ tracks info --file "01-64 Main R -23 LUFS (direct out).wav"
sample_rate: 48000 Hz, channels: 1 bits_per_sample: 24 frame_count: 307459072 duration: 1h46m45.397333333s
```

### Checking a file for silence

There are times when Tracks has issues talking to the audio device, happening more frequently than desired between Tracks and my Avid S3L-X. Luckily, Tracks doesn't break the recording off (like Pro Tools does, which is why I don't use Pro Tools for recording via AVB), and instead records only silence.

To test for silence, use the `check` command. The file below unfortunately has several blocks of silence, but now I can quickly document where they are, and don't need to listen to the full 1h46m to find them all.

```console
$ tracks check --file "01-64 Main R -23 LUFS (direct out).wav"
3m43s - 3m50s
6m50s - 6m58s
27m10s - 27m15s
27m15s - 27m23s
48m54s - 48m59s
49m0s - 49m2s
58m38s - 58m43s
58m44s - 58m52s
1h25m50s - 1h25m55s
1h25m55s - 1h25m57s
1h41m17s - 1h41m24s
```

## Getting help

To see a full list of available flags, request `--help`.

```console
$ tracks --help
NAME:
   tracks - A tool for integrating Waves Tracks and Avid Venue

USAGE:
   tracks [global options] command [command options] [arguments...]

COMMANDS:
     help, h  Shows a list of commands or help for one command

   venue:
     copy  copy tracks with new names
     link  make links with new names, without removing original files
     move  move or rename tracks

   wave:
     check  check wave files for known errors
     info   output info about wave file

GLOBAL OPTIONS:
   --dry_run, -n  do a dry run
   --help, -h     show help
   --version, -v  print the version
```

## Installation
_This section covers the installation of Go, required Go libraries, and the Tracks source code._

Download and install the latest version of Go using instructions from https://golang.org/doc/install. The Tracks tool works on all versions of Go starting at v1.6.

To test setup a Ubuntu 16.04 Linux machine, these commands were used:

```console
$ sudo apt-get install golang
$ mkdir ~/opt/go/{bin,pkg,src}
$ cat <<EOF >~/.bashrc
# Golang
export GOPATH="${HOME}/opt/go"
export GOBIN="${GOPATH}/bin"
PATH="${PATH}:${GOBIN}"
EOF
```

Log out and back in to test that the new environment variables were configured properly.

Download the Tracks tool source code. This will automatically compile the code, and place a binary in the `$GOBIN` directory.

```console
$ go get -v -t github.com/kward/tracks
```

Test the installation.

```console
$ tracks --help
```

## Updating
_This section covers updating an existing installation._

To update the software, run the following command to download the latest source, and compile a new binary.

```console
$ go get -v -t -u github.com/kward/tracks
```

## Tested software and systems
- macOS (10.12 Sierra, 10.13 High Sierra)
- Waves Tracks Live 1.3
- Avid D-Show (3.1.1)
- Avid Profile (unknown version)
- Avid S3L-X (4.5.3.3)
- Avid VENUE Standalone (4.5.3)
- Avid Pro Tools (12.x)

:warning: This software is neither endorsed nor supported by either Waves or Avid.
