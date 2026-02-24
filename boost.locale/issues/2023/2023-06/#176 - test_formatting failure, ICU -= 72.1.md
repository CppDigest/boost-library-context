# #176 - test_formatting failure, ICU >= 72.1 [Closed]

> Username: jptrainor  
> Created at: 2023-06-06 14:42:36 UTC  
> Updated at: 2023-06-21 07:45:48 UTC  
> Closed at: 2023-06-21 07:45:48 UTC  
> Url: https://github.com/boostorg/locale/issues/176  

The test_formatting.cpp unit test fails for boost 1.82.0 and for ICU version greater than or equal to 72.1 due to a change in ICU time formatting. Date-time conversion string compares fail because ICU now uses a three byte UTF-8 [narrow no-break space](https://www.fileformat.info/info/unicode/char/202f/index.htm) character where before an ordinary single byte space character was used.  
  
Note that other projects have experience similar unit test failures do to this ICU change, for example: [node](https://github.com/nodejs/node/issues/46123).  
  
Here is the failure output:  
  
```  
testing.capture-output bin.v2/libs/locale/test/test_formatting.test/clang-darwin-14/debug/threadapi-pthread/threading-multi/visibility-hidden/test_formatting.run  
====== BEGIN OUTPUT ======  
Testing char, UTF-8  
Error in line 319:   
---- [3:33:13 PM] != [3:33:13 PM]  
Error in line 320:   
---- [3:33 PM] != [3:33 PM]  
Error in line 321:   
---- [3:33:13 PM] != [3:33:13 PM]  
Error in line 322:   
---- [3:33:13 PM GMT] != [3:33:13 PM GMT]  
Error in line 330:   
---- [3:33:13 PM Greenwich Mean Time] != [3:33:13 PM Greenwich Mean Time]  
Error in line 334:   
---- [4:33:13 PM] != [4:33:13 PM]  
Error in line 335:   
---- [4:33 PM] != [4:33 PM]  
Error in line 341:   
---- [4:33:13 PM] != [4:33:13 PM]  
Error in line 362:   
---- [4:33:13 PM GMT+1] != [4:33:13 PM GMT+1]  
Error in line 369:   
---- [4:33:13 PM GMT+01:00] != [4:33:13 PM GMT+01:00]  
Error in line 372:   
---- [Feb 5, 1970, 3:33:13 PM] != [Feb 5, 1970, 3:33:13 PM]  
Error in line 379:   
---- [2/5/70, 3:33 PM] != [2/5/70, 3:33 PM]  
Error in line 385:   
---- [Feb 5, 1970, 3:33:13 PM] != [Feb 5, 1970, 3:33:13 PM]  
Error in line 391:   
---- [February 5, 1970 at 3:33:13 PM GMT] != [February 5, 1970 at 3:33:13 PM GMT]  
Error in line 399:   
---- [Thursday, February 5, 1970 at 3:33:13 PM Greenwich Mean Time] != [Thursday, February 5, 1970 at 3:33:13 PM Greenwich Mean Time]  
Test: %a  
Test: %A  
Test: %b  
Test: %B  
Test: %c  
Error in line 439: ss.str() == to<CharType>(mark_result.second)  
---- [Thursday, February 5, 1970 at 3:33:13 PM Greenwich Mean Time] != [Thursday, February 5, 1970 at 3:33:13 PM Greenwich Mean Time]  
Test: %d  
Test: %e  
Test: %h  
Test: %H  
Test: %I  
Test: %j  
Test: %m  
Test: %M  
Test: %n  
Test: %p  
Test: %r  
Test: %R  
Test: %S  
Test: %t  
Test: %T  
Test: %x  
Test: %X  
Error in line 439: ss.str() == to<CharType>(mark_result.second)  
---- [3:33:13 PM] != [3:33:13 PM]  
Test: %y  
Test: %Y  
Test: %Z  
Test: %%  
Test: 'Now is %A, %H o'clo''ck ' or not ' '  
Test: ''test %H''  
Test: '%H''  
Test: ''%H''  
Error in line 660: Format: {1,time,gmt}  
---- [3:33:13 PM] != [3:33:13 PM]  
Error in line 661: Format: {1,datetime,gmt}  
---- [Feb 5, 1970, 3:33:13 PM] != [Feb 5, 1970, 3:33:13 PM]  
Error in line 662: Format: {1,dt,gmt}  
---- [Feb 5, 1970, 3:33:13 PM] != [Feb 5, 1970, 3:33:13 PM]  
Error in line 664: Format: {1,time=short,gmt}  
---- [3:33 PM] != [3:33 PM]  
Error in line 665: Format: {1,time=s,gmt}  
---- [3:33 PM] != [3:33 PM]  
Failed Error limits reached, stopping unit test  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
Here is script that you can run on MacOS that will download ICU 73.1 and Boost 1.82.0, build and test ICU, build boost, and run the locale tests. The boost locale tests are expected to fail, as shown above.  
  
```  
#! /bin/zsh -x  
  
# Download icu and boost to boost_local_test.d directory. Build icu  
# and run tests. Build boost --with-locale and run locale tests.  
#  
# Expect results: PASS for ICU <= 71.1, FAIL for ICU >= 72.1  
#  
# The test failure is due to the introduction of the narrow  
# non-breaking space characters in date/time conversions in icu 72.1.  
#  
# Tested with: MacOS 13.4, XCode 14.3.1  
#  
# Log file is written to: boost_locale_test.log  
  
set -e  
  
# setup error trap  
function ErrorTrap {  
    echo FAILED  
}  
trap ErrorTrap INT TERM EXIT  
  
mkdir boost_locale_test.d  
cd    boost_locale_test.d  
  
# setup log  
exec > >(tee boost_locale_test.log)  
exec 2> >(tee -a boost_locale_test.log)  
  
ICU_VER="73.1"  
ICU_RELEASE=release-$(echo $ICU_VER | sed s/\\./-/)  
ICU4C_SRC=icu4c-$(echo $ICU_VER | sed s/\\./_/)-src  
  
BOOST_VER="1.82.0"  
BOOST_SRC=boost_$(echo $BOOST_VER | sed s/\\./_/g)  
  
JOBS=$(sysctl -n hw.ncpu)  
INSTDIR=$(pwd)/install  
  
# get icu  
wget -q "https://github.com/unicode-org/icu/releases/download/${ICU_RELEASE}/${ICU4C_SRC}.tgz"  
tar xzf "${ICU4C_SRC}".tgz  
mv icu "${ICU4C_SRC}"  
  
# get boost  
wget -q "https://boostorg.jfrog.io/artifactory/main/release/"${BOOST_VER}"/source/"${BOOST_SRC}".tar.gz"  
tar xzf "${BOOST_SRC}".tar.gz  
  
# build icu  
(  
  cd "${ICU4C_SRC}"/source  
  CXXFLAGS=-std=c++11 ./runConfigureICU MacOSX --enable-static --prefix="${INSTDIR}"  
  for target in check install  
  do  
      gnumake -j${JOBS} CXXFLAGS=-std=c++11 $target  
  done  
)  
  
# build boost  
(  
  cd "${BOOST_SRC}"  
  ./bootstrap.sh --prefix="${INSTDIR}"  
  for target in stage libs/locale/test  
  do  
      ./b2  -j${JOBS} -sICU_PATH="${INSTDIR}" boost.locale.icu=on boost.locale.iconv=off boost.locale.winapi=off boost.locale.std=off boost.locale.posix=off --with-locale cxxflags=-std=c++11 toolset=clang $target  
  done  
)  
  
# clear error trap  
trap - INT TERM EXIT  
  
echo PASSED  
```
