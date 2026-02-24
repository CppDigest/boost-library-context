# #41 - Link errors on Linux in a simple program modeled after quick_start.cpp [Closed]

> Username: pdimov  
> Created at: 2018-10-24 22:44:57 UTC  
> Updated at: 2018-10-25 13:20:12 UTC  
> Closed at: 2018-10-25 13:20:12 UTC  
> Url: https://github.com/boostorg/wave/issues/41  

When trying this simple program:  
  
https://github.com/boostorg/wave/commit/5891614e845944f60b0dd639c7825fad85297ff1  
  
I get link errors about missing `new_lexer`:  
  
https://travis-ci.org/boostorg/wave/builds/445687895  
  
Is this normal? It doesn't happen on Windows.  
  
I tried to add `BOOST_SYMBOL_VISIBLE` here  
  
https://github.com/boostorg/wave/commit/694ee3701ead38589482e612232bfdb227229856  
  
and there  
  
https://github.com/boostorg/wave/commit/f008da510662c224334b136092c8d5d5bc703c46  
  
but neither helped.

---

## Comment 1

> Username: hkaiser  
> Created at: 2018-10-25 00:44:48 UTC  
> Url: https://github.com/boostorg/wave/issues/41#issuecomment-432874928  

Does this example work for you: https://github.com/boostorg/wave/blob/develop/samples/quick_start/quick_start.cpp?

---

## Comment 2

> Username: pdimov  
> Created at: 2018-10-25 01:27:07 UTC  
> Url: https://github.com/boostorg/wave/issues/41#issuecomment-432881756  

I'm pretty sure it doesn't, as the one I'm using is virtually identical. The problem is caused by the explicit instantiations not being seen due to the new default of `visibility=hidden` and is fixed by #42.
