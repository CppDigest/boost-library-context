# #24 - Cannot find `BoostRoot.cmake` when using CMake to compile source codes. [Closed]

> Username: hwhsu1231  
> Created at: 2022-07-27 09:19:46 UTC  
> Updated at: 2023-07-15 07:53:04 UTC  
> Closed at: 2023-01-07 03:01:15 UTC  
> Url: https://github.com/boostorg/cmake/issues/24  

### Descriptions  
  
I tried to use CMake to compile the source codes. However, it failed with the following errors:  
  
```cmd  
CMake Error at CMakeLists.txt:20 (include):  
  include could not find requested file:  
  
    BoostRoot  
  
  
-- Configuring incomplete, errors occurred!  
```  
  
Therefore, I went to check the project folder, but didn't find `BoostRoot.cmake`.  
  
Besides, I found that all of folders inside the `tools` folder are **EMPTY**, too.  
  
What happened? What do I miss?   
  
### Steps of Installation  
  
<details><summary>Click to expand</summary>  
  
1.  Download and unzip the source code.  
  
    [boost-1.79.0.zip](https://github.com/boostorg/boost/archive/refs/tags/boost-1.79.0.zip)  
  
2.  Open Windows Terminal (`cmd.exe`) and change to the project folder.  
  
    ```  
    cd boost-1.79.0  
    ```  
  
3.  call `vcvarsall.bat x64` to preload the MSVC environment.  
  
    ```  
    vcvarsall x64  
    ```  
  
4.  Create the build folder and change into it.  
  
    ```  
    md build-msvc16-x64  
    cd build-msvc16-x64  
    ```  
  
5.  Configure the project with the generator `Ninja Multi-Config`.  
  
    ```  
    cmake ../cmake -G"Ninja Multi-Config"  
    ```  
  
</details>  
  
### Logs  
  
<details><summary>Click to expand</summary>  
  
```cmd  
C:\ccxxpkgs\source\boost-1.79.0>md build-msvc16-x64  
  
C:\ccxxpkgs\source\boost-1.79.0>cd build-msvc16-x64  
  
C:\ccxxpkgs\source\boost-1.79.0\build-msvc16-x64>vcvarsall x64  
**********************************************************************  
** Visual Studio 2019 Developer Command Prompt v16.11.16  
** Copyright (c) 2021 Microsoft Corporation  
**********************************************************************  
[vcvarsall.bat] Environment initialized for: 'x64'  
  
C:\ccxxpkgs\source\boost-1.79.0\build-msvc16-x64>cmake .. -G"Ninja Multi-Config"  
-- The CXX compiler identification is MSVC 19.29.30145.0  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
CMake Error at CMakeLists.txt:20 (include):  
  include could not find requested file:  
  
    BoostRoot  
  
  
-- Configuring incomplete, errors occurred!  
See also "C:/ccxxpkgs/source/boost-1.79.0/build-msvc16-x64/CMakeFiles/CMakeOutput.log".  
```  
  
</details>  
  
### Screenshots  
  
![image](https://user-images.githubusercontent.com/71438617/181211095-573bf74d-bef8-4ecb-8d41-a096803eafa0.png)

---

## Comment 1

> Username: pdimov  
> Created at: 2022-07-27 14:11:59 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1196814954  

Your step 1 only downloads the "boostorg/boost" repository, but not its submodules. You have to use `git clone -b boost-1.79.0 --recurse-submodules https://github.com/boostorg/boost` instead.  
  
In addition, step 5 needs to be `cmake ..` instead of `cmake ../cmake`.

---

## Comment 2

> Username: hwhsu1231  
> Created at: 2022-07-27 18:56:27 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1197236997  

@pdimov  
  
I followed what you said to `git clone` the repository, and then use CMake to compile the source code. It **WORKED**.   
  
### Steps of Installation  
  
<details><summary>Click to expand</summary>  
  
1.  git clone the source code.  
  
    ```  
    git clone -b boost-1.79.0 --recurse-submodules https://github.com/boostorg/boost  
    ```  
  
2.  Open Windows Terminal (`cmd.exe`) and change to the project folder.  
  
    ```  
    cd boost  
    ```  
  
3.  call `vcvarsall.bat x64` to preload the MSVC environment.  
  
    ```  
    vcvarsall x64  
    ```  
  
4.  Create the build folder and change into it.  
  
    ```  
    md build-msvc16-x64  
    cd build-msvc16-x64  
    ```  
  
5.  Configure the project with the generator `Ninja Multi-Config`.  
  
    ```  
    cmake .. -G"Ninja Multi-Config"  
    ```  
  
6.  Open `cmake-gui` to edit some Cache Variables. After editing, press **Configure** and **Generate** buttons. Then, close it.  
  
    ```  
    cmake-gui .  
  
    BUILD_SHARED_LIBS=ON  
    CMAKE_INSTALL_PREFIX=C:/ccxxpkgs/install/msvc16-x64/boost-1.79.0  
    ```  
  
7.  Build the project with `Debug` and `Release` configurations.  
  
    ```  
    cmake --build . --config Debug  
    cmake --build . --config Release  
    ```  
  
8.  Install the project with `Debug` and `Release` configurations.  
  
    ```  
    cmake --install . --config Debug  
    cmake --install . --config Release  
    ```  
  
</details>  
  
### How to find_package() in CONFIG mode?  
  
However, how do I use it with `find_package()` command in **CONFIG** mode?  
  
The followings are my example code of `CMakeLists.txt` and the error messages:  
  
-   CMakeLists.txt  
  
    <details><summary>Click to expand</summary>  
  
    ```cmake  
    cmake_minimum_required(VERSION 3.15)  
    get_filename_component(folder_name "${CMAKE_CURRENT_SOURCE_DIR}" NAME)  
    project(${folder_name} LANGUAGES C CXX)  
  
    list(APPEND CMAKE_MODULE_PATH "C:/ccxxpkgs/install")  
    list(APPEND CMAKE_PREFIX_PATH "C:/ccxxpkgs/install")  
  
    find_package(boost 1.79.0 CONFIG REQUIRED  
      COMPONENTS filesystem regex asio  
    )  
    ```  
  
    </details>  
  
-   Logs of configuring CMake project  
  
    <details><summary>Click to expand</summary>  
  
    ```  
    [proc] Executing command: "C:\Program Files\CMake\bin\cmake.EXE" --no-warn-unused-cli -DCMAKE_EXPORT_COMPILE_COMMANDS:BOOL=TRUE -DCMAKE_BUILD_TYPE:STRING=Debug -DCMAKE_INSTALL_PREFIX:STRING=d:/Code-Cpp-MyFavorite/Code-Research-CMake-ThirdParty/cmake-find_package-boost/install -Sd:/Code-Cpp-MyFavorite/Code-Research-CMake-ThirdParty/cmake-find_package-boost -Bd:/Code-Cpp-MyFavorite/Code-Research-CMake-ThirdParty/cmake-find_package-boost/build/win32-MSVC-x64-Debug -G Ninja  
    [cmake] Not searching for unused variables given on the command line.  
    [cmake] -- The C compiler identification is MSVC 19.29.30145.0  
    [cmake] -- The CXX compiler identification is MSVC 19.29.30145.0  
    [cmake] -- Detecting C compiler ABI info  
    [cmake] -- Detecting C compiler ABI info - done  
    [cmake] -- Check for working C compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped  
    [cmake] -- Detecting C compile features  
    [cmake] -- Detecting C compile features - done  
    [cmake] -- Detecting CXX compiler ABI info  
    [cmake] -- Detecting CXX compiler ABI info - done  
    [cmake] -- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio/2019/Community/VC/Tools/MSVC/14.29.30133/bin/Hostx64/x64/cl.exe - skipped  
    [cmake] -- Detecting CXX compile features  
    [cmake] -- Detecting CXX compile features - done  
    [cmake] CMake Error at CMakeLists.txt:8 (find_package):  
    [cmake]   Could not find a package configuration file provided by "boost" (requested  
    [cmake]   version 1.79.0) with any of the following names:  
    [cmake]   
    [cmake]     boostConfig.cmake  
    [cmake]     boost-config.cmake  
    [cmake]   
    [cmake]   Add the installation prefix of "boost" to CMAKE_PREFIX_PATH or set  
    [cmake]   "boost_DIR" to a directory containing one of the above files.  If "boost"  
    [cmake]   provides a separate development package or SDK, be sure it has been  
    [cmake]   installed.  
    [cmake]   
    [cmake]   
    [cmake] -- Configuring incomplete, errors occurred!  
    [cmake] See also "D:/Code-Cpp-MyFavorite/Code-Research-CMake-ThirdParty/cmake-find_package-boost/build/win32-MSVC-x64-Debug/CMakeFiles/CMakeOutput.log".  
    ```  
  
    </details>  
  
  
### My opinions  
  
I think the reason why `find_package()` failed is because there didn't exist the `boost-config.cmake` file.  
  
Take Qt for reference. There is a `C:/Qt/6.3.1/msvc2019_64/lib/cmake/Qt6/Qt6Config.cmake`. Therefore, we can use it with:  
  
```cmake  
find_package(Qt6 CONFIG REQUIRED  
  COMPONENTS Core Widget Gui  
)  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2022-07-27 19:21:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1197267124  

Our CMake build does not have a master BoostConfig, so instead of `find_package(Boost 1.79.0 CONFIG REQUIRED COMPONENTS filesystem regex asio)` you will need  
```  
find_package(boost_filesystem 1.79.0 CONFIG REQUIRED)  
find_package(boost_regex 1.79.0 CONFIG REQUIRED)  
find_package(boost_asio 1.79.0 CONFIG REQUIRED)  
```  
We may add support for `find_package(Boost)` at some later date.

---

## Comment 4

> Username: hwhsu1231  
> Created at: 2022-07-27 19:33:11 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1197278452  

@pdimov   
  
Moreover, after downloading and unzipping the [boost_1_79_0.zip](https://boostorg.jfrog.io/artifactory/main/release/1.79.0/source/boost_1_79_0.zip) provided in the [Download](https://www.boost.org/users/download/) page, I found that there doesn't exist `CMakeLists.txt` in the root directory. Does Boost official miss it?  
  
### Screenshots  
  
![image](https://user-images.githubusercontent.com/71438617/181356330-bf8c371e-1952-4ad9-8aca-7e36bc89c053.png)

---

## Comment 5

> Username: hwhsu1231  
> Created at: 2022-07-27 19:35:59 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1197280860  

> We may add support for `find_package(Boost)` at some later date.  
  
I hope this feature supported as soon as possible.

---

## Comment 6

> Username: pdimov  
> Created at: 2022-07-27 20:21:40 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1197325771  

The Boost release archive has a slightly different layout - all headers from `libs/<libname>/include/boost/` are moved into `boost/` and deleted from their original location - so the CMake build doesn't support it, which is why we remove CMakeLists.txt from the release archive in order to not mislead people that it works.

---

## Comment 7

> Username: hwhsu1231  
> Created at: 2022-07-28 01:36:38 UTC  
> Updated at: 2022-07-28 03:06:37 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1197551703  

Why not use the same layout?   
  
For me, I prefer to download the zipped source codes of the specific version. Therefore, I hope that Boost can be compiled with CMake and be used with `find_package()` in **CONFIG** mode, instead of **MODULE** mode.

---

## Comment 8

> Username: pdimov  
> Created at: 2023-01-04 20:14:43 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1371373346  

We now provide CMake-compatible archives on Github: https://github.com/boostorg/boost/releases. Release 1.81 does not support `find_package(Boost)` yet, but 1.82 will.

---

## Comment 9

> Username: felixf4xu  
> Created at: 2023-07-15 07:53:04 UTC  
> Url: https://github.com/boostorg/cmake/issues/24#issuecomment-1636702341  

just for information:  
  
`BoostRoot.cmake` is in folder of `tools/cmake`, after git submodule is initialized.
