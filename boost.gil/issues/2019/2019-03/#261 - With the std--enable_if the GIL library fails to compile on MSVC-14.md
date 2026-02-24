# #261 - With the std::enable_if the GIL library fails to compile on MSVC-14: [Closed]

> Username: apolukhin  
> Created at: 2019-03-18 19:02:46 UTC  
> Updated at: 2019-03-20 09:03:02 UTC  
> Closed at: 2019-03-19 19:34:12 UTC  
> Url: https://github.com/boostorg/gil/issues/261  

### Build error:  
```  
C:\boost-local\boost/gil/algorithm.hpp(424): error C2988: unrecognizable template declaration/definition  
C:\boost-local\boost/gil/algorithm.hpp(424): error C2143: syntax error: missing ',' before '*'  
C:\boost-local\boost/gil/algorithm.hpp(424): error C4430: missing type specifier - int assumed. Note: C++ does not support default-int  
C:\boost-local\boost/gil/extension/toolbox/metafunctions/get_num_bits.hpp(63): error C2039: 'type': is not a member of 'std::enable_if<false,_Ty>'  
        with  
        [  
            _Ty=void  
        ]  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\type_traits(1877): note: see declaration of 'std::enable_if<false,_Ty>'  
        with  
        [  
            _Ty=void  
        ]  
C:\boost-local\boost/gil/extension/toolbox/metafunctions/get_num_bits.hpp(64): error C3203: 'type': unspecialized class template can't be used as a template argument for template parameter '<unnamed-symbol>', expected a real type  
C:\boost-local\boost/gil/io/row_buffer_helper.hpp(60): error C2039: 'type': is not a member of 'std::enable_if<false,_Ty>'  
        with  
        [  
            _Ty=void  
        ]  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\type_traits(1877): note: see declaration of 'std::enable_if<false,_Ty>'  
        with  
        [  
            _Ty=void  
        ]  
C:\boost-local\boost/gil/io/row_buffer_helper.hpp(62): error C3203: 'type': unspecialized class template can't be used as a template argument for template parameter 'DummyT', expected a real type  
C:\boost-local\boost/gil/io/row_buffer_helper.hpp(188): error C2039: 'type': is not a member of 'std::enable_if<false,_Ty>'  
        with  
        [  
            _Ty=void  
        ]  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\INCLUDE\type_traits(1877): note: see declaration of 'std::enable_if<false,_Ty>'  
...  
```  
  
Full build logs: https://ci.appveyor.com/project/apolukhin/boost-cookbook/branch/second_edition/job/db2wlari8d653c66#L3208  
  
### Minimal Working Example (in C++)  
Test program is available at https://github.com/apolukhin/Boost-Cookbook/blob/second_edition/Chapter12/07_gil/main.cpp  
  
### Actual behavior  
Does not compile  
  
### Expected  behavior  
Compiles  
  
### Environment  
  
All relevant information like:  
  
- Boost version (see `<boost/version.hpp>`): 1.70 (from develop)  
- Compiler version: msvc-14  
- Build settings: default

---

## Comment 1

> Username: mloskot  
> Created at: 2019-03-18 21:15:22 UTC  
> Updated at: 2019-03-18 21:15:32 UTC  
> Url: https://github.com/boostorg/gil/issues/261#issuecomment-474104049  

I think, for one of the errors, this (and alike)  
  
https://github.com/boostorg/gil/blob/54f1817e991c44e42c50fcbf91ad95a37f965a19/include/boost/gil/extension/toolbox/metafunctions/get_num_bits.hpp#L52-L66  
  
should be corrected so `::type::value` reads `::value`.  
  
Unfortunately, I have no access to VS2015 to test it out.  
  
There also were, and still may be, missing MPL headers. I have just fixed ones for `get_num_bits` in https://github.com/boostorg/gil/commit/af8dcac47e49006e92d82cda7a9f1aab93b2d8b3

---

## Comment 2

> Username: mloskot  
> Created at: 2019-03-19 19:35:37 UTC  
> Url: https://github.com/boostorg/gil/issues/261#issuecomment-474545067  

@apolukhin Hopefully #262 fixes contributed by @Kojoley will fix the build for you too.

---

## Comment 3

> Username: mloskot  
> Created at: 2019-03-20 08:03:28 UTC  
> Url: https://github.com/boostorg/gil/issues/261#issuecomment-474726420  

I'm adding VS2015 builds to GIL's Azure Pilpelines in #263   
ATM, the `ml/azp-add-vs2015-build` branch there does not include @Kojoley's fixes from #262 and, interestingly, the build succeeds without errors (https://dev.azure.com/boostorg/gil/_build/results?buildId=280)  
  
```  
-- Building for: Visual Studio 14 2015  
-- The CXX compiler identification is MSVC 19.0.24215.1  
-- Check for working CXX compiler: C:/Program Files (x86)/Microsoft Visual Studio 14.0/VC/bin/cl.exe  
```  
  
```  
C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\CL.exe /c /ID:\a\1\s\include /ID:\a\1\s\test /I"C:\Boost\include\boost-1_68" /W4 /WX- /O2 /Ob2 /Oy- /D WIN32 /D _WINDOWS /D NDEBUG /D _CRT_NONSTDC_NO_DEPRECATE /D _SCL_SECURE_NO_DEPRECATE /D _CRT_SECURE_NO_WARNINGS /D NOMINMAX /D BOOST_CONFIG_SUPPRESS_OUTDATED_MESSAGE /D BOOST_ALL_NO_LIB /D "CMAKE_INTDIR=\"Release\"" /D _MBCS /Gm- /EHsc /MD /GS /fp:precise /Zc:wchar_t /Zc:forScope /Zc:inline /GR /Fo"test_ext_toolbox.dir\Release\\" /Fd"test_ext_toolbox.dir\Release\vc140.pdb" /Gd /TP /analyze- /FC /errorReport:queue  -bigobj  
 D:\a\1\s\toolbox\test\test.cpp   
 D:\a\1\s\toolbox\test\channel_type.cpp   
 D:\a\1\s\toolbox\test\channel_view.cpp   
 D:\a\1\s\toolbox\test\cmyka.cpp   
 D:\a\1\s\toolbox\test\get_num_bits.cpp   
 D:\a\1\s\toolbox\test\get_pixel_type.cpp   
 D:\a\1\s\toolbox\test\gray_alpha.cpp   
 D:\a\1\s\toolbox\test\gray_to_rgba.cpp   
 D:\a\1\s\toolbox\test\hsl_hsv_test.cpp   
 D:\a\1\s\toolbox\test\indexed_image_test.cpp   
 D:\a\1\s\toolbox\test\is_bit_aligned.cpp   
 D:\a\1\s\toolbox\test\is_homogeneous.cpp   
 D:\a\1\s\toolbox\test\is_similar.cpp   
 D:\a\1\s\toolbox\test\lab_test.cpp   
 D:\a\1\s\toolbox\test\pixel_bit_size.cpp   
 D:\a\1\s\toolbox\test\rgb_to_luminance.cpp   
 D:\a\1\s\toolbox\test\xyz_test.cpp  
```  
  
Looks like the test do not cover equivalent specialisations as @apolukhin 's programs, or VS2015 version/update is different, or pre`-std::enable_if` headers from Boost 1.68 installed on AzP are preferred   
(despite this order of include directories `CL.exe /c /ID:\a\1\s\include /ID:\a\1\s\test /I"C:\Boost\include\boost-1_68"` as listed above). I will look into the latter.

---

## Comment 4

> Username: mloskot  
> Created at: 2019-03-20 09:03:01 UTC  
> Url: https://github.com/boostorg/gil/issues/261#issuecomment-474743925  

I have updated #263 to ensure the pre-`std::enable_if` headers from Boost 1.68 installed on AzP are NOT preferred. The build still succeeds.
