# #180 - error C2220: warning treated as error, warning C4702: unreachable code [Closed]

> Username: jptrainor  
> Created at: 2023-06-16 19:34:17 UTC  
> Updated at: 2023-06-19 19:19:58 UTC  
> Closed at: 2023-06-19 19:19:58 UTC  
> Url: https://github.com/boostorg/locale/issues/180  

On Windows, the boost::locale test_std_convert.cpp test will fail to compile due to an unreachable code warning:  
  
```  
 ...\boost_1_82_0\libs\locale\test\test_std_formatting.c  
 pp(152) : error C2220: warning treated as error - no 'object' file generated  
 ...\boost_1_82_0\libs\locale\test\test_std_formatting.c  
 pp(152) : warning C4702: unreachable code  
```  
  
Test Platform:  
* Windows 10 version 22H2  
* Windows Python, for the ICI build  
* Visual Studio 2017 Community Edition version 15.9.55  
* ICU 71.1  
* Boost 1.82.0  
* boost.locale.icu=on boost.locale.winapi=on  
  
Compiling the boost::locale tests with `cxxflags="\wd4702"` works around the problem.  
  
Here is a Cygwin bash script that downloads icu and boost, compiles both, and will as with the unreachable code warning noted above.  
  
````  
#! /bin/bash -x  
  
# Download icu and boost, build icu, build boost --with-locale, and   
# build the boost::locale tests.  
#  
# The boost::locale test_std_formatting.cpp file, and other test files, will fail to  
# compile due to an unreachable code warning:  
#  
# ...\boost_1_82_0\libs\locale\test\test_std_formatting.c  
# pp(152) : error C2220: warning treated as error - no 'object' file generated  
# ...\boost_1_82_0\libs\locale\test\test_std_formatting.c  
# pp(152) : warning C4702: unreachable code  
#  
# Compiling the boost::locale tests with cxxflags="\wd4702" works around the  
# problem by disabling the unreachable code warning.  
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
  
DIR=boost_locale_windows_unreachable_warning.$(date "+%m-%d-%y_%H-%M-%S").d  
mkdir $DIR  
cd $DIR  
  
# setup log  
exec > >(tee boost_locale_windows_unreachable_warning.log)  
exec 2> >(tee -a boost_locale_windows_unreachable_warning.log)  
  
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
          boost.locale.icu=on boost.locale.iconv=off boost.locale.winapi=on boost.locale.std=off boost.locale.posix=off \  
          --with-locale \  
          toolset="msvc-14.1" \  
          architecture=x86 \  
          address-model=64  
  
    # This fails with "error C2220: warning treated as error" do to  
    # "warning C4702: unreachable code" when compiling  
    # test_std_formatting.cpp. Compiling with cxxflags="\wd4702" works  
    # around the problem.  
    unset TZ;  
    wincmd b2 debug -j4 \  
          -sICU_PATH=$(cygpath -w "${ICU_PATH}") \  
          boost.locale.icu=on boost.locale.iconv=off boost.locale.winapi=on boost.locale.std=off boost.locale.posix=off \  
          --with-locale \  
          toolset="msvc-14.1" \  
          architecture=x86 \  
          address-model=64 \  
          libs/locale/test  
)  
  
# clear error trap  
trap - INT TERM EXIT  
  
echo PASSED  
````

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-06-18 07:25:40 UTC  
> Url: https://github.com/boostorg/locale/issues/180#issuecomment-1596004422  

Thanks for the report, looks like we don't test with disabled std backend which although possible might not be so useful in practice and hence wasn't included in CI.  
  
Will be fixed.
