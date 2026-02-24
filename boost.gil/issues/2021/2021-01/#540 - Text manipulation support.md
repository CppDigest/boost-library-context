# #540 - Text manipulation support? [Open]

> Username: yuzarx  
> Created at: 2021-01-14 00:06:31 UTC  
> Updated at: 2021-01-15 09:50:18 UTC  
> Url: https://github.com/boostorg/gil/issues/540  

Hi, 👋  
I know this is dumb to open an issue for,  
but will boost.gil support text manipulation, if so when ?  
and thanks in advance

---

## Comment 1

> Username: sdebionne  
> Created at: 2021-01-14 08:28:55 UTC  
> Url: https://github.com/boostorg/gil/issues/540#issuecomment-760016965  

GIL stands for Generic Image Library, what kind of text manipulation support are you expecting from an *image* library? Rendering text?

---

## Comment 2

> Username: yuzarx  
> Created at: 2021-01-14 09:07:33 UTC  
> Url: https://github.com/boostorg/gil/issues/540#issuecomment-760053052  

I know this might be hard and a lot to implement, but yes, yes, rendering text, loading from fonts, text color, text position etc.

---

## Comment 3

> Username: sdebionne  
> Created at: 2021-01-14 14:29:41 UTC  
> Url: https://github.com/boostorg/gil/issues/540#issuecomment-760231765  

I would recommand to check this (old) contribution then, it might be a good start:  
  
[FreetypeGIL] by Tom Brinkman - A Freetype wrapper for GIL. Renders text onto GIL views.  
  
 * [gil/extension/toolbox/freegil.hpp]  
 * Tom's original announcement in [boost::freetype - extension for boost::gil]  
  
[FreetypeGIL]: https://github.com/boost-gil/gil-contributions-archive/tree/sandbox/boost/gil/extension/toolbox      
[gil/extension/toolbox/freegil.hpp]: https://github.com/boost-gil/gil-contributions-archive/blob/sandbox/boost/gil/extension/toolbox/freegil.hpp  
[boost::freetype - extension for boost::gil]: https://lists.boost.org/Archives/boost/2007/04/119826.php

---

## Comment 4

> Username: yuzarx  
> Created at: 2021-01-14 16:21:35 UTC  
> Url: https://github.com/boostorg/gil/issues/540#issuecomment-760303324  

Okey ty I really appreciate, one thing how do I get the value  of `boost::pixel` didn't find any way to do it

---

## Comment 5

> Username: mloskot  
> Created at: 2021-01-15 09:27:13 UTC  
> Url: https://github.com/boostorg/gil/issues/540#issuecomment-760778489  

> yes, rendering text, loading from fonts, text color, text position etc.  
  
GIL is not a rendering library. You should look at https://github.com/cpp-io2d/P0267_RefImpl and http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2019/p0267r10.pdf, and plenty of other libraries out there.  
  
>  how do I get the value of boost::pixel  
  
There is no such thing as `boost::pixel`
