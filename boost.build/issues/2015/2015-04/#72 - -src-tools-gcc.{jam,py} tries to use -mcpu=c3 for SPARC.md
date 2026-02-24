# #72 - /src/tools/gcc.{jam,py} tries to use -mcpu=c3 for SPARC [Closed]

> Username: tnn2  
> Created at: 2015-04-25 18:54:42 UTC  
> Updated at: 2015-04-27 16:13:38 UTC  
> Closed at: 2015-04-27 16:13:38 UTC  
> Url: https://github.com/boostorg/build/issues/72  

... but -mcpu=c3 is for VIA C3 X86 CPUs. That is broken. The default for SPARC should probably be -mcpu=v7.

---

## Comment 1

> Username: vprus  
> Created at: 2015-04-26 20:54:20 UTC  
> Url: https://github.com/boostorg/build/issues/72#issuecomment-96433942  

Is 'C3' a valid CPU name for Sparc really, or totally bogus? In other words, do you suggest to remove the line that say:  
  
```  
cpu-flags gcc OPTIONS : sparc : c3 : -mcpu=c3 : default ;  
```  
  
and make the following one:  
  
```  
cpu-flags gcc OPTIONS : sparc : v7 : -mcpu=v7 ;  
```  
  
into the default one?

---

## Comment 2

> Username: tnn2  
> Created at: 2015-04-27 08:09:56 UTC  
> Url: https://github.com/boostorg/build/issues/72#issuecomment-96548396  

c3 is not valid for sparc, no. I've tested moving the default to v7 and it works fine on at least a NetBSD/sparc64 system. The c3 line should be removed.

---

## Comment 3

> Username: vprus  
> Created at: 2015-04-27 16:13:38 UTC  
> Url: https://github.com/boostorg/build/issues/72#issuecomment-96728385  

Thanks, I've fixed this in ec60c37.
