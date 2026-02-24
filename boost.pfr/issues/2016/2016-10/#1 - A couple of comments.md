# #1 - A couple of comments [Closed]

> Username: AntonBikineev  
> Created at: 2016-10-03 16:14:03 UTC  
> Updated at: 2017-01-05 18:02:26 UTC  
> Closed at: 2017-01-05 18:02:26 UTC  
> Url: https://github.com/boostorg/pfr/issues/1  

Hey Anton,  
I just looked through the source code and was impressed by the number of smartest metaprogramming tricks. It literally made my brain exploded.  
I have only a couple of comments to discuss:  
1. First, the requirement of using pod type seems to be too restrictive. IIUC what we need from a type is to be aggregate initializable and be able to convert via reinterpret_cast to an arbitrary structure. For this, standard_layout requirement seems to be good enough and would allow flat_type to be _somewhat_trivial*. Consider the following case:  
  
```  
struct A { // not trivial, yet aggregate_initializable class (in C++14)  
    int a = 8;  
    double b = im_not_trivial_but_long_running_function();  
};  
boost::pfr::flat_get<1>(A{});  
```  
  
On top of that, as flat types are ubiquitously used in constexpr context, it would be nice to static_assert on is_literal_type in the first place.  
2. `has_reference_members` seems to just check whether members can be constructed from temporary objects. it doesn't cover cases when a reference is const or rvalue reference. I'm not sure, maybe it is not intented. Anyways, this requrement is already checked by `is_pod`, as `is_pod` implies `is_standard_layout`, which explicitly prohibits reference members.

---

## Comment 1

> Username: apolukhin  
> Created at: 2016-10-08 15:07:56 UTC  
> Url: https://github.com/boostorg/pfr/issues/1#issuecomment-252429486  

> 1. First, the requirement of using pod type seems to be too restrictive. IIUC what we need from a type is to be aggregate initializable and be able to convert via reinterpret_cast to an arbitrary structure.  
  
Good point. I'm trying to implement that in a user-friendly manner right now (with readable error message in border-cases).  
  
Ideally I'd like to see the following problem solved: we need a trait std::is_aggregate_initializable<T> here, otherwise there's no way to distinguish between  
  
``` cpp  
struct test_me {  
    int i = 2;  
    short s = 14;  
};  
```  
  
and  
  
``` cpp  
struct test_me {  
    int i = 2;  
    short s = 14;  
  
    test_me(){} // not aggregate initializable, PFR can not work with that, compile time error with unreadable message  
};  
```  
  
> 1. `has_reference_members` seems to just check whether members can be constructed from temporary objects. it doesn't cover cases when a reference is const or rvalue reference. I'm not sure, maybe it is not intented. Anyways, this requrement is already checked by is_pod, as is_pod implies is_standard_layout, which explicitly prohibits reference members.  
  
Removed the check. Thanks!

---

## Comment 2

> Username: apolukhin  
> Created at: 2017-01-05 18:02:26 UTC  
> Url: https://github.com/boostorg/pfr/issues/1#issuecomment-270712348  

New technique was integrated into the library :) Limitation removed: non-flat functions work with any constexpr aggregate initializable function in C++14.
