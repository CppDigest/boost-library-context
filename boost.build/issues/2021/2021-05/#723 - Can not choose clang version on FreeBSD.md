# #723 - Can not choose clang version on FreeBSD [Open]

> Username: fasxmut  
> Created at: 2021-05-02 20:31:48 UTC  
> Updated at: 2021-05-29 16:22:46 UTC  
> Url: https://github.com/boostorg/build/issues/723  

Commonly there are several different versions of clang installed on the FreeBSD system, but b2 seems can only use the default clang:  
  
This works:  
b2 toolset=clang  
  
This works:  
using clang ;  
  
But these all fail:  
b2 toolset=clang10  
b2 toolset=clang-10  
b2 toolset=clang11  
b2 toolset=clang-11  
b2 toolset=clang-devel  
  
using clang : 10 ;  
  
using clang : 11 ;  
  
using clang : devel ;  
  
It just can not use alt clang except the default.   
If I try them and find,  
b2 toolset=clang-11 -n  
gives me screen information that it still invokes clang++,   
the real c++ compiler command should be clang++11

---

## Comment 1

> Username: fasxmut  
> Created at: 2021-05-04 21:13:19 UTC  
> Updated at: 2021-05-04 21:15:55 UTC  
> Url: https://github.com/boostorg/build/issues/723#issuecomment-832252784  

After two days I have to reply my own question, because I got the answer by myself.  
When I read toolset.jam and clang.jam I got a bunch of arg variables $(2), $(3), ..., $(9), they are not so easy to understand whats the meaning. But Finally I got something. There is a way but it is not perfect, write it at the top of the project Jamfile :  
  
using clang : 11 : clang++11 ;  
  
The second arg (11) passed to using can be anything, but do not include dash - , and the extra fourth args can be used:  
  
using clang : 11 : clang++11 : &lt;cxxflags&gt;-std=c++20 ;  
using clang : 10 : clang++10 ;  
  
Here it is ok to only type command **b2**, but command **b2 toolset=clang-11**  will cause it to report duplicate error.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:16 UTC  
> Url: https://github.com/boostorg/build/issues/723#issuecomment-850859566  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
