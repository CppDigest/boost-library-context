# #181 - icu only build on windows fails [Closed]

> Username: jptrainor  
> Created at: 2023-06-16 20:46:32 UTC  
> Updated at: 2023-06-19 19:19:58 UTC  
> Closed at: 2023-06-19 19:19:58 UTC  
> Url: https://github.com/boostorg/locale/issues/181  

Compiling boost::locale with only icu enabled fails with a test_winapi_formatting.cpp link error.  
  
Build configuration:  
````  
boost.locale.icu=on boost.locale.iconv=off boost.locale.winapi=off boost.locale.std=off boost.locale.posix=off  
````  
  
Error:  
  
````  
test_winapi_formatting.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) unsigned int __cdecl boost::locale::i\  
mpl  
_win::locale_to_lcid(class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)" (__imp_?loca\  
le_  
to_lcid@impl_win@locale@boost@@YAIAEBV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@@Z) referenced in function "void\  
 __  
cdecl test_main(int,char * *)" (?test_main@@YAXHPEAPEAD@Z)  
bin.v2\libs\locale\test\test_winapi_formatting.test\msvc-14.1\debug\threadapi-win32\threading-multi\test_winapi_formatting.exe : fa\  
tal  
error LNK1120: 1 unresolved externals  
````  
  
Platform:  
* Windows 10 version 22H2  
* Windows Python, for the ICU build  
* ICU 71.1  
* Boost 1.82.0  
* Visual Studio 2017 Community Edition version 15.9.55  
  
Note: Compiling with only icu enabled does work on MacOS and Linux.  
  
The following Cygwin bash script duplicates the error:  
  
````  
#! /bin/bash -x  
  
# Download icu and boost, build icu, build boost --with-locale and  
# the buile boost::locale test with only icu enabled, as follows:  
#  
#  boost.locale.icu=on boost.locale.iconv=off boost.locale.winapi=off boost.locale.std=off boost.locale.posix=off  
#  
# The boost:locale test build will fail a link error:  
#  
# test_winapi_formatting.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) unsigned int __cdecl boost::locale::impl  
# _win::locale_to_lcid(class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const &)" (__imp_?locale_  
# to_lcid@impl_win@locale@boost@@YAIAEBV?$basic_string@DU?$char_traits@D@std@@V?$allocator@D@2@@std@@@Z) referenced in function "void __  
# cdecl test_main(int,char * *)" (?test_main@@YAXHPEAPEAD@Z)  
# bin.v2\libs\locale\test\test_winapi_formatting.test\msvc-14.1\debug\threadapi-win32\threading-multi\test_winapi_formatting.exe : fatal  
#  error LNK1120: 1 unresolved externals  
#  
#  
# Test Platform:  
#   Windows 10 version 22H2  
#   Visual Studio 2017 Community Edition version 15.9.55  
#   Cygwin to run this script  
  
set -e  
  
# Windows path to local Visual Studio environment setup batch file.  
WIN_VCVARSALL_BAT="c:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build\vcvarsx86_amd64.bat"  
  
# Don't raise ICU version beyond 71.1 until  
# https://github.com/boostorg/locale/issues/176 is resolved.  
ICU_VER="71.1"  
ICU_RELEASE=release-$(echo $ICU_VER | sed s/\\./-/)  
ICU4C_SRC=icu4c-$(echo $ICU_VER | sed s/\\./_/)-src  
  
BOOST_VER="1.82.0"  
BOOST_SRC=boost_$(echo $BOOST_VER | sed s/\\./_/g)  
  
# setup error trap  
function ErrorTrap {  
    echo FAILED  
}  
trap ErrorTrap INT TERM EXIT  
  
DIR=boost_locale_windows_icu_only.$(date "+%m-%d-%y_%H-%M-%S").d  
mkdir $DIR  
cd $DIR  
  
# setup log  
exec > >(tee boost_locale_windows_icu_only.log)  
exec 2> >(tee -a boost_locale_windows_icu_only.log)  
  
# Run windows build command by writing a temporary windows bat file  
# and executing it using cmd.exe.  The visual studio environment is  
# configured (vcvarsall.bat) before the build command.  
function wincmd {  
    local cmd="$*"  
    local batfile=$(mktemp wincmd_tmp_XXXXXX.bat)  
  
    cat <<__EOF > "$batfile"  
call "$WIN_VCVARSALL_BAT"  
$cmd  
__EOF  
  
    cat $batfile  
  
    cmd.exe /c "$batfile"  
}  
  
# get icu  
wget --quiet --no-check-certificate "https://github.com/unicode-org/icu/releases/download/${ICU_RELEASE}/${ICU4C_SRC}.tgz"  
tar xvzf "${ICU4C_SRC}".tgz  
ICU_PATH=${PWD}/icu  
  
# get boost  
wget --quiet --no-check-certificate "https://boostorg.jfrog.io/artifactory/main/release/"${BOOST_VER}"/source/"${BOOST_SRC}".tar.gz"  
tar xvzf "${BOOST_SRC}".tar.gz  
  
(  
    cd icu/source  
    wincmd msbuild allinone\\allinone.sln /p:Configuration=Debug /p:Platform=x64  
    wincmd msbuild allinone\\allinone.sln /p:Configuration=Debug /p:Platform=x64 /target:makedata  
)  
  
(  
    cd ${BOOST_SRC}  
  
    wincmd bootstrap.bat  
  
    wincmd b2 debug -j4 \  
          -sICU_PATH=$(cygpath -w "${ICU_PATH}") \  
          boost.locale.icu=on boost.locale.iconv=off boost.locale.winapi=off boost.locale.std=off boost.locale.posix=off \  
          --with-locale \  
          toolset="msvc-14.1" \  
          architecture=x86 \  
          address-model=64  
  
    # Fails with test_winapi_formatting.cpp link error.  
    # Note: cxxflags="\wd4702" works around https://github.com/boostorg/locale/issues/180  
    unset TZ;  
    wincmd b2 debug -j4 \  
          -sICU_PATH=$(cygpath -w "${ICU_PATH}") \  
          boost.locale.icu=on boost.locale.iconv=off boost.locale.winapi=off boost.locale.std=off boost.locale.posix=off \  
          --with-locale \  
          toolset="msvc-14.1" \  
          architecture=x86 \  
          address-model=64 \  
          cxxflags="\wd4702" \  
          libs/locale/test  
)  
  
# clear error trap  
trap - INT TERM EXIT  
  
echo PASSED  
````
