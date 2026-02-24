# #1975 - DeepCode found several possible bugs [Closed]

> Username: patlecat  
> Created at: 2020-06-06 07:38:30 UTC  
> Updated at: 2020-06-06 07:52:25 UTC  
> Closed at: 2020-06-06 07:47:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1975  

Running deepcode it found 2 possible problems and 5 recommendations ([DeepCode](https://www.deepcode.ai/)):  
  
Link to report: https://www.deepcode.ai/app/gh/boostorg/beast/f25c904d149a5d0a1c54a5f3d8c9045bf27a4fda/_/dashboard

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-06-06 07:47:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1975#issuecomment-640006450  

The two criticals are in the zlib example code which is not part of the beast library.  
  
I have looked at analysis from deepcode before. In a project like ours it is not able to descriminate by default between test code and real library code.  
  
It’s output must be carefully checked as it is _very often_ a false positive.  
  
Deepcode is a product still in its infancy.   
  
Thanks for taking the time to report this. Please do report any confirmed security related errors, logic errors or static analysis findings.  
  
R

---

## Comment 2

> Username: patlecat  
> Created at: 2020-06-06 07:52:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1975#issuecomment-640007238  

@madmongo1 Ah that's cool that you used it already. Just thought it might be of help even though the C++ part is still in Beta.
