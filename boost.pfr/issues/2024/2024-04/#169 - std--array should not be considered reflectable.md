# #169 - std::array should not be considered reflectable [Open]

> Username: kiwixz  
> Created at: 2024-04-19 19:20:32 UTC  
> Updated at: 2024-10-17 09:03:19 UTC  
> Url: https://github.com/boostorg/pfr/issues/169  

It's a common type that is an aggregate but with a C array as only member.  
  
Until something like #20 is implemented, it should be explicitly marked as non-reflectable.
