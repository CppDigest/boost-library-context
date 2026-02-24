# #37 - uuid will not build with -Wextra -Werror -pedantic in clang/gcc [Closed]

> Username: jeking3  
> Created at: 2017-09-26 19:29:02 UTC  
> Updated at: 2017-11-03 17:17:07 UTC  
> Closed at: 2017-11-03 17:17:07 UTC  
> Url: https://github.com/boostorg/uuid/issues/37  

There are various minor issues that need to be resolved, for example:  
```  
clang-linux.compile.c++.without-pth ../../../bin.v2/libs/uuid/test/test_random_generator.test/clang-linux-4.0.0/debug/test_random_generator.o  
In file included from test_random_generator.cpp:16:  
In file included from ../../../boost/random.hpp:60:  
../../../boost/random/binomial_distribution.hpp:407:9: error: anonymous types declared in an anonymous union are an extension [-Werror,-Wnested-anon-types]  
        struct {  
        ^  
In file included from test_random_generator.cpp:16:  
In file included from ../../../boost/random.hpp:72:  
In file included from ../../../boost/random/negative_binomial_distribution.hpp:21:  
../../../boost/random/poisson_distribution.hpp:338:9: error: anonymous types declared in an anonymous union are an extension [-Werror,-Wnested-anon-types]  
        struct {  
        ^  
2 errors generated.  
```  
  
and  
  
```  
clang-linux.compile.c++.without-pth ../../../bin.v2/libs/uuid/test/~hdr-decl-uuid_generators~hpp.test/clang-linux-4.0.0/debug/~hdr-decl-uuid_generators~hpp.o  
In file included from compile/decl_header.cpp:19:  
In file included from ../../../boost/uuid/uuid_generators.hpp:17:  
In file included from ../../../boost/uuid/random_generator.hpp:12:  
../../../boost/uuid/detail/seed_rng.hpp:226:32: error: cast between pointer-to-function and pointer-to-object is an extension [-Werror,-Wpedantic]  
            sha.process_bytes( (unsigned char const*)&std::rand, sizeof( void(*)() ) );  
                               ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
1 error generated.  
```  
  
and  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/uuid/test/test_msvc_simd_bug981648.test/gcc-6.3.0/release/debug-symbols-on/test_msvc_simd_bug981648_foo.o  
test_msvc_simd_bug981648_foo.cpp: In function â€˜void mp_grid_update_marker_parameters(headerProperty*, my_obj&)â€™:  
test_msvc_simd_bug981648_foo.cpp:22:17: error: variable â€˜old_header_propâ€™ set but not used [-Werror=unused-but-set-variable]  
 headerProperty *old_header_prop = NULL;  
                 ^~~~~~~~~~~~~~~  
cc1plus: all warnings being treated as errors  
  
    "g++"   -O3 -finline-functions -Wno-inline -Wall -pedantic -g -fPIC -m64 -Werror -Wextra -pedantic -DBOOST_ALL_NO_LIB=1 -DNDEBUG  -I"../../.." -c -o "../../../bin.v2/libs/uuid/test/test_msvc_simd_bug981648.test/gcc-6.3.0/release/debug-symbols-on/test_msvc_simd_bug981648_foo.o" "test_msvc_simd_bug981648_foo.cpp"  
  
...failed gcc.compile.c++ ../../../bin.v2/libs/uuid/test/test_msvc_simd_bug981648.test/gcc-6.3.0/release/debug-symbols-on/test_msvc_simd_bug981648_foo.o...  
...failed updating 1 target...  
```  
  
and  
  
```  
gcc.compile.c++ ../../../bin.v2/libs/uuid/test/test_msvc_simd_bug981648.test/gcc-6.3.0/release/debug-symbols-on/test_msvc_simd_bug981648_foo.o  
test_msvc_simd_bug981648_foo.cpp: In function â€˜void mp_grid_update_marker_parameters(headerProperty*, my_obj&)â€™:  
test_msvc_simd_bug981648_foo.cpp:28:60: error: format â€˜%pâ€™ expects argument of type â€˜void*â€™, but argument 2 has type â€˜my_obj*â€™ [-Werror=format=]  
 std::printf("works okay, if it reaches this printf: %p\n",p);  
                                                            ^  
```  
  
also from the msvc-14.1 build in appveyor:  
  
```  
ompile-c-c++ bin.v2\libs\uuid\test\test_io.test\msvc-14.1\debug\link-static\threading-multi\test_io.obj  
test_io.cpp  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.11.25503\include\xutility(2294): warning C4996: 'std::copy::_Unchecked_iterators::_Deprecate': Call to 'std::copy' with parameters that may be unsafe - this call relies on the caller to check that the passed values are correct. To disable this warning, use -D_SCL_SECURE_NO_WARNINGS. See documentation on how to use Visual C++ 'Checked Iterators'  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.11.25503\include\xutility(2294): note: see declaration of 'std::copy::_Unchecked_iterators::_Deprecate'  
C:\projects\boost-root\boost/uuid/uuid_io.hpp(124): note: see reference to function template instantiation '_OutIt std::copy<unsigned char*,boost::uuids::uuid::iterator>(_InIt,_InIt,_OutIt)' being compiled  
        with  
        [  
            _OutIt=boost::uuids::uuid::iterator,  
            _InIt=unsigned char *  
        ]  
libs\uuid\test\test_io.cpp(108): note: see reference to function template instantiation 'std::basic_istream<char,std::char_traits<char>> &boost::uuids::operator >><char,std::char_traits<char>>(std::basic_istream<char,std::char_traits<char>> &,boost::uuids::uuid &)' being compiled  
testing.capture-output bin.v2\libs\uuid\test\test_io.test\msvc-14.1\release\link-static\threading-multi\test_io.run  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2017-10-14 21:55:29 UTC  
> Url: https://github.com/boostorg/uuid/issues/37#issuecomment-336670137  

The second item in the list (cast between pointer and function) will disappear when seed_rng is removed, following completion of work in boost::random::random_device.  
  
I am concerned about removing the unused variable in test_msvc_simd_bug981648_foo.cpp.  I don't know if it's needed for a proper test of the issue.

---

## Comment 2

> Username: jeking3  
> Created at: 2017-11-03 17:16:54 UTC  
> Url: https://github.com/boostorg/uuid/issues/37#issuecomment-341769630  

Resolving the issues listed here as the last one outstanding is in a PR to another project: https://github.com/boostorg/random/pull/32
