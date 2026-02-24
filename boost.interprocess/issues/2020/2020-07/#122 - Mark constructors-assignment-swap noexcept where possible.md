# #122 - Mark constructors/assignment/swap noexcept where possible [Closed]

> Username: Lastique  
> Created at: 2020-07-26 21:53:59 UTC  
> Updated at: 2021-02-15 23:01:39 UTC  
> Closed at: 2021-02-15 23:01:26 UTC  
> Url: https://github.com/boostorg/interprocess/issues/122  

Please mark functions `noexcept` where possible for the types provided by Boost.Interprocess. In particular, I'm interested in types `shared_memory_object` and `mapped_region`, but this is probably not limited to those.  
  
The functions that have immediate benefit from being marked are:  
- Default constructors.  
- Move constructors and assignment.  
- Copy constructors and assignment, where possible.  
- `swap`.  
  
The important effects of this markup includes:  
- More efficient implementation of standard containers and algorithms.  
- Implicitly defined constructors and operators of user's types that have Boost.Interprocess types in members or base classes become `noexcept`.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-02-15 23:01:39 UTC  
> Url: https://github.com/boostorg/interprocess/issues/122#issuecomment-779484453  

Many thanks for the report!
