<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->
<a id="readme-top"></a>

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <!-- <a href="https://github.com/wildminder/rexuiz-neo">
    <img src="images/logo.png" alt="Logo" width="80" height="80">
  </a> -->

<h1 align="center">Rexuiz Neo</h1>

  <p align="center">
    A build system for Rexuiz.
    <br />
    <a href="https://github.com/wildminder/rexuiz-neo/issues/new?labels=bug&template=bug-report---.md">Report Bug</a>
    &middot;
    <a href="https://github.com/wildminder/rexuiz-neo/issues/new?labels=enhancement&template=feature-request---.md">Request Feature</a>
  </p>
</div>

<!-- PROJECT SHIELDS -->
[![Contributors][contributors-shield]][contributors-url]
[![Forks][forks-shield]][forks-url]
[![Stargazers][stars-shield]][stars-url]
[![Issues][issues-shield]][issues-url]
<!-- [![MIT License][license-shield]][license-url] -->


<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#about-the-project">About The Project</a></li>
    <li>
      <a href="#building-on-windows">Building on Windows</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation-and-build">Installation and Build</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#advanced-building-options">Advanced Building Options</a></li>
    <li><a href="#troubleshooting">Troubleshooting</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#contact">Contact</a></li>
    <li><a href="#acknowledgments">Acknowledgments</a></li>
  </ol>
</details>



<!-- ABOUT THE PROJECT -->
## About The Project

Rexuiz is a modernization of a beloved online shooter, Nexuiz Classic. Its history began about ten years ago when the developers at AlienTrap published a simple, but very exciting free online 3D shooter. It immediately attracted attention with its dynamic, intense, fast-paced gameplay and nice graphics. At the moment, the project is gaining momentum and we want to present it to you.

## Features
- 2025-06-10
  - Updated all dependencies
  - Resolved compilation issues

**Technical Notes:**
*   Compiles flawlessly with `SDL2-2.26.5`.
*   Also compiles with the latest SDL2-2.32.8, but starting from SDL 2.27, there are some issues with loading speed and freezes in the game menu — likely caused by the modern Windows API for gamepad/joystick input (Windows.Gaming.Input).

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- GETTING STARTED -->
## Building on Windows

This guide will walk you through setting up a local development environment on Windows to build the project.

### Prerequisites

You will need to set up the MSYS2 environment to get the necessary toolchain and libraries.

1.  **Download and Install MSYS2**
    *   Go to the [MSYS2 official website](https://www.msys2.org/) and download the `msys2-x86_64-*.exe` installer.
    *   Run the installer. It's recommended to use a simple path like `C:\msys64`.
    *   After installation, run "MSYS2 MSYS" from your Start Menu.

2.  **Update MSYS2 Core Packages**
    *   In the "MSYS2 MSYS" terminal, update the package database and core packages. You may need to do this multiple times.
        ```bash
        pacman -Syu
        ```
    *   If prompted to close the terminal, do so, then re-open "MSYS2 MSYS" and run the command again until it reports no more updates.

3.  **Install MinGW-w64 Toolchain and Build Tools**
    *   Open the **"MSYS2 MinGW 64-bit"** terminal from your Start Menu.
    *   Install the required toolchain and developer tools:
        ```bash
        pacman -S --needed base-devel mingw-w64-x86_64-toolchain mingw-w64-x86_64-cmake mingw-w64-x86_64-wget \
            mingw-w64-x86_64-libzip \
            patch \
            zip \
            unzip \
            p7zip \
            mingw-w64-x86_64-imagemagick \
            mingw-w64-x86_64-libvorbis \
            mingw-w64-x86_64-yasm \
            mingw-w64-x86_64-autotools \
            mingw-w64-x86_64-openssl \
            vim
        ```
    *   Press `Enter` to select all default packages and `Y` to confirm the installation.

4.  **Compile `vorbis-tools` from source (if needed)**
    *   First, check if `oggenc` is available:
        ```bash
        which oggenc
        ```
    *   If not found, clone and compile it from source within the "MSYS2 MinGW 64-bit" terminal:
        ```bash
        git clone https://github.com/xiph/vorbis-tools
        cd vorbis-tools
        AUTOMAKE_FLAGS=--include-deps ./autogen.sh
        ./configure --prefix=/mingw64 
        make
        make install
        cd ..
        ```
    *   Verify the installation again with `which oggenc`.

### Installation and Build

With the environment set up, you can now clone and build the project.

1.  **Get Game Data**
    *   The build script will attempt to download `nexuiz-252.zip`. If this fails, please download it manually from [SourceForge](https://sourceforge.net/projects/nexuiz/files/) and place it in the project's root directory.

2.  **Clone the Repository**
    *   Clone the project and its submodules. Make sure you are in the **"MSYS2 MinGW 64-bit"** terminal.
       ```sh
       git clone --recurse-submodules -j8 https://github.com/wildminder/rexuiz-neo.git rexuiz-source
       cd rexuiz-source
       ```

3.  **Build the Project**
    *   Execute the make command. This will download dependencies, compile tools, and build the entire game. Be patient, as this can take a long time.
       ```sh
       make DPTARGET=win64
       ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- USAGE EXAMPLES -->
## Usage

*   After a successful build, the compiled game and data will be in a subdirectory named `Rexuiz` within your project folder (e.g., `C:\projects\rexuiz-source\Rexuiz`).
*   The main executable is `Rexuiz\rexuiz-sdl-x86_64.exe`.
*   Required DLLs will be in `Rexuiz\bin64\`. The game engine should find these automatically.
*   If `libwinpthread-1.dll` is missing, copy it manually from your `msys64\mingw64\bin` directory into the `Rexuiz` game folder.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ADVANCED BUILDING -->
## Advanced Building Options

### Building Only the Game Engine (DarkPlacesRM)

If you only want to recompile the game engine without rebuilding the entire project:

1.  Navigate to the root `rexuiz-source` directory in your "MSYS2 MinGW 64-bit" terminal.
2.  Run the `engine` make target:
    ```bash    
    make engine DPTARGET=win64
    ```
3.  The newly compiled `rexuiz-sdl-x86_64.exe` will be located in the `DarkPlacesRM/` directory. You can copy this executable to your existing game distribution to update it.

### Building the Launcher (Optional)

The project includes an FLTK-based launcher that can be built separately.

*   **For Linux/macOS:**
    ```bash
    make flrexuizlauncher
    ```
*   **For Windows (from MSYS2/MinGW):**
    ```bash
    make flrexuizlauncher DPTARGET=win64
    ```
The compiled launcher will be placed in the `Rexuiz/` directory.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

## Troubleshooting

*   **CMakeCache.txt is different than the directory:**
    This error occurs when CMake's cache points to old paths. To fix it, navigate to the `gmqcc` subdirectory and clean the CMake files before rebuilding.
    ```bash
    cd gmqcc
    rm -f CMakeCache.txt
    rm -rf CMakeFiles/ Makefile cmake_install.cmake
    cd ..
    make DPTARGET=win64
    ```

*   **Cleaning the Build:**
    If you need to start over, you can clean previous build artifacts for the Windows target:
    ```bash
    make clean DPTARGET=win64
    ```

*   **Disk Space:**
    Ensure you have at least 10GB of free disk space for MSYS2, the toolchain, project sources, and compiled binaries.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- ACKNOWLEDGMENTS -->
## Acknowledgments

*   The original developer of this project is https://github.com/kasymovga/rexuiz

<p align="right">(<a href="#readme-top">back to top</a>)</p>



<!-- MARKDOWN LINKS & IMAGES -->
[contributors-shield]: https://img.shields.io/github/contributors/wildminder/rexuiz-neo.svg?style=flat
[contributors-url]: https://github.com/wildminder/rexuiz-neo/graphs/contributors
[forks-shield]: https://img.shields.io/github/forks/wildminder/rexuiz-neo.svg?style=flat
[forks-url]: https://github.com/wildminder/rexuiz-neo/network/members
[stars-shield]: https://img.shields.io/github/stars/wildminder/rexuiz-neo.svg?style=flat
[stars-url]: https://github.com/wildminder/rexuiz-neo/stargazers
[issues-shield]: https://img.shields.io/github/issues/wildminder/rexuiz-neo.svg?style=flat
[issues-url]: https://github.com/wildminder/rexuiz-neo/issues
<!-- [license-shield]: https://img.shields.io/github/license/wildminder/rexuiz-neo.svg?style=flat -->
<!-- [license-url]: https://github.com/wildminder/rexuiz-neo/blob/master/LICENSE.txt -->
