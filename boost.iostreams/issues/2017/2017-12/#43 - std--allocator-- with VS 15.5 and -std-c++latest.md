# #43 - std::allocator<> with VS 15.5 and /std:c++latest [Closed]

> Username: matbech  
> Created at: 2017-12-05 00:31:16 UTC  
> Updated at: 2017-12-09 20:02:03 UTC  
> Closed at: 2017-12-09 20:02:03 UTC  
> Url: https://github.com/boostorg/iostreams/issues/43  

The VS 15.5 compiler (/std:c++latest ) complains about zlib.hpp because of the deprecated std::allocator<>  
  
``  
error C4996: 'std::allocator<char>::rebind<char>::other': warning STL4010: Various members of std::allocator are deprecated in C++17. Use std::allocator_traits instead of accessing these members directly. You can define _SILENCE_CXX17_OLD_ALLOCATOR_MEMBERS_DEPRECATION_WARNING or _SILENCE_ALL_CXX17_DEPRECATION_WARNINGS to acknowledge that you have received this warning.  
``  
  
File: zlib.hpp  
```  
template<typename Alloc>  
struct zlib_allocator_traits {  
#ifndef BOOST_NO_STD_ALLOCATOR  
    typedef typename Alloc::template rebind<char>::other type;  
...  
  
template<typename Alloc = std::allocator<char> >  
class zlib_compressor_impl : public zlib_base, public zlib_allocator<Alloc> {  
  
etc   
```

---

## Comment 1

> Username: eldiener  
> Created at: 2017-12-09 01:45:24 UTC  
> Url: https://github.com/boostorg/iostreams/issues/43#issuecomment-350414307  

The problem appears to be knowing in the code when msvc-14.1 is being used and /std:c++latest is being used as a compiler option. I can use Boost.Config to determine that msvc-14.1 is being used but I do not see how I am to determine that /std:c++latest is being used by the compiler.

---

## Comment 2

> Username: matbech  
> Created at: 2017-12-09 02:06:38 UTC  
> Updated at: 2017-12-09 02:31:29 UTC  
> Url: https://github.com/boostorg/iostreams/issues/43#issuecomment-350415891  

Regardless of the compiler/switch, std::allocator can be replaced with allocator_traits. It's being used in other libraries:  
https://github.com/search?q=org%3Aboostorg+std%3A%3Aallocator_traits&type=Code  
  
They use a helper if the compiler does not support it (if BOOST_NO_CXX11_ALLOCATOR is defined):  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/detail/allocator.hpp  
https://github.com/boostorg/thread/blob/develop/include/boost/thread/csbl/memory/allocator_traits.hpp

---

## Comment 3

> Username: eldiener  
> Created at: 2017-12-09 03:39:19 UTC  
> Url: https://github.com/boostorg/iostreams/issues/43#issuecomment-350421038  

It looks like for C++11 on up I should be able to replace:  
  
`typedef typename Alloc::template rebind<char>::other type;`  
  
with  
  
`typedef typename std::allocator_traits<Alloc>::template rebind_alloc<char> type;`

---

## Comment 4

> Username: eldiener  
> Created at: 2017-12-09 04:54:36 UTC  
> Url: https://github.com/boostorg/iostreams/issues/43#issuecomment-350424070  

Thanks for opening this issue. I have posted a fix in the 'develop' branch of iostreams. Feel free to test it out as I have done.

---

## Comment 5

> Username: matbech  
> Created at: 2017-12-09 12:00:04 UTC  
> Url: https://github.com/boostorg/iostreams/issues/43#issuecomment-350454470  

Thank you. There are a few other places that need some care:  
https://github.com/boostorg/iostreams/search?utf8=%E2%9C%93&q=std%3A%3Aallocator&type=  
e.g.  
https://github.com/boostorg/iostreams/blob/develop/include/boost/iostreams/detail/buffer.hpp#L42  
https://github.com/boostorg/iostreams/blob/develop/include/boost/iostreams/filter/lzma.hpp#L115

---

## Comment 6

> Username: eldiener  
> Created at: 2017-12-09 13:20:58 UTC  
> Url: https://github.com/boostorg/iostreams/issues/43#issuecomment-350466089  

I pushed to develop changes for the other situations where BOOST_NO_STD_ALLOCATOR is being checked.
