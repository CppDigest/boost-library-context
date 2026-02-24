# #265 - Consider adding a way to get type names as compile-time strings [Open]

> Username: ldionne  
> Created at: 2016-03-28 17:55:28 UTC  
> Updated at: 2016-03-28 21:51:48 UTC  
> Url: https://github.com/boostorg/hana/issues/265  

As done in [ctti](https://github.com/Manu343726/ctti).

---

## Comment 1

> Username: Manu343726  
> Created at: 2016-03-28 19:10:07 UTC  
> Updated at: 2016-03-28 19:10:46 UTC  
> Url: https://github.com/boostorg/hana/issues/265#issuecomment-202533765  

Let me note the two main issues with CTTI type IDs above:  
- **Fixed size name storage**: Since we wanted to have an **unique** `constexpr` class to store both the name and its hash, the only way to store names homogeneously was writing a fixed-length `constexpr` string class. This has the caveat that storing `ctti::type_id_t`s variables may add a lot of overhead (We have a macro to control the length of the string OTOH). Since I was using ctti mainly to store type IDs and not playing with the type name (As type tag for a variant, IDs for a reflection engine, etc), I wrote a `ctti::unnamed::type_id_t` counterpart that holds the hash only. That's enough to use IDs as keys in hash tables and the like, unless you want the name of the type as part of debugging of course.    
  I'm sure this is not a problem in Hana since it's metaprogramming related, so working with heterogeneous types is the whole point of the lib. If I were you, I would ignore all the `constexpr` string stuff and store the strings as typelists (Less compiler support headaches to care about).  
- **ICEs**: As the README of the library says, we support GCC, Clang, and Visual Studio (Linux, MinGW, Cygwin, etc). This looks great for a readme, but from type to time GCC raises ICEs on some use cases. I haven't confirmed this (A good test suite is something the library is missing... (), but I can tell you some patterns that _could_ result in an ICE.  
  First one: **Storing an ID in a class as a static constant**:  
    
  ``` cpp  
  class MyClass  
  {  
      static constexpr ctti::type_id_t id = ctti::type_id<MyClass>();  
  };  
  ```  
    
  Hopefully the workaround here is simple: Don't store the ID, use a function.  
    
  Today I have found a second one when working on https://github.com/GueimUCM/siplasplas/pull/19: **Using IDs as template parameters** (See the PR comments). Again, this is `constexpr`-related, so if you follow with typelists this may not affect you.  
  
Hope it helps.

---

## Comment 2

> Username: apolukhin  
> Created at: 2016-03-28 20:00:18 UTC  
> Url: https://github.com/boostorg/hana/issues/265#issuecomment-202560054  

Probably it could be more useful to just use the implementation from Boost.TypeIndex https://github.com/apolukhin/type_index/tree/constexpr14. constexpr14 branch will be merged to develop right after the 1.61 release.  
  
Then all you need to do is to include this header boost/type_index/ctti_type_index.hpp

---

## Comment 3

> Username: Manu343726  
> Created at: 2016-03-28 21:51:47 UTC  
> Url: https://github.com/boostorg/hana/issues/265#issuecomment-202595016  

@ldionne If boost will support a similar feature, I agree with Antony, just pick it from Boost.TypeIndex instead of reinventing the wheel.  
  
@apolukhin we have an open feature idea of having cross-platform type IDs, maybe parsing names to get a common representation (MSVC for example prepends `class`/`struct` to a class type, also puts spaces between closing angle brackets following pre-C++11 rules). This arises in some situations like the user entering a type name manually as part of a search:  
  
``` cpp  
static std::unique_ptr<BaseClass> getInstance(const std::string& name)  
{  
     return types[name].create(...);  
}  
```  
  
We thought about processing type names given by `__PRETTY_FUNCTION__` and friends to eliminate such "_irrelevant_" details.  
  
Have you considered this, and if so, advanced in some way?
