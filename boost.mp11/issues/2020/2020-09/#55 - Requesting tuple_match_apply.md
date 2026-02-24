# #55 - Requesting tuple_match_apply [Open]

> Username: libbooze  
> Created at: 2020-09-07 22:50:27 UTC  
> Updated at: 2020-09-09 09:58:08 UTC  
> Url: https://github.com/boostorg/mp11/issues/55  

First sorry for the ugly name, I could not think of anything better.   
I know mp11 is mostly a lib for nice manipulation of lists, but one feature that really worked great for me is the tuple_for_each.   
  
I used it to implement nice(IMHO) dispatching on runtime value. "Problem" is that it is not optimally efficient since I am doing an equivalent of many ifs, instead of a switch or if else.  
Code example with basic idea is here(code is missing propagation of return value of the lambda, ignores alignment... but I hope general idea is clear).  
  
https://godbolt.org/z/1TscGs  
  
I have no idea what is the proper way to handle default case, I would suspect that there could be a tag type, but in my use I do not really need it since I cover all cases, so hard for me to judge if it would be a good addition.

---

## Comment 1

> Username: pdimov  
> Created at: 2020-09-07 23:29:57 UTC  
> Url: https://github.com/boostorg/mp11/issues/55#issuecomment-688539915  

This is how I'd have written it: https://godbolt.org/z/dn7hhh  
  
It's a bit less clear than ideal because values can't be put directly into a type list, they have to be wrapped into `integral_constant`, I use mp_int here. But the main point is the use of `mp_with_index` - it takes a runtime value and gives you an `integral_constant` over it, by generating a switch over the possible values. Once we have the integral constant, we can look up the type based on it, using `mp_map_find`. It's also possible to use a plain list of the types and index it with `mp_at`, instead of using a map.

---

## Comment 2

> Username: libbooze  
> Created at: 2020-09-09 01:11:08 UTC  
> Url: https://github.com/boostorg/mp11/issues/55#issuecomment-689236862  

@pdimov brainbending, but amazing... I could not understand how you can use E(value, not type) as template parameter, but it is of integral_constant kind of thing so that is why (I guess) it works. https://godbolt.org/z/r8PxaM  
  
Is there a reason why there is no mp_enum?  
Would make a nicer code in cases like this(and maybe safer since you could check (I guess) that type of enums match. I presume it is just that most of the time people do not write code like this so it was not worth the effort, or is there another technical reason?  
  
Regarding this issue: your solution is very nice, but you might want to keep it open with lowest priority in case you got some other people in the future asking for this or similar feature. I say this since original code is potentially less efficient but probably a bit more readable to average C++ developer...  
  
Anyways thank you for a quick and great answer, and thanks to Barry for spreading awareness of mp11 on SO.

---

## Comment 3

> Username: pdimov  
> Created at: 2020-09-09 01:30:45 UTC  
> Url: https://github.com/boostorg/mp11/issues/55#issuecomment-689242255  

`mp_enum<E, v>` would just be `std::integral_constant<E, v>`, so there's not that much gain for supplying the alias. (`mp_int<v>` is also just `std::integral_constant<int, v>`, but saves more typing.)  
  
I could have used `integral_constant<Encoding, ...>` in this case, but `mp_with_index<N>` always accepts size_t (because it generates a switch for 0, 1, 2, ..., N-1), so I didn't bother.  
  
Under C++17, I can in principle define `template<auto V> using mp_constant = std::integral_constant<decltype(V), V>;`, which allows `mp_constant<Encoding::u8>` directly. But I try to avoid adding C++17-only features to Mp11, for now.

---

## Comment 4

> Username: libbooze  
> Created at: 2020-09-09 09:58:07 UTC  
> Url: https://github.com/boostorg/mp11/issues/55#issuecomment-689459562  

@pdimov I was assuming mp_enum could "remember" the enum class it "came from"(using enum_type = ....) while behaving mostly like integral_constant, but then when "assignments" happen from another mp_enum you would check that enum_type matches so you can not convert enum class Color(with integral value 47) to enum class SIUnit.  
  
But again hard for me to claim this is desired since I do not have overview of the library/common use cases.
