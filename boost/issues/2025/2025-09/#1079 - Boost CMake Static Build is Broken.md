# #1079 - Boost CMake Static Build is Broken [Open]

> Username: 0xFFFC0000  
> Created at: 2025-09-04 18:40:12 UTC  
> Updated at: 2025-09-08 07:48:47 UTC  
> Url: https://github.com/boostorg/boost/issues/1079  

When building static Boost with CMake on MSYS2/MinGW, the script is totally broken.  
  
For example, as you can see from the log below, it builds Boost statically, but it finds and links against `.dll.a` files, which are shared-library stubs on MinGW. I encountered this issue while debugging the CI for the Monero project and noticed that MinGW/MSYS2 is building Boost with CMake. Monero CI builds Monero statically and uses Boost.Locale. Although Boost.Locale is compiled statically, it depends on the ICU dynamic library (`.dll.a` and `.dll`), which defeats the whole purpose of a static build. I debugged it and discovered that, regardless of the options passed to the Boost build, it uses `.dll.a` import libraries and does not respect the static-build setting on MSYS/MinGW  
  
  
These three lines from the logs are important:   
```  
-- Found ZLIB: C:/msys64/mingw64/lib/libz.dll.a (found version "1.3.1")  
-- Found BZip2: C:/msys64/mingw64/lib/libbz2.dll.a (found version "1.0.8")  
-- Found LibLZMA: C:/msys64/mingw64/lib/liblzma.dll.a (found version "5.8.1")  
```  
  
  
Full log:  
```  
$ cd  /home/user/dev/boost-1.89.0/ && rm -rf /home/user/dev/boost-1.89.0/build/ && mk  
dir  -p /home/user/dev/boost-1.89.0/build && cd  /home/user/dev/boost-1.89.0/build &&  
 cmake.exe      -G "Ninja"       -DBUILD_SHARED_LIBS="OFF"      -DBOOST_INSTALL_LAYOUT=  
tagged      -DBOOST_STACKTRACE_ENABLE_FROM_EXCEPTION=OFF            -DBUILD_TESTING=OFF  
      -S /home/user/dev/boost-1.89.0/      -B /home/user/dev/boost-1.89.0/build -DBoo  
st_USE_STATIC_LIB=ON -DBoost_USE_STATIC_LIBS=1  
-- The CXX compiler identification is GNU 15.2.0  
-- Detecting CXX compiler ABI info  
-- Detecting CXX compiler ABI info - done  
-- Check for working CXX compiler: C:/msys64/mingw64/bin/c++.exe - skipped  
-- Detecting CXX compile features  
-- Detecting CXX compile features - done  
-- Boost: using tagged layout: include, bin, lib, lib/cmake  
-- Boost: Release build, static libraries, MPI OFF, Python OFF, testing OFF  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD  
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success  
-- Found Threads: TRUE  
-- Performing Test BOOST_ATOMIC_TARGET_X86  
-- Performing Test BOOST_ATOMIC_TARGET_X86 - Success  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE2  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE2 - Success  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE41  
-- Performing Test BOOST_ATOMIC_COMPILER_HAS_SSE41 - Success  
-- Performing Test BOOST_ATOMIC_HAS_PTHREAD_COND_CLOCKWAIT  
-- Performing Test BOOST_ATOMIC_HAS_PTHREAD_COND_CLOCKWAIT - Failed  
-- Performing Test BOOST_CHARCONV_QUADMATH_FOUND  
-- Performing Test BOOST_CHARCONV_QUADMATH_FOUND - Success  
-- Boost.Charconv: quadmath support ON  
-- Found OpenSSL: C:/msys64/mingw64/lib/libcrypto.dll.a (found version "3.5.2")  
-- The C compiler identification is GNU 15.2.0  
-- Detecting C compiler ABI info  
-- Detecting C compiler ABI info - done  
-- Check for working C compiler: C:/msys64/mingw64/bin/cc.exe - skipped  
-- Detecting C compile features  
-- Detecting C compile features - done  
-- Boost.Context: architecture x86_64, binary format pe, ABI ms, assembler gas, suffix .asm, implementation fcontext  
-- The ASM compiler identification is GNU  
-- Found assembler: C:/msys64/mingw64/bin/cc.exe  
-- Boost.Fiber: NUMA target OS is windows  
-- Performing Test BOOST_FILESYSTEM_HAS_INIT_PRIORITY  
-- Performing Test BOOST_FILESYSTEM_HAS_INIT_PRIORITY - Success  
-- Performing Test BOOST_FILESYSTEM_HAS_CXX20_ATOMIC_REF  
-- Performing Test BOOST_FILESYSTEM_HAS_CXX20_ATOMIC_REF - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BLKSIZE - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIM  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIM - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMENSEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMENSEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_MTIMESPEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMENSEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMENSEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC  
-- Performing Test BOOST_FILESYSTEM_HAS_STAT_ST_BIRTHTIMESPEC - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX_SYSCALL  
-- Performing Test BOOST_FILESYSTEM_HAS_STATX_SYSCALL - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW  
-- Performing Test BOOST_FILESYSTEM_HAS_FDOPENDIR_NOFOLLOW - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_DIRENT_D_TYPE  
-- Performing Test BOOST_FILESYSTEM_HAS_DIRENT_D_TYPE - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_POSIX_AT_APIS  
-- Performing Test BOOST_FILESYSTEM_HAS_POSIX_AT_APIS - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_FALLOCATE  
-- Performing Test BOOST_FILESYSTEM_HAS_FALLOCATE - Failed  
-- Performing Test BOOST_FILESYSTEM_HAS_BCRYPT  
-- Performing Test BOOST_FILESYSTEM_HAS_BCRYPT - Success  
-- Found ZLIB: C:/msys64/mingw64/lib/libz.dll.a (found version "1.3.1")  
-- Found BZip2: C:/msys64/mingw64/lib/libbz2.dll.a (found version "1.0.8")  
-- Found LibLZMA: C:/msys64/mingw64/lib/liblzma.dll.a (found version "5.8.1")  
-- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS  
-- Performing Test BOOST_IOSTREAMS_HAS_LZMA_CPUTHREADS - Success  
-- Boost.Iostreams: ZLIB ON, BZip2 ON, LZMA ON (multithreaded), Zstd ON  
```  
  
And this output shows static version of those libraries are available, but CMake does not find them:   
  
```  
user@DESKTOP MINGW64 ~  
$ ls -hall /mingw64/lib/libz.a  
-rw-r--r-- 1 user user 107K Jan 22  2024 /mingw64/lib/libz.a  
  
user@DESKTOP MINGW64 ~  
$ ls -hall /mingw64/lib/libbz2.a  
-rw-r--r-- 1 user user 72K Sep 26  2023 /mingw64/lib/libbz2.a  
  
user@DESKTOP MINGW64 ~  
$ ls -hall /mingw64/lib/liblzma.a  
-rw-r--r-- 1 user user 298K Apr 11 05:15 /mingw64/lib/liblzma.a  
```  
  
1. https://github.com/monero-project/monero/actions/runs/17355393056/job/49267581759#step:6:14433  
2. https://github.com/msys2/MINGW-packages/blob/30aa64090795cf43e58e209603a5e92515ec543d/mingw-w64-boost/PKGBUILD#L84

---

## Comment 1

> Username: 0xFFFC0000  
> Created at: 2025-09-07 08:53:46 UTC  
> Url: https://github.com/boostorg/boost/issues/1079#issuecomment-3263599510  

The very fact that we have `__imp_` symbols inside an `.a` does show the compilation is broken :   
  
```  
$ nm -g  /mingw64/lib/libboost_locale-mt.a | grep __imp  
                 U __imp__errno  
                 U __imp__ZN6icu_7713UnicodeString6appendEi  
                 U __imp__ZN6icu_7713UnicodeStringC1Eiii  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKciP10UConverterR10UErrorCode  
                 U __imp__ZN6icu_7713UnicodeStringD1Ev  
                 U __imp_GetLastError  
                 U __imp_IsDBCSLeadByteEx  
                 U __imp_MultiByteToWideChar  
                 U __imp_WideCharToMultiByte  
                 U __imp_CloseHandle  
                 U __imp_CreateEventA  
                 U __imp_FormatMessageA  
                 U __imp_FormatMessageW  
                 U __imp_LocalFree  
                 U __imp_SetEvent  
                 U __imp_WaitForSingleObjectEx  
                 U __imp_WideCharToMultiByte  
                 U __imp_CloseHandle  
                 U __imp_CreateEventA  
                 U __imp_FormatMessageA  
                 U __imp_FormatMessageW  
                 U __imp_LocalFree  
                 U __imp_SetEvent  
                 U __imp_WaitForSingleObjectEx  
                 U __imp_WideCharToMultiByte  
                 U __imp__errno  
                 U __imp_CloseHandle  
                 U __imp_CreateEventA  
                 U __imp_FormatMessageA  
                 U __imp_FormatMessageW  
                 U __imp_LocalFree  
                 U __imp_SetEvent  
                 U __imp_WaitForSingleObjectEx  
                 U __imp_WideCharToMultiByte  
                 U __imp__wfopen  
                 U __imp_GetLocaleInfoA  
                 U __imp_IsValidCodePage  
                 U __imp__ZN6icu_7713BreakIterator18createLineInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_7713BreakIterator18createWordInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_7713BreakIterator22createSentenceInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_7713BreakIterator23createCharacterInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKciP10UConverterR10UErrorCode  
                 U __imp__ZN6icu_7713UnicodeStringD1Ev  
                 U __imp__ZN6icu_776LocaleC1ERKS0_  
                 U __imp__ZN6icu_776LocaleD1Ev  
                 U __imp__ZNK6icu_7713UnicodeString11countChar32Eii  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKciP10UConverterR10UErrorCode  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKDsi  
                 U __imp__ZN6icu_7713UnicodeStringD1Ev  
                 U __imp__ZN6icu_776LocaleC1ERKS0_  
                 U __imp__ZN6icu_776LocaleD1Ev  
                 U __imp__ZN6icu_778Collator14createInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp_FormatMessageA  
                 U __imp_FormatMessageW  
                 U __imp_LocalFree  
                 U __imp_WideCharToMultiByte  
                 U __imp__ZN6icu_7710Normalizer9normalizeERKNS_13UnicodeStringE18UNormalizationModeiRS1_R10UErrorCode  
                 U __imp__ZN6icu_7713UnicodeString7toLowerERKNS_6LocaleE  
                 U __imp__ZN6icu_7713UnicodeString7toTitleEPNS_13BreakIteratorERKNS_6LocaleE  
                 U __imp__ZN6icu_7713UnicodeString7toUpperERKNS_6LocaleE  
                 U __imp__ZN6icu_7713UnicodeString8foldCaseEj  
                 U __imp__ZN6icu_7713UnicodeStringaSERKS0_  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKciP10UConverterR10UErrorCode  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKDsi  
                 U __imp__ZN6icu_7713UnicodeStringD1Ev  
                 U __imp__ZN6icu_776LocaleC1ERKS0_  
                 U __imp__ZN6icu_776LocaleD1Ev  
                 U __imp__ZTVN6icu_7713UnicodeStringE  
                 U __imp__ZN6icu_7713UnicodeStringaSERKS0_  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKc  
                 U __imp__ZN6icu_7713UnicodeStringD1Ev  
                 U __imp__ZN6icu_7717GregorianCalendar16getStaticClassIDEv  
                 U __imp__ZN6icu_776LocaleC1ERKS0_  
                 U __imp__ZN6icu_776LocaleD1Ev  
                 U __imp__ZN6icu_778Calendar13adoptTimeZoneEPNS_8TimeZoneE  
                 U __imp__ZN6icu_778Calendar14createInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_778Calendar15setTimeInMillisEdR10UErrorCode  
                 U __imp__ZN6icu_778Calendar3setE19UCalendarDateFieldsi  
                 U __imp__ZN6icu_778TimeZone13createDefaultEv  
                 U __imp__ZN6icu_778TimeZone14createTimeZoneERKNS_13UnicodeStringE  
                 U __imp__ZNK6icu_778Calendar11getTimeZoneEv  
                 U __imp__ZNK6icu_778Calendar15getTimeInMillisER10UErrorCode  
                 U __imp__ZNK6icu_778Calendar17getFirstDayOfWeekER10UErrorCode  
                 U __imp__ZNK6icu_778Calendar3getE19UCalendarDateFieldsR10UErrorCode  
                 U __imp__ZTVN6icu_7713UnicodeStringE  
                 U __imp_CloseHandle  
                 U __imp_CreateEventA  
                 U __imp_FormatMessageA  
                 U __imp_FormatMessageW  
                 U __imp_LocalFree  
                 U __imp_SetEvent  
                 U __imp_WaitForSingleObjectEx  
                 U __imp_WideCharToMultiByte  
                 U __imp__ZN6icu_7710DateFormat18createDateInstanceENS0_6EStyleERKNS_6LocaleE  
                 U __imp__ZN6icu_7710DateFormat18createTimeInstanceENS0_6EStyleERKNS_6LocaleE  
                 U __imp__ZN6icu_7710DateFormat22createDateTimeInstanceENS0_6EStyleES1_RKNS_6LocaleE  
                 U __imp__ZN6icu_7711Formattable16getDecimalNumberER10UErrorCode  
                 U __imp__ZN6icu_7711FormattableC1Ev  
                 U __imp__ZN6icu_7711FormattableD1Ev  
                 U __imp__ZN6icu_7713ParsePositionD1Ev  
                 U __imp__ZN6icu_7713UnicodeString8doAppendEPKDsii  
                 U __imp__ZN6icu_7713UnicodeString8doAppendERKS0_ii  
                 U __imp__ZN6icu_7713UnicodeStringaSEOS0_  
                 U __imp__ZN6icu_7713UnicodeStringaSERKS0_  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKc  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKciP10UConverterR10UErrorCode  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKDsi  
                 U __imp__ZN6icu_7713UnicodeStringC1ERKS0_  
                 U __imp__ZN6icu_7713UnicodeStringD1Ev  
                 U __imp__ZN6icu_7716SimpleDateFormatC1ERKNS_13UnicodeStringERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_776LocaleD1Ev  
                 U __imp__ZN6icu_777UMemorydlEPv  
                 U __imp__ZN6icu_777UMemorynwEy  
                 U __imp__ZN6icu_778TimeZone13createDefaultEv  
                 U __imp__ZN6icu_778TimeZone14createTimeZoneERKNS_13UnicodeStringE  
                 U __imp__ZNK6icu_7710DateFormat5parseERKNS_13UnicodeStringERNS_13ParsePositionE  
                 U __imp__ZNK6icu_7710DateFormat6formatEdRNS_13UnicodeStringE  
                 U __imp__ZNK6icu_7711Formattable7getLongER10UErrorCode  
                 U __imp__ZNK6icu_7711Formattable8getInt64ER10UErrorCode  
                 U __imp__ZNK6icu_7711Formattable9getDoubleER10UErrorCode  
                 U __imp__ZNK6icu_7711Formattable9isNumericEv  
                 U __imp__ZNK6icu_7712NumberFormat23getMaximumIntegerDigitsEv  
                 U __imp__ZNK6icu_7712NumberFormat6formatEdRNS_13UnicodeStringE  
                 U __imp__ZNK6icu_7712NumberFormat6formatEiRNS_13UnicodeStringE  
                 U __imp__ZNK6icu_7712NumberFormat6formatExRNS_13UnicodeStringE  
                 U __imp__ZNK6icu_7713UnicodeString11countChar32Eii  
                 U __imp__ZTVN6icu_7713ParsePositionE  
                 U __imp__ZTVN6icu_7713UnicodeStringE  
                 U __imp__ZN6icu_7710DateFormat18createDateInstanceENS0_6EStyleERKNS_6LocaleE  
                 U __imp__ZN6icu_7710DateFormat18createTimeInstanceENS0_6EStyleERKNS_6LocaleE  
                 U __imp__ZN6icu_7710DateFormat22createDateTimeInstanceENS0_6EStyleES1_RKNS_6LocaleE  
                 U __imp__ZN6icu_7712NumberFormat14createInstanceERKNS_6LocaleE18UNumberFormatStyleR10UErrorCode  
                 U __imp__ZN6icu_7712NumberFormat14createInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_7712NumberFormat21createPercentInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_7712NumberFormat24createScientificInstanceERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_7713UnicodeStringaSEOS0_  
                 U __imp__ZN6icu_7713UnicodeStringC1EPKc  
                 U __imp__ZN6icu_7713UnicodeStringC1ERKS0_  
                 U __imp__ZN6icu_7713UnicodeStringD1Ev  
                 U __imp__ZN6icu_7716SimpleDateFormat16getStaticClassIDEv  
                 U __imp__ZN6icu_7721RuleBasedNumberFormatC1ENS_15URBNFRuleSetTagERKNS_6LocaleER10UErrorCode  
                 U __imp__ZN6icu_776LocaleC1ERKS0_  
                 U __imp__ZN6icu_776LocaleD1Ev  
                 U __imp__ZN6icu_777UMemorydlEPv  
                 U __imp__ZN6icu_777UMemorynwEy  
                 U __imp__ZTVN6icu_7713UnicodeStringE  
                 U __imp__ZN6icu_776Locale15createCanonicalEPKc  
                 U __imp__ZN6icu_776LocaleaSEOS0_  
                 U __imp__ZN6icu_776LocaleC1Ev  
                 U __imp__ZN6icu_776LocaleD1Ev  
                 U __imp__ZN6icu_776LocaleC1ERKS0_  
                 U __imp__ZN6icu_776LocaleD1Ev  
                 U __imp_GetLocaleInfoA  
                 U __imp_CompareStringW  
                 U __imp_LCMapStringW  
                 U __imp_FoldStringW  
                 U __imp_LCMapStringW  
                 U __imp_GetCurrencyFormatW  
                 U __imp_GetDateFormatW  
                 U __imp_GetLocaleInfoW  
                 U __imp_GetTimeFormatW  
                 U __imp_CloseHandle  
                 U __imp_CreateEventA  
                 U __imp_EnumSystemLocalesA  
                 U __imp_FormatMessageA  
                 U __imp_FormatMessageW  
                 U __imp_GetLocaleInfoA  
                 U __imp_LocalFree  
                 U __imp_SetEvent  
                 U __imp_WaitForSingleObjectEx  
                 U __imp_WideCharToMultiByte  
```  
  
The `__imp_` does represent pointers to imported functions or variables from a DLL.

---

## Comment 2

> Username: lhmouse  
> Created at: 2025-09-08 07:48:35 UTC  
> Updated at: 2025-09-08 07:48:47 UTC  
> Url: https://github.com/boostorg/boost/issues/1079#issuecomment-3265028872  

It's because this macro is missing in CMakeLists.txt (it exists in Jamfile only; pity):  
  
```  
$ grep -Fnr U_STATIC_IMPLEMENTATION boost-1.89.0/libs/locale  
boost-1.89.0/libs/locale/build/Jamfile.v2:189:  <runtime-link>static:<define>U_STATIC_IMPLEMENTATION=1  
```
