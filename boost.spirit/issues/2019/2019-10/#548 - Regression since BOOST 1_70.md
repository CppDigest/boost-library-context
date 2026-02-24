# #548 - Regression since BOOST 1_70 [Closed]

> Username: artemp  
> Created at: 2019-10-18 11:07:04 UTC  
> Updated at: 2019-10-19 08:52:49 UTC  
> Closed at: 2019-10-19 00:25:22 UTC  
> Url: https://github.com/boostorg/spirit/issues/548  

Minimal use-case : https://github.com/artemp/spirit-x3-test ( somewhat related to #511)   
This code compiles, links and runs as expected with BOOST <= 1.69 (both GCC and clang)  but results in linker error with BOOST >=1.70. Key features are separate TUs for grammar instantiations and use of semantic actions in aggregate grammar.   
  
/cc @djowel @Kojoley

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-10-18 19:50:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/548#issuecomment-543910856  

I can confirm the bug, regressed with #457, should be fixed with #549.  
  
You can avoid the bug by defining rules with `force_attribute = true` though.

---

## Comment 2

> Username: artemp  
> Created at: 2019-10-19 08:52:39 UTC  
> Updated at: 2019-10-19 08:52:49 UTC  
> Url: https://github.com/boostorg/spirit/issues/548#issuecomment-544117126  

@Kojoley - thanks for the quick fix!
