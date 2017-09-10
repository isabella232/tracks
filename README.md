# venue-tracks
Tool to rename tracks based on Venue channel names.

This software is currently quite limited in that it expects the tracks to be
named like "Track XX-Y.wav" where XX is the track number, and Y is the session
number.

If the `dest_dir` flag is not provided, as is shown in the exmaples, the files
will be renamed in-place. In this case, it is highly recommended that a backup
of the files be made before running the software as it is still relatively new
software.

## Project links

[![GoDoc](https://godoc.org/github.com/kward/tracks?status.svg)](https://godoc.org/github.com/kward/tracks)
[![Travis CI](https://travis-ci.org/kward/tracks.png?branch=master)](https://travis-ci.org/kward/tracks)

## Usage
Perform a test run of renaming all tracks in the `~/tmp/audiofiles` directory using the `.

```sh
go run tracks.go \
  --src_dir "~/tmp/audiofiles" \
  --info_file "~/tmp/Avid VENUE Jul 02 2017, 13-08.html" \
  --dry_run
```

Perform the rename.

```sh
go run tracks.go \
  --src_dir "~/tmp/audiofiles" \
  --info_file "~/tmp/Avid VENUE Jul 02 2017, 13-08.html"
```
