# #113 - docs: example differs from its description [Open]

> Username: hgkjshegfskef  
> Created at: 2021-09-09 09:14:54 UTC  
> Updated at: 2021-09-09 17:13:31 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/113  

The example at https://www.boost.org/doc/libs/1_77_0/libs/safe_numerics/doc/html/eliminate_runtime_penalty/3.html is aimed to demonstrate how different type promotion policies and exception policies can be combined. The description says:  
  
> As before, we define a type safe_t to reflect our view of legal values for this program. This uses the automatic type promotion policy as well as the loose_trap_policy exception policy to enforce elimination of runtime penalties.  
  
But `safe_t` is defined as:  
```cpp  
using safe_t = safe_signed_range<  
    -24,  
    82,  
    native,  
    loose_exception_policy  
>;  
```  
According to description it should perhaps be defined as:  
```cpp  
using safe_t = safe_signed_range<  
    -24,  
    82,  
    automatic,  
    loose_trap_policy  
>;  
```

---

## Comment 1

> Username: robertramey  
> Created at: 2021-09-09 16:37:59 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/113#issuecomment-916260060  

Good to know that at least someone is reading the documents!  Good catch.  I've made the change on my local machine - let's hope that the example still compiles and runs !!!

---

## Comment 2

> Username: hgkjshegfskef  
> Created at: 2021-09-09 17:13:31 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/113#issuecomment-916284570  

Thank you for being thorough with the documentation. While reading further, I have noticed a few more problems: e.g. some excerpts refer to boost::numeric, which I assume was the old name of the library, and things like that. Maybe I will just submit a PR to not bother you with changes. This one I just wasn't sure, since I am just learning about the library.  
  
Re: this problem, looks like it compiles with the changes according to documentation wording: https://godbolt.org/z/EheKvPYeG
