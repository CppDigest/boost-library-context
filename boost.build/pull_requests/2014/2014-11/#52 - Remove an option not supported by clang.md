# #52 Remove an option not supported by clang. [Closed]

> Username: KayEss  
> Created at: 2014-11-16 02:31:09 UTC  
> Updated at: 2021-10-02 22:20:38 UTC  
> Closed at: 2014-11-25 07:11:52 UTC  
> Url: https://github.com/boostorg/build/pull/52  

I'm not certain when, if ever, clang supported the flag. Certainly in 3.5 it produces a fairly ugly warning:  
  
```  
clang: warning: optimization flag '-finline-functions' is not supported  
clang: warning: argument unused during compilation: '-finline-functions'  
```  
  
The 3.5 release notes state that clang is now warning about options it doesn't understand -- previously it was silently ignoring them. I think it might be better to just turn it off for 3.5 and later, but I only know how to do this for specific versions, not a condition like < 3.5.

---

## Comment 1

> Username: vprus  
> Created_at: 2014-11-25 06:23:30 UTC  
> Url: https://github.com/boostorg/build/pull/52#issuecomment-64315700  

Kirit,  
  
am I right that effectively, clang does not have a way to control inlining independently of optimization settings?

---

## Comment 2

> Username: KayEss  
> Created_at: 2014-11-25 06:40:56 UTC  
> Url: https://github.com/boostorg/build/pull/52#issuecomment-64316867  

I'm afraid I'm no clang expert, I was just going off the warning emitted by 3.5 and looked in the release notes. A quick look at the docs doesn't turn anything up.  
- 3.1: http://llvm.org/releases/3.1/tools/clang/docs/UsersManual.html  
- 3.4: http://llvm.org/releases/3.4/tools/clang/docs/UsersManual.html  
- Current (3.6): http://clang.llvm.org/docs/UsersManual.html  
  
So it doesn't look like it. 3.6 does have options for reporting on the inliner -- it does look like you can't specify if you want it or not. The release note is here: http://llvm.org/releases/3.5.0/tools/clang/docs/ReleaseNotes.html#improvements-to-clang-s-diagnostics It talks about generating a warning rather than an error, but I've never seen an error due to the inline option.

---

## Comment 3

> Username: vprus  
> Created_at: 2014-11-25 07:11:52 UTC  
> Url: https://github.com/boostorg/build/pull/52#issuecomment-64318898  

Thanks, looks like -finline-functions is something only gcc has. I've merged your patch.

---

## Comment 4

> Username: KayEss  
> Created_at: 2014-11-25 07:20:13 UTC  
> Url: https://github.com/boostorg/build/pull/52#issuecomment-64319429  

I saw some other clang configurations, like a darwin.clang one I think -- I didn't do anything with those as I can't run them right now.

---
