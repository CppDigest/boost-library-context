# #126 - Whole Program Optimization /GL on windows [Closed]

> Username: Trigve  
> Created at: 2019-11-06 18:41:50 UTC  
> Updated at: 2020-04-24 09:00:15 UTC  
> Closed at: 2020-04-24 09:00:14 UTC  
> Url: https://github.com/boostorg/context/issues/126  

Hi,  
in the documentation, there is this text  
`Windows using fcontext_t: turn off global program optimization (/GL) and change /EHsc (compiler assumes that functions declared as extern "C" never throw a C++ exception) to /EHs (tells compiler assumes that functions declared as extern "C" may throw an exception). `  
  
I've used context (with the /GL and /EHsc) as part of boost.coroutine and didn't experienced any crash or whatsoever.  
Is this still valid (mainly the /GL)?  
I'm asking because I'm going to migrate to the boost.fibers, where there is this notice too (because it is using context) and want to be sure everything will be smooth.

---

## Comment 1

> Username: olk  
> Created at: 2019-11-06 18:56:30 UTC  
> Url: https://github.com/boostorg/context/issues/126#issuecomment-550451236  

I don't use Windows as development platform - to switch off /GL and /EHsc was reported by some users.

---

## Comment 2

> Username: Trigve  
> Created at: 2019-11-06 19:01:54 UTC  
> Url: https://github.com/boostorg/context/issues/126#issuecomment-550453520  

Ok, I'll try and if will encounter some crashes I'll reply back.

---

## Comment 3

> Username: olk  
> Created at: 2020-04-24 09:00:14 UTC  
> Url: https://github.com/boostorg/context/issues/126#issuecomment-618893239  

no reply
