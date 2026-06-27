# [wb32-dfu-updater_cli](https://github.com/WestberryTech/wb32-dfu-updater)

[![Current Version](https://img.shields.io/github/tag/WestberryTech/wb32-dfu-updater.svg)](https://github.com/WestberryTech/wb32-dfu-updater/tags)
[![License](https://img.shields.io/github/license/WestberryTech/wb32-dfu-updater)](https://github.com/WestberryTech/wb32-dfu-updater/blob/master/LICENSE)
[![GitHub contributors](https://img.shields.io/github/contributors/WestberryTech/wb32-dfu-updater.svg)](https://github.com/WestberryTech/wb32-dfu-updater/pulse/monthly)
[![GitHub forks](https://img.shields.io/github/forks/WestberryTech/wb32-dfu-updater.svg?style=social&label=Fork)](https://github.com/WestberryTech/wb32-dfu-updater/)
[<img src="https://s1.ax1x.com/2022/05/18/OoUE79.png" width="2%" height="3%" />](https://formulae.brew.sh/formula/wb32-dfu-updater_cli)
[<img src="https://s1.ax1x.com/2022/05/18/OoawP1.png" width="2%" height="3%" />](https://packages.msys2.org/package/mingw-w64-x86_64-wb32-dfu-updater?repo=mingw64)  

`wb32-dfu-updater` is a host tool used to download and upload firmware to/from WB32 MCU via USB. (`wb32-dfu-updater_cli` is the command line version)

## Pre-built packages

| OS | Link |
| --- | --- |
| Windows (MINGW64) | https://packages.msys2.org/package/mingw-w64-x86_64-wb32-dfu-updater |
| macOS | https://formulae.brew.sh/formula/wb32-dfu-updater_cli |
| Fedora Linux | https://copr.fedorainfracloud.org/coprs/erovia/wb32-dfu-updater/ |
| Other Linux | https://formulae.brew.sh/formula/wb32-dfu-updater_cli |

## How to build wb32-dfu-updater_cli:

**Windows system please run on MINGW64!!!**

### Prerequisites :

- cmake-3.5.0 or more see http://www.cmake.org/cmake/resources/software.html
- libusb-1.0.24 or more see https://github.com/libusb/libusb/releases/download/v1.0.24/libusb-1.0.24.tar.bz2

`libusb` is discovered automatically through `pkg-config`, so no manual path configuration is required. Install a C compiler, CMake, pkg-config and the libusb-1.0 development files for your system :

| Platform | Install command |
| --- | --- |
| Debian or Ubuntu | `sudo apt install build-essential cmake pkg-config libusb-1.0-0-dev` |
| Fedora | `sudo dnf install gcc cmake pkgconf-pkg-config libusb1-devel` |
| Arch | `sudo pacman -S base-devel cmake pkgconf libusb` |
| macOS | `brew install cmake pkg-config libusb` |
| MSYS2 or MinGW64 | `pacman -S mingw-w64-x86_64-toolchain mingw-w64-x86_64-cmake mingw-w64-x86_64-pkgconf mingw-w64-x86_64-libusb` |

### Install the wb32-dfu-updater_cli :
- ``` git clone https://github.com/WestberryTech/wb32-dfu-updater.git ```
- ``` cd wb32-dfu-updater ```
- ``` cmake -S . -B build -DCMAKE_BUILD_TYPE=Release ```
- ``` cmake --build build ```
- ``` cmake --install build ```
- If Permission denied is displayed, use the ``` sudo cmake --install build ```

To install to a different location than the default `/usr/local`, set the prefix when configuring :
- ``` cmake -S . -B build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX="$HOME/.local" ```

If `libusb` is installed in a non-standard location, point `pkg-config` at it before configuring :
- ``` PKG_CONFIG_PATH=/opt/libusb/lib/pkgconfig cmake -S . -B build -DCMAKE_BUILD_TYPE=Release ```

## Windows driver

You can found the Windows driver for wb32-dfu-updater_cli in the `driver` directory.

To install the Windows driver for wb32-dfu-updater_cli, you should unzip the package and run `winusb_install.bat`.
