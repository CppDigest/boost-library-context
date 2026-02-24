# #526 - Warning Level 4 Issue [Closed]

> Username: ghost  
> Created at: 2017-06-22 07:03:24 UTC  
> Updated at: 2017-06-23 14:49:54 UTC  
> Closed at: 2017-06-23 02:08:42 UTC  
> Url: https://github.com/boostorg/beast/issues/526  

Hi, your sources doesn't compile with Warning Level4 (/W4) (Visual Studio 2015). And warnings are treated as errors here what shouldn't be so unusal. Would like to see this fixed. It's only that line:  
  
`Traits::assign(*this->pptr(), ch);`  
  
> 1>...\beast\include\beast/core/detail/static_ostream.hpp(60): warning C4244: 'argument': conversion from 'beast::detail::static_ostream_buffer::int_type' to 'const std::char_traits<char>::_Elem', possible loss of data

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-22 08:01:39 UTC  
> Url: https://github.com/boostorg/beast/issues/526#issuecomment-310306321  

willfix

---

## Comment 2

> Username: ghost  
> Created at: 2017-06-22 08:33:56 UTC  
> Updated at: 2017-06-22 08:36:19 UTC  
> Url: https://github.com/boostorg/beast/issues/526#issuecomment-310313956  

Ok, I was a bit to optimistic with that one line. There are dozens of additional warnings now because I had not referenced any of beasts classes so far :). Like e.g.:  
  
> ...\beast\include\beast/zlib/detail/deflate_stream.hpp(814): warning C4244: '=': conversion from 'int' to 'uint8_t', possible loss of data  
> ...\beast\include\beast/zlib/detail/deflate_stream.hpp(2629): warning C4244: 'argument': conversion from 'beast::zlib::uInt' to 'uint8_t', possible loss of data  
> ...\beast\include\beast/zlib/detail/deflate_stream.hpp(1637): warning C4244: '+=': conversion from 'int' to 'uint16_t', possible loss of data  
  
and many others...  
  
Maybe you can check it with Warning Level4 (/W4) activated on your setup.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-22 16:00:54 UTC  
> Url: https://github.com/boostorg/beast/issues/526#issuecomment-310424912  

I will try to fix it. Note that the build scripts already enable `/W4` but I had to disable some of the integer warnings because of the zlib code.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2017-06-22 18:11:34 UTC  
> Url: https://github.com/boostorg/beast/issues/526#issuecomment-310459326  

I think all the warnings are fixed in this branch, can you please confirm? https://github.com/vinniefalco/Beast/pull/527

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-23 02:08:42 UTC  
> Url: https://github.com/boostorg/beast/issues/526#issuecomment-310551066  

Should be fixed in *v65*

---

## Comment 6

> Username: ghost  
> Created at: 2017-06-23 14:49:54 UTC  
> Url: https://github.com/boostorg/beast/issues/526#issuecomment-310686122  

Thanks for the update. No warnings had been shown so far.
