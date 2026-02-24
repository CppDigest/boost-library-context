# #159 - Warnings about freeing non-heap-allocated object [Closed]

> Username: Lastique  
> Created at: 2022-03-10 18:19:05 UTC  
> Updated at: 2022-06-16 23:02:59 UTC  
> Closed at: 2022-06-16 23:02:59 UTC  
> Url: https://github.com/boostorg/wave/issues/159  

On Kubuntu 21.10, gcc 11.2 produces warnings like these when building Boost.Wave 1.79.0 beta1 RC1 (presumably, revision f861b1a305a5dd70286ff84255ad78b1fd46975c):  
  
```  
In file included from /usr/include/x86_64-linux-gnu/c++/11/bits/c++allocator.h:33,  
                 from /usr/include/c++/11/bits/allocator.h:46,  
                 from /usr/include/c++/11/string:41,  
                 from libs/wave/src/token_ids.cpp:18:  
In member function ‘__gnu_cxx::new_allocator<char>::deallocate(char*, unsigned long)’,  
    inlined from ‘boost::allocator_deallocate<std::allocator<char> >(std::allocator<char>&, boost::allocator_pointer<std::allocator<char> >::type, boost::allocator_size_type<std::allocator<char> >::type)void’ at ./boost/core/allocator_access.hpp:375:17,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >::Free(void*, unsigned long)’ at ./boost/wave/util/flex_string.hpp:542:36,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >::~AllocatorStringStorage()’ at ./boost/wave/util/flex_string.hpp:624:17,  
    inlined from ‘boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >::reserve(unsigned long)’ at ./boost/wave/util/flex_string.hpp:678:5,  
    inlined from ‘boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*>::CowString(char const*, unsigned long, std::allocator<char> const&)’ at ./boost/wave/util/flex_string.hpp:1259:23,  
    inlined from ‘boost::wave::util::flex_string<char, std::char_traits<char>, std::allocator<char>, boost::wave::util::CowString<boost::wave::util::AllocatorStringStorage<char, std::allocator<char> >, char*> >::flex_string(char const*, std::allocator<char> const&)’ at ./boost/wave/util/flex_string.hpp:1481:43,  
    inlined from ‘boost::wave::get_token_name(boost::wave::token_id)’ at libs/wave/src/token_ids.cpp:243:64:  
/usr/include/c++/11/ext/new_allocator.h:145:26: warning: ‘operator delete(void*, unsigned long)’ called on unallocated object ‘boost::wave::util::SimpleStringStorage<char, std::allocator<char> >::emptyString_’ [-Wfree-nonheap-object]  
  145 |         ::operator delete(__p  
      |         ~~~~~~~~~~~~~~~~~^~~~  
  146 | #if __cpp_sized_deallocation  
      | ~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
  147 |                           , __t * sizeof(_Tp)  
      |                           ~~~~~~~~~~~~~~~~~~~  
  148 | #endif  
      | ~~~~~~                      
  149 |                          );  
      |                          ~  
In file included from ./boost/wave/wave_config.hpp:278,  
                 from libs/wave/src/token_ids.cpp:22:  
/usr/include/c++/11/ext/new_allocator.h: In function ‘boost::wave::get_token_name(boost::wave::token_id)’:  
./boost/wave/util/flex_string.hpp:511:1: note: declared here  
  511 | SimpleStringStorage<E, A>::emptyString_ =  
      | ^~~~~~~~~~~~~~~~~~~~~~~~~  
```  
  
The command line is:  
  
```  
b2 -j 16 --toolset=gcc cxxflags="-flto=auto -ffat-lto-objects -march=nehalem -mtune=skylake -fcf-protection=none -faligned-new -fno-plt -fvisibility-inlines-hidden -O3 -fno-math-errno -fno-rounding-math -fno-signaling-nans -fno-trapping-math -ftree-vectorize -fgcse-sm -fgcse-las -fgcse-after-reload -ftree-loop-im -ftree-loop-distribution -ftree-loop-if-convert -funswitch-loops -frename-registers -fivopts" linkflags="-Wl,-z,relro -Wl,-z,now -Wl,-O1 -Wl,--hash-style=both -Wl,-z,noexecstack -Wl,--as-needed" \  
 variant=release cxxstd=17-gnu threading=multi debug-symbols=on runtime-link=shared optimization=speed inlining=full warnings=on link=shared stage  
```

---

## Comment 1

> Username: jefftrull  
> Created at: 2022-03-10 18:45:12 UTC  
> Url: https://github.com/boostorg/wave/issues/159#issuecomment-1064378934  

Very interesting. I don't think that code has changed. Maybe it's an improved analysis in gcc? I have Kubuntu 20.04 and don't see this.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-03-10 20:36:54 UTC  
> Url: https://github.com/boostorg/wave/issues/159#issuecomment-1064480974  

I think, the compiler does not understand the invariant that `pData_` does not point to `SimpleStringStorage<E, A>::emptyString_` when `capacity()` is zero, hence the warning. If that's the case then I think the warning needs to be silenced with a pragma.  
  
PS: I can see that there are places where you compare `pData_` with `&SimpleStringStorage<E, A>::emptyString_`. This could silence the warning too, but I don't like this, as it will likely break with shared libraries (i.e. there may be multiple instances of `SimpleStringStorage<E, A>::emptyString_`, and `pData_` may point to one of them and not compare equal to a pointer to another). I'm not sure how relevant shared libraries are to Boost.Wave or `AllocatorStringStorage` in particular. If they are, it is probably a good idea to change the other places to check `capacity()` instead of pointer comparisons.

---

## Comment 3

> Username: jefftrull  
> Created at: 2022-06-09 02:45:39 UTC  
> Url: https://github.com/boostorg/wave/issues/159#issuecomment-1150609482  

Can you help me reproduce this? I'm not very familiar with bjam. I always build Wave using `b2` from the top level, like this:  
  
`./b2 --with-wave`  
  
or from within `libs/wave` like this:  
  
`../../b2 build samples test`  
  
Neither of those commands seem to generate this warning - and I'm actually on Kubuntu 21.10 myself - though maybe I missed it.  
  
I don't seem to have a `bjam` in my path, although I know it's kind of the same thing as `b2` (?). Thanks for your help.

---

## Comment 4

> Username: Lastique  
> Created at: 2022-06-09 08:36:10 UTC  
> Url: https://github.com/boostorg/wave/issues/159#issuecomment-1150835756  

I'm on Kubuntu 22.04 now, and the command in the original post still produces the warnings with the current Boost develop. Replace `bjam` with `b2`. I'm running the command from Boost root. You can add `--with-wave` to build only Boost.Wave. Here is the build log:  
[boost_wave_log.txt](https://github.com/boostorg/wave/files/8868495/boost_wave_log.txt)  
  
```  
$ g++ -v  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/11/lto-wrapper  
OFFLOAD_TARGET_NAMES=nvptx-none:amdgcn-amdhsa  
OFFLOAD_TARGET_DEFAULT=1  
Target: x86_64-linux-gnu  
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 11.2.0-19ubuntu1' --with-bugurl=file:///usr/share/doc/gcc-11/README.Bugs --enable-languages=c,ada,c++,go,brig,d,fortran,objc,obj-c++,m2 --prefix=/usr --with-gcc-major-version-only --program-suffix=-11 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --enable-bootstrap --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-plugin --enable-default-pie --with-system-zlib --enable-libphobos-checking=release --with-target-system-zlib=auto --enable-objc-gc=auto --enable-multiarch --disable-werror --enable-cet --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none=/build/gcc-11-gBFGDP/gcc-11-11.2.0/debian/tmp-nvptx/usr,amdgcn-amdhsa=/build/gcc-11-gBFGDP/gcc-11-11.2.0/debian/tmp-gcn/usr --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu --with-build-config=bootstrap-lto-lean --enable-link-serialization=2  
Thread model: posix  
Supported LTO compression algorithms: zlib zstd  
gcc version 11.2.0 (Ubuntu 11.2.0-19ubuntu1)   
```

---

## Comment 5

> Username: Lastique  
> Created at: 2022-06-09 08:39:36 UTC  
> Updated at: 2022-06-09 08:42:04 UTC  
> Url: https://github.com/boostorg/wave/issues/159#issuecomment-1150839150  

A simpler command that reproduces the warnings (also from Boost root):  
  
```  
b2 -j16 warnings=on --with-wave  
```  
  
(adjust `-j` to your machine capacity, of course.)

---

## Comment 6

> Username: jefftrull  
> Created at: 2022-06-09 18:35:13 UTC  
> Url: https://github.com/boostorg/wave/issues/159#issuecomment-1151470260  

Thanks! I can reproduce this now. I'm not sure what exactly to do; this code is actually the work of @andralex and is described in detail [here](http://erdani.org/publications/cuj-06-2001.php.html). I've looked at it a bit and other than a pragma, I'm not sure how to safely make a change. The most recent version of the code from the master is [here](https://github.com/snaewe/loki-lib/tree/master/include/loki/flex) AFAICT and the `emptyString_` sentinel is still used. Any thoughts?
