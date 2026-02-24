# #19 - Clarify constraints on type T of endian_buffer when align::no [Closed]

> Username: viboes  
> Created at: 2017-01-29 09:31:31 UTC  
> Updated at: 2019-04-29 16:35:23 UTC  
> Closed at: 2019-04-29 16:35:23 UTC  
> Url: https://github.com/boostorg/endian/issues/19  

The constraints for type T in this case are not ducumented.  
  
From the errors at compile time I have deduced them to   
```  
    // Needed by unaligned  
    Int() = default;  
    Int(int i) : value (i) {}  
    explicit operator char() { return value; }  
    int operator >>(int n) { return value >> n; }  
    int operator <<(int n) { return value << n; }   
```  
http://melpon.org/wandbox/permlink/erLEWpc1oaR64iMW  
  
While  
```  
    Int() = default;  
    explicit Int(int i) : value (i) {}  
```  
are natural on a wrapped opaque int, the implicit conversion is less desirable as well as the conversions to char and the shift operations.   
  
However, I believe these constraints are not justified. I believe that the conversion could be explicit and that the implementation could use reinterpret_cast instead of static_cast, as we are reinterpreting the bits. IMHO, the shift shouldn't be required. It should be an implementation detail.

---

## Comment 1

> Username: arvidn  
> Created at: 2017-11-06 12:28:01 UTC  
> Url: https://github.com/boostorg/endian/issues/19#issuecomment-342134248  

In my mind, it would be great to support stronger type-safety with custom integer classes, another challenge is to have a protocol to find out what the underlying integer type is. Something like [underlying_type](http://en.cppreference.com/w/cpp/types/underlying_type).  
  
I would love to be able to use enum classes as the value type of of ``endian_buffer``. But more importantly, my own fixed_point type or "strong-typedef" integers whose purpose is to disable operators like ``<<`` and ``>>`` on them (among others).  
  
Specifically, the protocol for finding the underlying type answers the questions of what to ``static_cast`` the value to, and what to construct it from. To support enum classes, I imagine it would have to be a traits class.

---

## Comment 2

> Username: viboes  
> Created at: 2018-02-12 06:41:51 UTC  
> Updated at: 2018-02-12 06:52:20 UTC  
> Url: https://github.com/boostorg/endian/issues/19#issuecomment-364839309  

I've an alternative C++11 solution to the UDT that I'd like to share here  
https://wandbox.org/permlink/WLSay4d5pJftn1Q6  
  
It uses a member endian_bufffer and the UDT needs to provide a underlying member function and be explicitly constructible from the underlying type. The underlying member function could be relaxed by using a non-member ADL `underlying` function. This function could defaults to static_cast<undelying_type<E>> for Enums.

---

## Comment 3

> Username: pdimov  
> Created at: 2019-04-29 16:35:23 UTC  
> Url: https://github.com/boostorg/endian/issues/19#issuecomment-487651239  

The constraints on `T` are now  
  
> When `Nbits` is equal to `sizeof(T)*8`, `T` must be a trivially copyable type (such as `float`) that is assumed to have the same endianness as `uintNbits_t`.  
>   
> When `Nbits` is less than `sizeof(T)*8`, `T` must be either a standard integral type (C++std, [basic.fundamental]) or an `enum`.
