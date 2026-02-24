# #23 - Address naming issues inherited from p0172r0 [Closed]

> Username: badair  
> Created at: 2016-04-14 22:34:40 UTC  
> Updated at: 2016-07-30 22:00:47 UTC  
> Closed at: 2016-07-30 22:00:47 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/23  

- `has_member_qualifiers`  
- `is_const_member`  
- `is_volatile_member`  
- `is_cv_member`  
- `is_lvalue_reference_member`  
- `is_rvalue_reference_member`  
- `add_member_const`  
- `add_member_volatile`  
- `add_member_cv`  
- `add_member_lvalue_reference`  
- `add_member_rvalue_reference`  
- `remove_member_const`  
- `remove_member_volatile`  
- `remove_member_cv`  
- `remove_member_reference`  
- `remove_member_lvalue_reference`  
- `remove_member_rvalue_reference`  
- `remove_all_member_qualifiers`  
  
The names in CallableTraits are based on the paper http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2015/p0172r0.html.   
  
Currently the CallableTraits implementation applies this logic to function objects' operator(), but these names do not make this behavior obvious at all. Either separate that behavior into new traits, or rename these to make more sense.  
  
Also, address "has" vs. "is", "qualifiers" on some but not others, etc.  
  
Consider `_t` and `_v` like the standard library... :(

---

## Comment 1

> Username: badair  
> Created at: 2016-07-30 22:00:47 UTC  
> Url: https://github.com/boostorg/callable_traits/issues/23#issuecomment-236392228  

Not fixing.
