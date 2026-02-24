# #226 - unknown type name 'BOOST_CONSTEXPR' [Closed]

> Username: NAThompson  
> Created at: 2019-07-05 22:41:40 UTC  
> Updated at: 2019-07-06 19:31:54 UTC  
> Closed at: 2019-07-06 08:03:06 UTC  
> Url: https://github.com/boostorg/math/issues/226  

Just ran the examples:  
  
```bash  
➜  example git:(develop) ✗ ../../../b2 --reconfigure cxxflags="--std=c++17 -Wfatal-errors"  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
    - Boost.Config Feature Check: cxx11_lambdas : yes  
    - Boost.Config Feature Check: cxx11_numeric_limits : yes  
    - GCC libquadmath and __float128 support : no  
    - Boost.Config Feature Check: cxx11_explicit_conversion_operators : yes  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes  
    - Boost.Config Feature Check: cxx11_function_template_default_args : yes  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes  
    - Boost.Config Feature Check: cxx11_allocator : yes  
    - Boost.Config Feature Check: cxx11_hdr_random : yes  
    - Boost.Config Feature Check: cxx11_hdr_thread : yes  
    - Boost.Config Feature Check: cxx11_hdr_atomic : yes  
    - Boost.Config Feature Check: cxx11_decltype : yes  
    - Boost.Config Feature Check: cxx11_hdr_future : yes  
    - Boost.Config Feature Check: cxx11_hdr_chrono : yes  
    - Boost.Config Feature Check: cxx17_if_constexpr : yes  
    - Boost.Config Feature Check: cxx17_std_apply : yes  
    - Boost.Config Feature Check: cxx11_inline_namespaces : yes  
darwin.compile.c++ ../../../bin.v2/libs/math/example/catmull_rom_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/catmull_rom_example.o  
In file included from catmull_rom_example.cpp:11:  
../../../boost/math/interpolators/catmull_rom.hpp:23:11: fatal error: unknown type name 'BOOST_CONSTEXPR'  
   inline BOOST_CONSTEXPR std::size_t size(const C& c)  
          ^  
1 error generated.  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions --std=c++17 -Wfatal-errors -Wno-missing-braces -DBOOST_ALL_NO_LIB=1  -I"../../.." -I"../include_private" -c -o "../../../bin.v2/libs/math/example/catmull_rom_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/catmull_rom_example.o" "catmull_rom_example.cpp"  
```  
Strangely, I don't understand why this is popping up here. I went and checked out develop on boost.config, but no joy.  
  
Is `BOOST_CONSTEXPR` semantic sugar in this case? Can it be removed without fear?

---

## Comment 1

> Username: pabristow  
> Created at: 2019-07-06 09:16:10 UTC  
> Url: https://github.com/boostorg/math/issues/226#issuecomment-508910713  

From: Nick <notifications@github.com>  
Sent: 5 July 2019 23:42  
To: boostorg/math <math@noreply.github.com>  
Cc: Subscribed <subscribed@noreply.github.com>  
Subject: [boostorg/math] unknown type name 'BOOST_CONSTEXPR' (#226)  
  
  
Just ran the examples:  
  
➜  example git:(develop) ✗ ../../../b2 --reconfigure cxxflags="--std=c++17 -Wfatal-errors"  
  
  
  
Performing configuration checks  
  
  
  
  
  
  
  
    - default address-model    : 64-bit  
  
  
  
    - default architecture     : x86  
  
  
  
    - symlinks supported       : yes  
  
  
  
    - BOOST_COMP_GNUC >= 4.3.0 : no  
  
  
  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_lambdas : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_numeric_limits : yes  
  
  
  
    - GCC libquadmath and __float128 support : no  
  
  
  
    - Boost.Config Feature Check: cxx11_explicit_conversion_operators : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_smart_ptr : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_function_template_default_args : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_unified_initialization_syntax : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_allocator : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_hdr_random : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_hdr_thread : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_hdr_atomic : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_decltype : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_hdr_future : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_hdr_chrono : yes  
  
  
  
    - Boost.Config Feature Check: cxx17_if_constexpr : yes  
  
  
  
    - Boost.Config Feature Check: cxx17_std_apply : yes  
  
  
  
    - Boost.Config Feature Check: cxx11_inline_namespaces : yes  
  
  
  
darwin.compile.c++ ../../../bin.v2/libs/math/example/catmull_rom_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/catmull_rom_example.o  
  
  
  
In file included from catmull_rom_example.cpp:11:  
  
  
  
../../../boost/math/interpolators/catmull_rom.hpp:23:11: fatal error: unknown type name 'BOOST_CONSTEXPR'  
  
  
  
   inline BOOST_CONSTEXPR std::size_t size(const C& c)  
  
  
  
          ^  
  
  
  
1 error generated.  
  
  
  
  
  
  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -O0 -fno-inline -Wall -g -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions --std=c++17 -Wfatal-errors -Wno-missing-braces -DBOOST_ALL_NO_LIB=1  -I"../../.." -I"../include_private" -c -o "../../../bin.v2/libs/math/example/catmull_rom_example.test/darwin-4.2.1/debug/threading-multi/visibility-hidden/catmull_rom_example.o" "catmull_rom_example.cpp"  
  
Strangely, I don't understand why this is popping up here. I went and checked out develop on boost.config, but no joy.  
  
Is BOOST_CONSTEXPR semantic sugar in this case? Can it be removed without fear?  
  
This suggest s to me that BOOST_CONSTEXPR isn’t defined for Darwin platform, so  
  
I think that you could try BOOST_CONST_OR_CONSTEXPR here instead.  
  
https://www.boost.org/doc/libs/1_70_0/libs/config/doc/html/boost_config/boost_macro_reference.html  
Some compilers don't support the use of constexpr. This macro expands to const on those compilers, and constexpr elsewhere. For example, when defining const expr variables replace:  
static constexpr UIntType xor_mask = a;  
with:  
static BOOST_CONSTEXPR_OR_CONST UIntType xor_mask = a;  
  
HTH  
  
  
  
Paul

---

## Comment 2

> Username: jzmaddock  
> Created at: 2019-07-06 09:29:53 UTC  
> Url: https://github.com/boostorg/math/issues/226#issuecomment-508911599  

No there was a missing #include, fixed in the commit referenced above.

---

## Comment 3

> Username: jzmaddock  
> Created at: 2019-07-06 19:31:54 UTC  
> Url: https://github.com/boostorg/math/issues/226#issuecomment-508948798  

Well that turned into a CI bloodbath, still the newly added concept checks (which were there all along, they just weren't activated in the Jamfile), did trap a number of issues.  Just pushed another fix for those and cancelled the Appveyor build.
