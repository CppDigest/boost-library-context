# #93 - Concept `SafeNumeric` is unnecessary [Closed]

> Username: akrzemi1  
> Created at: 2020-10-14 20:47:28 UTC  
> Updated at: 2020-10-17 04:07:25 UTC  
> Closed at: 2020-10-17 04:07:25 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/93  

This library defines concept `SafeNumeric` but it never uses it for anything. In generic libraries concepts are used as an interface between templates that require something of their types, and concrete types that want to adhere to these requirements. This is the case for concept `Numeric`: template `safe<>` requires it as a constraint, and types like `int` or `safe<int>` model it. In case of `SafeNumeric` some types model it, but no template requires it.  
  
Also, intuitively, this library is for providing `safe<int>` which is *just like `int`*. This "just like `int`" is expressed with concept `Numeric`. One cannot apply an analogous reasoning to `SafeNumeric` and say, my function works only for things that are "just like `safe<int>`".

---

## Comment 1

> Username: robertramey  
> Created at: 2020-10-14 23:04:44 UTC  
> Updated at: 2020-10-14 23:21:29 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/93#issuecomment-708706019  

>This library defines concept SafeNumeric but it never uses it for anything  
If nothing else, it's used for documenting behavior of the library types.    
But it's more than that.  The type trait is_safe<T> is used through out the library.  In many/most cases it's used as an argument to enable_if to select appropriate definitions. But I know that at least in some cases, it is used to catch user errors in function calls and/or in user implementations of promotion and/or exception policies.  
  
You're correct of course that in the library, we don't really illustrate the usage fo SafeNumeric concept.  This is not a huge surprise since it's a "top level" type not used (very much) within the library.  But users that define their own types and/or functions which require safe parameters could/should/would use is_safe<T>::value.  Consider the example "Programming by Contract is Too Slow".  In this case test2 takes safe types as parameters:  
  
auto test2(unsigned int hours, unsigned int minutes){  
    // solution: use safe numerics  
    // safe types can be implicitly constructed base types  
    // construction guarentees corectness  
    // return value is known to fit in unsigned int  
    return convert(hours, minutes);  
}  
  
If one wanted catch user errors of passing an unsafe type, he could do the following instead.  
  
auto test2(unsigned int hours, unsigned int minutes){  
    // solution: use safe numerics  
    static_assert(is_safe<hours>::value);  
    static_assert(is_safe<minutes>::value);  
    // safe types can be implicitly constructed base types  
    // construction guarentees corectness  
    // return value is known to fit in unsigned int  
    return convert(hours, minutes);  
}  
  
Granted, not the greatest example as the library includes implicit conversion to safe types when appropriate.  But its the best example I can come up with on short notice.  
  
> Also, intuitively, this library is for providing safe<int> which is just like int  
I think it would be more accurate and helpful to rephrase this as:  
  
"intuitively, this library is for providing safe<int> which is an enhanced version of int".  Works like an int - but can never return an erroneous result.

---

## Comment 2

> Username: robertramey  
> Created at: 2020-10-14 23:19:59 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/93#issuecomment-708710863  

Damn - I see that is_safe is indeed documented in the SafeNumeric<T> concept.  So some of the above commentary is unnecessary.

---

## Comment 3

> Username: robertramey  
> Created at: 2020-10-17 04:07:25 UTC  
> Url: https://github.com/boostorg/safe_numerics/issues/93#issuecomment-710743471  

closing this - feel free to re-open if necessary.
