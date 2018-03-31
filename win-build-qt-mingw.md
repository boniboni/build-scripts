﻿## Build Qt with MSYS2
Using MSYS2 is much more easier, so I will only write about how to build Qt through MSYS2. If you want to build Qt through MSYS or MinGW(-w64), do it yourself.

### Step 1: Prepare MSYS2
1. Download MSYS2 from it's [official website](http://www.msys2.org/) (http://www.msys2.org/) and install it.
2. Run MSYS2 shell command prompt (default location: "C:\msys64\msys2_shell.cmd" -msys)
3. Update MSYS2 core components (if you have not done it yet):
   ```text
   pacman -Syu
   ```
4. Exit out from MSYS2-shell (just close the shell window forcely), restart MSYS2-shell, then run below command, to complete rest of other components update:
   ```text
   pacman -Su
   ```
5. Exit out of MSYS2-shell, restart MSYS2-shell, then you are ready to use MSYS2-shell.

### Step 2: Install required components
1. If you want to use ANGLE, download Microsoft DirectX SDK June 2010 from it's [official website](http://www.microsoft.com/en-us/download/details.aspx?id=6812) (http://www.microsoft.com/en-us/download/details.aspx?id=6812) and install it.
2. Start MSYS2-shell. Run/execute below commands to load MinGW-w64 SEH (64bit/x86_64) posix and Dwarf-2 (32bit/i686) posix toolchains & related other tools, dependencies & components from MSYS2 REPO:
   ```text
   pacman -S base-devel git mercurial cvs wget p7zip
   pacman -S perl ruby python2 mingw-w64-i686-toolchain mingw-w64-x86_64-toolchain
   ```
   **Note**

   The i686 (32bit) toolchain loads into "/c/msys64/mingw32/bin" ("C:\msys64\mingw32\bin") directory location, and, x86_64 (64bit) toolchain loads into "/c/msys64/mingw64/bin" ("C:\msys64\mingw64\bin") directory. Perl, Ruby, Python, OpenSSL etc loads into "/c/msys64/usr/bin" ("C:\msys64\usr\bin") directory.

### Step 3: Build Qt
1. Start MSYS2 shell command prompt and run below commands:
   ```text
   cd /c/Qt/Qt5.11.0/src
   windows2unix() { local pathPcs=() split pathTmp IFS=\;; read -ra split <<< "$*"; for pathTmp in "${split[@],}"; do pathPcs+=( "/${pathTmp//+([:\\])//}" ); done; echo "${pathPcs[*]}"; }; systemrootP=$(windows2unix "$SYSTEMROOT"); export PATH="/c/Program Files (x86)/Microsoft DirectX SDK (June 2010):$PWD/qtbase/bin:$PWD/gnuwin32/bin:/c/msys64/mingw64/bin:/c/msys64/usr/bin:$PATH"
   ```
   **Note**

   "/c/Qt/Qt5.11.0/src" is the location of your Qt source code directory, "/" means "\\", "/c/" means "C:\\", "/d/" means "D:\\" and so on.
2. Run below commands to configure Qt:
   ```text
   ./configure -opensource -confirm-license -release -static -static-runtime -skip qtwebengine -platform win32-g++ -opengl dynamic -nomake examples -nomake tests -prefix $PWD/../dist -c++std c++1z -silent -ltcg
   ```
   **Note**

   1. If you want to build the debug version of Qt, use "-debug" instead of "-release" or if you want to build both debug and release versions of Qt, use "-debug-and-release" instead of "-release".
   2. If you want to build the shared version of Qt, use "-shared" instead of "-static -static-runtime".
   3. **If you want to build QWebEngine, you have to change your system locale to English(United States)** and don't forget to change it back after building Qt.
   4. According to Qt official wiki, **QWebEngine module cannot be compiled statically**, so you have skip it using "-skip qtwebengine" when you are building the static version of Qt.
   5. If you want Qt to load OpenGL dynamically, use "-opengl dynamic" (recommended, but need Microsoft DirectX SDK to build ANGLE), if you want Qt to use Desktop OpenGL only, use "-opengl desktop", if you want Qt to use ANGLE only, use "-opengl es2" (Windows only).
   6. --prefix=The target directory the compiled binaries should be copied to.
3. If the configuration process above finished successfully, run below commands to build Qt and copy the binaries to the target directory:
   ```text
   mingw32-make -j n && mingw32-make install
   ```
   **Note**

   mingw32-make -j n where "n" are the number of CPU cores you have.