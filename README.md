# Waving-Z

This program is able to encode and decode ITU G.9959 frames
(implemented, e.g. by the z-wave home automation protocol) using a
RTL-SDR dongle or an HackRF One (or any other radio handling raw IQ
files).

## Build

     mkdir build
     cd build
     cmake .. -DCMAKE_BUILD_TYPE=Release
     cmake --build .


## Prerequisites

A cheap RTL SDR radio and/or an HackRF One wit the related software
`rtl-sdr` and/or `hackrf` packages.

## Example command lines for the EU frequency

The tools use the standard input and output to retrieve and generate
the data.

### Receive

Read the docs with:

     $ ./wave-in --help

Receive and decode z-wave packages using `rtl_srd` and `wave-in`.

     $ rtl_sdr -f 868420000 -s 2000000 -g 25  - | ./wave-in -u

### Transmit

Read the docs with:

     $ ./wave-out --help

Encode and transmit z-wave packages using `wave-out` and
`hackrf_transfer`.

     $ #              HomeId      SourceID FrameCtl LEN DestID CommandClass Command
     $ #              +---------+            +----+                           +---+
     $ ./wave-out -p 'd6 b2 62 08       01   41  03  0d     07           25   01 ff' > turn_on_device_7.cs8
     $ #              HomeId      SourceID FrameCtl LEN DestID CommandClass Command
     $ #              +---------+            +----+                           +---+
     $ ./wave-out -p 'd6 b2 62 08       01   41  03  0d     07           25   01 00' > turn_off_device_7.cs8
     $ hackrf_transfer -f 868420000 -s 2000000 -t turn_off_device_7.cs8

## Modulator details

The modulator is a simple FSK modulator. The modulator is phase
continuous using a simple trick (the sampling rate, baud rate, and
separation are in phase).

## Demodulator details

The demodulator is based on
https://github.com/andersesbensen/rtl-zwave and consists of an atan
demodulator and 2 nested state machines, the first one (`sample_sm`)
converts the samples into symbols and the second one (`symbol_sm`) the
bits into frame information and payload.
