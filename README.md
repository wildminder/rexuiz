Compiled flawlessly with SDL2-2.26.5.
Also compiled with the latest SDL2-2.32.8, but starting from SDL 2.27, there are some issues with loading speed and freezes in the game menu — likely caused by the modern Windows API for gamepad/joystick input (Windows.Gaming.Input).


**How to build**

Building on Windows.

**1: Setting up the Build Environment**

1.  **Download MSYS2:**
    *   Go to the MSYS2 official website: [https://www.msys2.org/](https://www.msys2.org/)
    *   Download the `msys2-x86_64-*.exe` installer.

2.  **Install MSYS2:**
    *   Run the installer. It's recommended to install it to a simple path like `C:\msys64`.
    *   After installation, do **not** immediately close the final installer window if it offers to run MSYS2. If it doesn't, find "MSYS2 MSYS" in your Start Menu and run it.

3.  **Update MSYS2 Core Packages:**
    *   In the "MSYS2 MSYS" terminal window that opens, first update the package database and core system packages:
        ```bash
        pacman -Syu
        ```
    *   It might ask you to close the terminal. If it says `warning: terminate MSYS2 without returning to shell and check for updates again`, close the MSYS2 window (`exit` or click the X), then re-open "MSYS2 MSYS" from the Start Menu and run the command again:
        ```bash
        pacman -Syu
        ```
    *   Repeat this process (close, reopen, `pacman -Syu`) until it says there are no more updates.
	*   Close the "MSYS2 MSYS" terminal.

4.  **Install the MinGW-w64 Toolchain and Build Tools:**
    *   Open the **"MSYS2 MinGW 64-bit"** terminal from your Start Menu. And sets up the environment for 64-bit MinGW compilation.
    *   Install the 64-bit MinGW-w64 toolchain (GCC, G++, make, etc.) and other necessary tools:
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
    *   Press Enter to select all packages in the `mingw-w64-x86_64-toolchain` group when prompted.
    *   Confirm the installation by typing `Y` when asked.

5.  **Verify Installation (Optional):**
    *   In the "MSYS2 MinGW 64-bit" terminal, check if the tools are available:
        ```bash
        gcc --version
        g++ --version
        make --version
        cmake --version
        wget --version
        ```
    They should report their versions.


6. **Compile vorbis-tools from source**
	*	Check your system for `vorbis-tools`
        ```bash
        which oggenc
        ```	
			
		If you can't find the `oggenc`, you could compile vorbis-tools from source within your MSYS2 MinGW. Download the vorbis-tools source		
        ```bash
        git clone https://github.com/xiph/vorbis-tools
        cd vorbis-tools
        ```
	*   In the "MSYS2 MinGW 64-bit" terminal:
        ```bash
        AUTOMAKE_FLAGS=--include-deps ./autogen.sh
		./configure --prefix=/mingw64 
		make
		make install		
        ```	
		`--prefix=/mingw64` - to install it into your MinGW64 tree.

	*	Check again for `vorbis-tools`. It should point the folder with oggenc
        ```bash
        which oggenc
        ```	
	* If something went wrong, try to clean to clean-up the `vorbis-tools` folder:
        ```bash
        make clean
		make maintainer-clean
        ```	
		
**2: Getting the Rexuiz Project Files**

0.	**nexuiz-252.zip**. If downloading failed, get this file from the [sourceforge](https://sourceforge.net/projects/nexuiz/files/)
	
1.  **Clone or Download the Project:**
    *   If you have Git installed on Windows or within MSYS2 (`pacman -S git`), you can clone it. In the terminal:
        ```bash
        git clone --recurse-submodules -j8 https://github.com/wildminder/rexuiz-neo rexuiz-source
        cd rexuiz-source
        ```
		you need to use `--recurse-submodules` to get all submodules

**3: Building Rexuiz**

1.  **Navigate to the Project Directory:**
    *   Make sure you are in the `rexuiz-source` directory within the **"MSYS2 MinGW 64-bit"** terminal.

2.  **Run the Make Command:**
    *   The Makefile is designed to detect `win64` as a target. Since you are already in a MinGW 64-bit environment, you don't need to specify `CROSSPREFIX`.
    *   Execute the build:
        ```bash
        make DPTARGET=win64
        ```
    *   Warning. Building the whole project will take a **long time**, especially the first time, as it will:
        *   Download dependencies (SDL2, libpng, libjpeg, freetype, curl, etc.) using `wget`.
        *   Configure and compile each dependency for MinGW-w64.
        *   Build `gmqcc`.
        *   Compile QuakeC game logic.
        *   Build the DarkPlacesRM engine.
        *   Re-package game data.

**4: Running the Game**
    
*   After a successful build, the compiled game and data will be in a subdirectory named `Rexuiz` within your project folder (e.g., `C:\projects\rexuiz-source\Rexuiz`).
*   The main executable will likely be `Rexuiz\rexuiz-sdl-x86_64.exe`.
*   Required DLLs (like `libcurl-4.dll`, `libfreetype-6.dll`) will be in `Rexuiz\bin64\`. The game engine should be able to find these.
*   `libwinpthread-1.dll` if this dll is missing, copy it manually from `msys64\mingw64\bin`
	
**5. Building the Launcher (Optional):**

The project includes an FLTK-based launcher (`flrexuizlauncher`). You can build it separately if needed.

*   **For Linux/macOS:**
    ```bash
    make flrexuizlauncher
    ```
*   **For Windows (cross-compilation from Linux):**
    *   32-bit:
        ```bash
        make flrexuizlauncher DPTARGET=win32
        ```
    *   64-bit:
        ```bash
        make flrexuizlauncher DPTARGET=win64
        ```
    The compiled launcher will be placed in the `Rexuiz/` directory with a platform/architecture-specific name (e.g., `RexuizLauncher.Linux-x86_64`, `RexuizLauncher.Windows-x86_64.exe`, or inside the macOS app bundle for the launcher).


**DarkPlacesRM**

Remember, `rexuiz-sdl-x86_64.exe` is the game engine. `RexuizLauncher.Windows-x86_64.exe` is the launcher. Your engine modifications will affect the former. The launcher will simply execute whatever `rexuiz-sdl-x86_64.exe` it finds.

*   You can build just the Engine. It will take less time then compiling the whole project. You can then take this newly compiled executable and place/replace the existing `rexuiz-sdl-x86_64.exe` in your pre-compiled Rexuiz game distribution (You can name it whatever you like).

1.  **Navigate to the Root Project Directory:**
    `cd /path/to/rexuiz-source`

2.  **Recompile the Engine:**
    From the root `rexuiz-source` directory, you can use the specific `engine` target from the main `Makefile`. This target is designed to build the engine and its immediate dependencies. In your MSYS2 MinGW 64-bit terminal.
    ```bash    
    make engine DPTARGET=win64
    ```
    
	This command will:
    *   Go into `DarkPlacesRM/`.
    *   Execute the make command defined by `DPMAKEOPTS` to build `sdl-rexuiz`.
    *   The output `rexuiz-sdl-x86_64.exe` will be in the `DarkPlacesRM/` directory.


**Important Considerations and Troubleshooting:**

*   **Cleaning:** If you need to rebuild, you can use `make clean DPTARGET=win64` to clean up previous build artifacts for the Windows target.

*   **/gmqcc/CMakeCache.txt is different than the directory**

    You need to remove the old CMake configuration files from the `gmqcc` directory so that CMake can reconfigure itself with the new correct paths.

    a.  **Navigate to the `gmqcc` directory** within your MSYS2 MinGW 64-bit terminal:
        ```bash
        cd gmqcc
        ```

    b.  **Remove CMake generated files** inside gmqcc (CMakeCache.txt, CMakeFiles/, Makefile, cmake_install.cmake):
        ```bash
        rm -f CMakeCache.txt
        rm -rf CMakeFiles/
        rm -f Makefile
        rm -f cmake_install.cmake
        ```

    c.  **Go back to the project root:**
        ```bash
        cd ..
        ```

    d.  **Try the build again:**
        Now, run your `make` command from the project root.
        ```bash
        make DPTARGET=win64
        ```
       
*   **Disk Space:** Ensure you have enough free disk space for MSYS2, the toolchain, project sources, and compiled libraries/binaries. At least 10Gb
*   **Build Time:** Be patient. Compiling everything from scratch can take a long time.


