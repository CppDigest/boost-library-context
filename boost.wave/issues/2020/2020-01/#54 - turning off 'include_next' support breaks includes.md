# #54 - turning off 'include_next' support breaks includes [Closed]

> Username: jefftrull  
> Created at: 2020-01-11 03:38:09 UTC  
> Updated at: 2020-02-12 16:37:52 UTC  
> Closed at: 2020-02-12 16:37:52 UTC  
> Url: https://github.com/boostorg/wave/issues/54  

This is a port of [TRAC 9874](https://svn.boost.org/trac10/ticket/9874). The original text reads:  
  
> The directive '#if BOOST_WAVE_SUPPORT_INCLUDE_NEXT !+ 0" in file cpp_re2_lexer.hpp is in the wrong place, and causes all #include processing to break if BOOST_WAVE_SUPPORT_INCLUDE_NEXT is set to 0.  
>   
> I've attached a simple patch.  
[wave.2.patch](https://github.com/boostorg/wave/files/4048939/wave.2.patch.txt)

---

## Comment 1

> Username: jefftrull  
> Created at: 2020-01-11 03:41:52 UTC  
> Url: https://github.com/boostorg/wave/issues/54#issuecomment-573276985  

I (@jefftrull ) reproduced this problem and also verified the patch fixes it. The attached test code produces the message `caught exception: error: ill formed #include directive: #include` when `BOOST_WAVE_SUPPORT_INCLUDE_NEXT` is defined as 0.  
[trac9874.cpp](https://github.com/boostorg/wave/files/4048941/trac9874.cpp.txt)

---

## Comment 2

> Username: jefftrull  
> Created at: 2020-02-12 16:37:52 UTC  
> Url: https://github.com/boostorg/wave/issues/54#issuecomment-585295907  

Closing thanks to merged fix
