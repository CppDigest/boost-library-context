# #7 - Tests failing on Windows with MSVC and GCC [Open]

> Username: adambadura  
> Created at: 2018-03-08 01:32:58 UTC  
> Updated at: 2019-09-30 06:16:17 UTC  
> Url: https://github.com/boostorg/bimap/issues/7  

On my Windows 10, with MS Visual Studio 2017 and MinGW with GCC 7.2 I have installed Boost following steps on https://github.com/boostorg/boost/wiki/Getting-Started:  
  
```  
git clone --recursive https://github.com/boostorg/boost.git  
cd boost  
git checkout develop  
.\bootstrap  
.\b2 headers  
```  
  
Then I have added `b2` to my `PATH` and done this:  
```  
cd libs\bimap\test  
b2 # for MSVC  
b2 toolset=gcc # for GCC  
```  
  
Sadly in both cases some tests failed. Different tests with both cases. Below console outputs are from a second run of `b2` to take out staff that is OK:  
```  
C:\Boost\boost\libs\bimap\test>b2  
..\..\..\libs\log\build\Jamfile.v2:45: Unescaped special character in argument <define>$(flag)=1  
C:/Boost/boost/libs/predef/check/../tools/check\predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...patience...  
...patience...  
...found 5709 targets...  
...updating 3 targets...  
msvc.write-setup-script ..\..\..\bin.v2\standalone\msvc\msvc-14.1\msvc-setup.bat  
testing.capture-output ..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.run  
====== BEGIN OUTPUT ======  
C:\Boost\boost\libs/bimap/test/test_bimap.hpp(417): test const_c.bucket_size(const_c.bucket(di->first)) == 1 failed in function: 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
C:\Boost\boost\libs/bimap/test/test_bimap.hpp(417): test const_c.bucket_size(const_c.bucket(di->first)) == 1 failed in function: 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
C:\Boost\boost\libs/bimap/test/test_bimap.hpp(417): test const_c.bucket_size(const_c.bucket(di->first)) == 1 failed in function: 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct boost::bimaps::relation::member_at::right,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<char,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
C:\Boost\boost\libs/bimap/test/test_bimap.hpp(417): test const_c.bucket_size(const_c.bucket(di->first)) == 1 failed in function: 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
C:\Boost\boost\libs/bimap/test/test_bimap.hpp(417): test const_c.bucket_size(const_c.bucket(di->first)) == 1 failed in function: 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
C:\Boost\boost\libs/bimap/test/test_bimap.hpp(417): test const_c.bucket_size(const_c.bucket(di->first)) == 1 failed in function: 'void __cdecl test_pair_unordered_associative_container<class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> >,class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > >>(class boost::bimaps::views::unordered_multimap_view<struct right_tag,class boost::bimaps::detail::bimap_core<struct boost::bimaps::unordered_set_of<struct boost::bimaps::tags::tagged<char,struct left_tag>,struct boost::hash<char>,struct std::equal_to<char> >,struct boost::bimaps::unordered_multiset_of<struct boost::bimaps::tags::tagged<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,struct right_tag>,struct boost::hash<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,struct std::equal_to<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > > >,struct boost::mpl::na,struct boost::mpl::na,struct boost::mpl::na> > &,const class std::map<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> >,char,struct std::less<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > >,class std::allocator<struct std::pair<class std::basic_string<char,struct std::char_traits<char>,class std::allocator<char> > const ,char> > > &)'  
  
**** 6 errors detected  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.exe"   > "..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.output" 2>&1  
    set status=%ERRORLEVEL%  
    echo. >> "..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.output"  
    echo EXIT STATUS: %status% >> "..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.output"  
    if %status% EQU 0 (  
        copy "..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.output" "..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\..\..\bin.v2\libs\bimap\test\test_bimap_unordered.test\msvc-14.1\debug\threading-multi\test_bimap_unordered.run...  
...failed updating 1 target...  
...skipped 1 target...  
...updated 2 targets...  
```  
  
The same with GCC:  
```  
C:\Boost\boost\libs\bimap\test>b2 toolset=gcc  
..\..\..\libs\log\build\Jamfile.v2:45: Unescaped special character in argument <define>$(flag)=1  
C:/Boost/boost/libs/predef/check/../tools/check\predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
...patience...  
...patience...  
...patience...  
...found 5660 targets...  
...updating 2 targets...  
testing.capture-output ..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.run  
====== BEGIN OUTPUT ======  
..\..\../boost/test/minimal.hpp(136): exception "boost::archive::archive_exception: output stream error" caught in function: 'int main(int, char**)'  
  
**** Testing aborted.  
**** 1 error detected  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
    set Path=C:\Boost\boost\bin.v2\libs\serialization\build\gcc-7.2.0\debug;C:\MinGW\bin;C:\MinGW\lib;C:\MinGW\lib32;C:\MinGW\lib64;%Path%  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.exe"   > "..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.output" 2>&1  
    set status=%ERRORLEVEL%  
    echo. >> "..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.output"  
    echo EXIT STATUS: %status% >> "..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.output"  
    if %status% EQU 0 (  
        copy "..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.output" "..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\..\..\bin.v2\libs\bimap\test\test_bimap_serialization.test\gcc-7.2.0\debug\test_bimap_serialization.run...  
...failed updating 1 target...  
...skipped 1 target...  
```  
  
Am I doing something wrong? How should I proceed to have the tests running clean (as I expected they should)?

---

## Comment 1

> Username: eldiener  
> Created at: 2018-12-20 15:31:14 UTC  
> Url: https://github.com/boostorg/bimap/issues/7#issuecomment-449036785  

The 'unescaped special character.." error no longer occurs but the failures in testing test_bimap_unordered do occur for me also.

---

## Comment 2

> Username: Kojoley  
> Created at: 2019-04-03 21:24:00 UTC  
> Url: https://github.com/boostorg/bimap/issues/7#issuecomment-479664416  

The `test_bimap_unordered` test failures seem to happen exclusively on 32bit.

---

## Comment 3

> Username: yuxianch  
> Created at: 2019-09-30 06:16:17 UTC  
> Url: https://github.com/boostorg/bimap/issues/7#issuecomment-536417677  

This test got errors both in Windows and Linux 32-bit mode.
