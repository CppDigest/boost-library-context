# #1062 - Inconsistent output across platform for `double`? [Open]

> Username: andrea-cassioli-maersk  
> Created at: 2024-12-17 16:19:43 UTC  
> Updated at: 2024-12-18 11:34:40 UTC  
> Url: https://github.com/boostorg/json/issues/1062  

Hi, I have noticed that sometimes serialised `double` numbers differs in the last digit between platform. For instance the same number is printed as follow.  
  
on OSX  
  
```  
2.6721272258931175E7  
```  
  
on WSL  
  
```  
2.672127225893118E7  
```  
  
In general it seems OSX uses one less digit to print. Is it a known issue? Any suggestion on how to investigate further?

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-12-17 16:31:31 UTC  
> Url: https://github.com/boostorg/json/issues/1062#issuecomment-2548962413  

Can you tell me what the source number is, so that I can write a test that reproduces the issue? Or better yet, can you provide a snippet that reproduces the issue? Also, what are the compilers you use on WSL and OSX, and what is the Boost version?

---

## Comment 2

> Username: andrea-cassioli-maersk  
> Created at: 2024-12-18 07:37:29 UTC  
> Url: https://github.com/boostorg/json/issues/1062#issuecomment-2550571308  

Thanks for the reply. I cannot really get a snippet to easily reproduce. But here a bit more details  
  
boost version: 1.83  
clang: 19.1  
gcc: 11.4  
  
"Can you tell me what the source number is" what do you mean?

---

## Comment 3

> Username: grisumbras  
> Created at: 2024-12-18 11:34:39 UTC  
> Url: https://github.com/boostorg/json/issues/1062#issuecomment-2551086874  

There's a `double` C++ object that is being serialized. What is its value?  
  
Just to be clear, it's GCC 11.4 on WSL, and Clang 19.1 on OS X, right? Do both platforms have x86_64 architecture and use 64 bit address model?
