# pcm2pwm 1.0 - a utility for 1-bit audio weirdos   // code by:  jeff@alyanak.ca
pcm2pwm is a simply utility for converting 8-bit PCM wave audio into a delta PWM byte stream.
It's output is intended for use with any 1-bit audio devices which require a simple playback
routine and/or relatively high degree of compression.

[![GitHub version](https://img.shields.io/github/release-pre/jeffalyanak/pcm2pwm.svg)](https://github.com/jeffalyanak/pcm2pwm/releases/latest)
[![License](https://img.shields.io/github/license/jeffalyanak/pcm2pwm.svg)](https://github.com/jeffalyanak/pcm2pwm/blob/development/LICENSE.txt)
[![Donate](https://img.shields.io/badge/donate--green.svg)](https://jeff.alyanak.ca/donate)
[![Matrix](https://img.shields.io/matrix/pcm2pwm:social.rights.ninja.svg)](https://matrix.to/#/#pcm2pwm:social.rights.ninja)
[![PGP](https://img.shields.io/keybase/pgp/jeffalyanak.svg?label=pgp)](https://jeff.alyanak.ca/pgp)

## Running the utility
pcm2pwm is a simple tool. It will reject formats it cannot read, feed it **only mono 8-bit wave files**. Audacity is a great, multi-platform tool for preparing your audio (see section below) and will allow you to export in this format.

Basic use is simple, just run the tool and hand it a filename with the `-i` option:

`$ pcm2pwm -i input.wav`

The pcm2pwm tool will output to standard output, so if you'd like to save the output to a file, just use:

`$ pcm2pwm -i input.wav > output.txt`

Additionally, you may specify two values to act as the high and low crossovers, using the `-H` and `-L` options. These values must be between `255` and `0`. For example:

`$ pcm2pwm -H 240 -L 15 -i input.wav`

This would trigger the high and low states slightly earlier and might be useful if your input wav file isn't very loud. Moving these two values too close or too far from your average amplitude will ruin the timbre of the resulting output sound. Experiment to see what works best, optimal values may differ depending on the playback routine.

## Modes

### Binary output
As of version 1.0, the `-b` option can be used to provide a filename for direct binary output.

`$ pcm2pwm -i input.wav -b output.bin <...>`

### Houston Tracker 2, inverse output
As of version 0.8, you may also add the `-h` option before the input file to toggle an inverted output format suitable for [Houston Tracker 2](https://github.com/utz82/HoustonTracker2).

`$ pcm2pwm -h -i input.wav <...>`

> _Remember that HT2 requires a <b>0x00</b> terminator after your sample data._

### DASM, tabbed output
As of version 1.0, the `-d` option can be used to provide output suitable for use in DASM, which requires a tab at the start of each `.byte` statement.

`$ pcm2pwm -d -i input.wav <...>`

## Preparing your input audio
With the addition - in version 0.5+ - of custom high and low crossovers, a wider range of
audio can be run through the pcm2pwm tool. However, the following advice may still return
the best results:


> _The input audio must be compressed, limited or otherwise overdriven in order to keep most of the samples below 3 and above 252 — basically, you want it to be clipping, or nearly clipping, as much as possible._

> _The easiest way to do this is to use an audio program like audacity to heavily amplify your audio with the "allow clipping" box ticked. If you know what you're doing with audio then you already know the tricks for maximizing your output "loudness", do this to the extreme._

## Using the output
Each output byte represents the number of samples since a zero-crossover. That is
to say, it's the number of samples since a state change. The output sample-rate will
match the input sample-rate, therefor a 11024Hz wav will produce byte data based on
the same reference rate. If your playback routine is faster or slower than this rate,
the pitch/speed of the resulting audio will be altered.


## Playback routine
If you're even thinking of using this utility you probably already know what you want
to use it for but here's an extremely simple explanation of what you need your playback
routine to do:

  * read byte
  * wait that many samples length of time (your playback routine may be slightly faster or slower given limitations of limited processors)
  * toggle state
  * read next byte
  * etc

Pretty simple, eh!

### Version History

  * 0.2   - Basic functionality
  * 0.5   - Added the ability to set high and low crossover values.
  * 0.7   - pcm2pwm now checks the input file's header to ensure compatibility.
  * 0.8   - Added the -h option for outputting in a Houston Tracker 2 format.
  * 0.8.1 - Bugfix affecting the conversion process. Should produce better output than before.
  * 1.0   - Switched to POSIX-style options/arguments, added binary output mode to output directly to a binary file, added DASM output mode to append a `\t` to each line.
