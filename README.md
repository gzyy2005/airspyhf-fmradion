# airspyhf-fmradion

*This repository is archived.* See [airspy-fmradion](https://github.com/jj1bdx/airspy-fmradion) for the further development.

* Version v0.1.2, 10-MAR-2019
* Software decoder for FM broadcast radio with AirSpy
* For MacOS and Linux
* This repository is forked from [airspy-fmradion](https://github.com/jj1bdx/airspy-fmradion) 0.2.7

## Usage

```sh
airspyhf-fmradion -q -c freq=88100000
    -b 1.0 -R - | \
    play -t raw -esigned-integer -b16 -r 48000 -c 2 -q -
```

## Introduction

**airspyhf-fmradion** is a software-defined radio receiver for FM broadcast radio, specifically designed for Airspy HF (Airspy HF+).

### airspyhf-fmradion provides

 - mono or stereo decoding of FM broadcasting stations
 - buffered real-time playback to soundcard or dumping to file
 - command-line interface (*only*)

### airspyhf-fmradion requires

 - Linux / macOS
 - C++11 (gcc, clang/llvm)
 - [Airspy HF library](https://github.com/airspy/airspyhf)
 - [sox](http://sox.sourceforge.net/)
 - Tested: Airspy R2
 - Fast computer
 - Medium-strong FM radio signal

For the latest version, see https://github.com/jj1bdx/airspyhf-fmradion

### Branches and tags

  - Official releases are tagged
  - _master_ is the "production" branch with the most stable release (often ahead of the latest release though)
  - _dev_ is the development branch that contains current developments that will be eventually released in the master branch
  - Other branches are experimental (and abandoned)

## Prerequisites

### Base requirements

  - `sudo apt-get install cmake pkg-config libusb-1.0-0-dev libasound2-dev libboost-all-dev`

### Airspy support

If you install from source in your own installation path, you have to specify the include path and library path.
For example if you installed it in `/opt/install/libairspy` you have to add `-DAIRSPYHF_INCLUDE_DIR=/opt/install/libairspyhf/include` to the cmake options.

To install the library from a Debian/Ubuntu installation just do:

  - `sudo apt-get install libairspyhf-dev`

### macOS

* Install HomeBrew `airspyhf`
* See <https://github.com/pothosware/homebrew-pothos/wiki>

```shell
brew tap pothosware/homebrew-pothos
brew tap dholm/homebrew-sdr #other sdr apps
brew update
brew install airspyhf --HEAD
```

## Installing

To install airspyhf-fmradion, download and unpack the source code and go to the
top level directory. Then do like this:

 - `mkdir build`
 - `cd build`
 - `cmake ..`

CMake tries to find librtlsdr. If this fails, you need to specify
the location of the library in one the following ways:

 - `cmake .. -DAIRSPYHF_INCLUDE_DIR=/path/airspyhf/include -DAIRSPYHF_LIBRARY_PATH=/path/rtlsdr/lib/libairspyhf.a`
 - `PKG_CONFIG_PATH=/path/to/rtlsdr/lib/pkgconfig cmake ..`

Compile and install

 - `make -j4` (for machines with 4 cores)
 - `make install`

## Basic command options

 - `-q` Quiet mode.
 - `-c config` Comma separated list of configuration options as key=value pairs or just key for switches. Depends on device type (see next paragraph).
 - `-d devidx` Device index, 'list' to show device list (default 0)
 - `-M` Disable stereo decoding
 - `-R filename` Write audio data as raw S16_LE samples. Uuse filename `-` to write to stdout
 - `-W filename` Write audio data to .WAV file
 - `-P [device]` Play audio via ALSA device (default `default`). Use `aplay -L` to get the list of devices for your system
 - `-T filename` Write pulse-per-second timestamps. Use filename '-' to write to stdout
 - `-b seconds` Set audio buffer size in seconds
 - `-X` Shift pilot phase (for Quadrature Multipath Monitor) (-X is ignored under mono mode (-M))
 - `-U` Set deemphasis to 75 microseconds (default: 50)

## Modification from airspy-fmradion v0.2.7

### Removed features

* Remove most of configuration options (unnecessary for Airspy HF)
* LPFIQ is single-stage

### No-goals

* Adaptive IF filtering (unable to obtain better results)

### Conversion process

* IF center frequency is down Fs/4 than the station frequency, i.e: when the station is 76.5MHz, the tuned frequency is 76.308MHz
* Airspy FM allows only 660kHz alias-free BW, so the maximum alias-free BW for IF is (660/2)kHz - 192kHz = 138kHz
* FM demodulation rate: 384kHz
* 48 * 16 = 768, so all filters are in integer sampling rates
* CPU usage: ~9% on Mac mini 2018 / macOS 10.14.3
* CPU usage: ~60% on Intel NUC DN2820FYKH Celeron N2830 / Ubuntu 18.04 (usable range with 2 cores)

### Filter characteristics

* IF first stage (768kHz -> 384kHz) : <-0.01dB: 80kHz, -3dB: 100kHz, -90dB: 135kHz
* Audio first stage (384kHz -> 96kHz) : <-0.01dB: 19kHz, -3dB: 28.6kHz, -90dB: 47.2kHz
* Audio second stage (96kHz -> 48kHz) : <-0.01dB: 15kHz, -3dB: 16.4kHz, -90dB: 18.8kHz

## Airspy HF configuration options

  - `freq=<int>` Desired tune frequency in Hz. Valid range from 60M to 240M. (default 100M: `100000000`)
  - `srate=<int>` Device sample rate. `list` lists valid values and exits. (default `768000`). Valid values depend on the Airspy HF firmware. Airspy HF firmware and library must support dynamic sample rate query.

## Authors

* Joris van Rantwijk
* Edouard Griffiths, F4EXB (no longer involving in maintaining NGSoftFM)
* Kenji Rikitake, JJ1BDX (maintainer)

## Acknowledgments

* András Retzler, HA7ILM
* Twitter [@lambdaprog](https://twitter.com/lambdaprog/)

## License

* As a whole package: GPLv3 (and later). See [LICENSE](LICENSE).
* Some source code files are stating GPL "v2 and later" license.
