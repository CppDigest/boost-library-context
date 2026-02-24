# #943 - Replace grammar::range recycled-strings mechanism [Closed]

> Username: alandefreitas  
> Created at: 2025-11-06 22:48:41 UTC  
> Updated at: 2026-01-20 01:35:33 UTC  
> Closed at: 2026-01-20 01:35:33 UTC  
> Url: https://github.com/boostorg/url/issues/943  

When we parse a range of rules `T` with `range_rule`, we get a [`boost::urls::grammar::range<T>`](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/range_rule.hpp#L63) that currently uses a recycled-strings implementation.   
  
### The current implementation  
  
A [`boost::urls::grammar::range<T>`](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/range_rule.hpp#L63) stores all rules as a type-erased [`range<T>::any_rule`](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/impl/range_rule.hpp#L40C8-L41C13).  
  
Here's the complete current connection between the rule and the mechanism:  
  
- The `range_rule` overload set has functions with [one](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/range_rule.hpp#L400) or [two](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/range_rule.hpp#L532C1-L532C11) parameters. The version with two overloads allows a different rule for the first element, but both must have the same return type. The function also forwards the minimum and maximum number of elements to the rules, although this is not relevant to this issue.  
- These functions return an implementation-defined `range_rule_t` type that can store [one](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/range_rule.hpp#L303) or [two](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/range_rule.hpp#L422) internal rule types. The `parse` function of this rule simply calls the rule repeatedly to ensure the string matches the rule and than returns a `range<T>`.  
- [`boost::urls::grammar::range<T>`](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/range_rule.hpp#L63) is a forward range/view of T that stores a type-erased rule that returns `T`. The `range<T>` constructors type-erase the rule to an interface with `first` and `next`, so we need a different function for the first rule in the case of two rules.   
- The constructor `range<T>` uses the private `small_buffer sb_;` of `range<T>` to store an [`range<T>::any_rule`](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/impl/range_rule.hpp#L40C8-L41C13).   
- The [concrete implementation](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/impl/range_rule.hpp#L281) of `range<T>::any_rule` is what contains the [`recycled_ptr`](https://github.com/boostorg/url/blob/1e010e5ba102641fd5c6b997e1dae950b13faaf1/include/boost/url/grammar/impl/range_rule.hpp#L299) storing the rule.  
  
### Proposal  
   
Change declaration to:  
  
```cpp  
template<  
    class ValueType,  
    class RangeRule = any_rule,  
    bool IsEmpty = std::is_empty<Rule>::value  
>  
class range;  
```  
  
This retains the functionality of the existing code while giving the user more options and better defaults:  
  
- It detects empty rules to specialize `range` for `IsEmpty == true`, so that a `range` over a stateless rule can use only a function pointer for type-erasure.  
- The user has the option to use another internal Rule to represent the rule without type-erasing it. A RangeRule is basically the same interface as the current any_rule interface, but it's defined as concepts (in practice, type_traits for enable_if because it's C++11).  
  
### Motivation  
  
- Stateless rules should not pay for string storage.   
- This simplifies the API and improves clarity and performance.  
- The API doesn't break unless the new parameters are customized.  
  
For future work, we could also consider doing what other libraries do, which is just having two different rule types for these cases range attempts to cover: `star` and `list`. In that case, there's no need for a special rule type.
