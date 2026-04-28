---
layout: default
parent: GTK Tutorial
title: Prerequisites
weight: 1
---

# Using a prebuilt SDK
The rest of the tutorial will show you how to manually set up the
`koxtoolchain` + `kindle-sdk` combination. However, I'm already building
pre-packaged SDKs that just need extracting and are ready for use afterwards.

You can find these under the [kindle-sdk releases][]. Always take the latest
one, as it will include the latest libraries. These are a device + specific
firmware version, like `scribe1-5.17.3.tar.gz`, or
`kindlepw5-5.16.2.1.1.tar.gz`. This doesn't mean they'll only work for a
specific device and/or firmware, but some devices and/or firmwares will have
different libraries, or different versions from libraries.

If you are not tightly integrating against Kindle internal libraries, the
included `kindlepw5-5.16.2.1.1.tar.gz` will work for SF (Soft-Float) Kindles,
while `scribe1-5.17.3.tar.gz` will work for HF (Hard-Float) Kindles.

```sh
# Downloading the latest HF pre-build SDK:
wget https://github.com/Sighery/kindle-sdk/releases/latest/download/scribe1-5.17.3.tar.gz
# Unpacking to any arbitrary directory:
mkdir ~/kindle-toolchains/
tar -xzf scribe1-5.17.3.tar.gz -C ~/kindle-toolchain/
```

If you use one of the pre-built SDKs, you can skip to the next section,
[Setting Up The Project][].

# Prerequisites
You will need to be running a Linux operating system* or WSL/msys2 under Windows<br/>
*MacOS is untested but may work

You will need a jailbroken Kindle and you will need to know what firmware your Kindle is running on.

You will also need to compile a toolchain targetting your device, the details of which are explained further on this page.

You will also need the following packages installed on your system for the toolchain:
- git
- ncurses
- gperf
- help2man
- bison
- texinfo
- flex
- gawk
- unzip
- wget

As well as `curl` and `sed` installed for the SDK

And specifically for this tutorial:
- meson
- gtk2 (with development headers)
- compilation tools

## Installing The Required Packages
### For Arch Linux
```sh
# For the toolchain
sudo pacman -S base-devel curl git gperf help2man unzip wget

# For the SDK
sudo pacman -S curl sed libarchive nettle

# For this tutorial
sudo pacman -S meson gtk2
```

### For Debian/Ubuntu
```sh
# For the toolchain
sudo apt-get install build-essential autoconf automake bison flex gawk libtool libtool-bin libncurses-dev curl file git gperf help2man texinfo unzip wget

# For the sdk
sudo apt-get install curl sed libarchive-dev nettle-dev

# For this tutorial
sudo apt-get install meson gtk2.0 libgtk2.0-dev
```

## Building The Toolchain

> [!NOTE]
> If you don't want to build the toolchain yourself or if you encounter difficulties in building it, use the [pre-built release](https://github.com/koreader/koxtoolchain/releases/latest)

#### 1. Clone the toolchain
```sh
git clone --recursive --depth=1 https://github.com/koreader/koxtoolchain.git
```

#### 2. Build the toolchain for your device
```sh
cd koxtoolchain
chmod +x ./gen-tc.sh
./gen-tc.sh <target>
```

Where target should be replaced as follows:

|     TC    |              Supported Devices              |               Target             |
|:---------:|:-------------------------------------------:|:--------------------------------:|
|   kindle  |             Kindle 2, DX, DXg, 3            | [not supported by this tutorial] |
|  kindle5  |             Kindle 4, Touch, PW1            | [not supported by this tutorial] |
| kindlepw2 | Kindle PW2 & everything since on FW <5.16.3 |             kindlepw2            |
|  kindlehf |          Any Kindle on FW >= 5.16.3         |              kindlehf            |

> [!NOTE]
> If you want to support multiple Kindles, you can compile multiple toolchain targets just by running `./gen-tc.sh <other_target>` and the new toolchain be added to your `~/x-tools` directory

> [!NOTE]
> Compilation usually takes around 30-minutes per target on most PCs

## Setting up the SDK

The KMC Kindle SDK augments your existing koxtoolchain installation by providing headers and library files used with the Kindle.

#### 1. Clone the SDK
```sh
git clone --recursive --depth=1 https://github.com/Sighery/kindle-sdk.git
```

#### 2. Install the SDK for your target
```sh
cd kindle-sdk
chmod +x ./gen-sdk.sh
./gen-sdk.sh <target>
```
Where `<target>` is the same as the toolchain you want to install the SDK for.

![](./images/sdk_install.png)
Once the SDK has finished installing itself, make a note of the path it returns to the `meson-crosscompile.txt` file, this will be important later!


[kindle-sdk releases]: https://github.com/Sighery/kindle-sdk/releases
[Setting Up The Project]: ./setting-up.html
