# #101 Add freestanding from_dump() function for reducing scopes and less ty… [Closed]

> Username: leha-bot  
> Created at: 2020-11-19 16:08:07 UTC  
> Updated at: 2020-12-15 14:18:48 UTC  
> Closed at: 2020-12-15 14:18:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/101  

…ping

---

## Comment 1

> Username: coveralls  
> Created_at: 2020-11-23 02:59:50 UTC  
> Updated_at: 2020-11-23 04:19:14 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/101#issuecomment-731902413  

[![Coverage Status](https://coveralls.io/builds/35159474/badge)](https://coveralls.io/builds/35159474)  
  
Coverage increased (+0.4%) to 92.688% when pulling **9fc7459e6794c382339d79df1c6e9d1718dc7be1 on leha-bot:freestanding_from_dump_factory_fn** into **e75d2ff93bbe63ed3f1f578823cae6b8039da821 on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2020-12-15 14:18:48 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/101#issuecomment-745313692  

Sorry, but I'm closing this PR.  
  
Infield experience shows that the dump/load functions of the Boost.Stacktrace library are badly designed and provoke users to do bad things (like use them in signal handlers).  
  
Your PR is good, but it makes simpler to use those bad functions.

---
