# #192 - collection_size_type missing increment/decrement operators [Open]

> Username: geoffthemedio  
> Created at: 2020-03-19 13:53:23 UTC  
> Updated at: 2020-03-24 19:27:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/192  

Various places such as [here](https://github.com/boostorg/serialization/blob/develop/include/boost/serialization/vector.hpp#L191) use `operator--` or `operator++` of `collection_size_type`, but [these are not defined](https://github.com/boostorg/serialization/blob/develop/include/boost/serialization/collection_size_type.hpp#L20).  
  
This doesn't seem to be a problem for most compilers, but it's apparently causing [compile errors with Fedora 32](https://github.com/freeorion/freeorion/issues/2795) for my project:  
````  
/usr/include/boost/serialization/vector.hpp: In function ‘void boost::serialization::save(Archive&, const std::vector<bool, Allocator>&, unsigned int)’:  
/usr/include/boost/serialization/vector.hpp:191:16: error: no post-decrement operator for type  
  191 |     while(count-- > 0){  
      |                ^~  
````  
  
A[ minimal test case](https://github.com/freeorion/freeorion/issues/2795#issuecomment-601420863) suggests the issue is with GCC 10.0.1

---

## Comment 1

> Username: correaa  
> Created at: 2020-03-20 02:18:58 UTC  
> Updated at: 2020-03-20 02:19:42 UTC  
> Url: https://github.com/boostorg/serialization/issues/192#issuecomment-601498240  

Indeed it is quite strange that it works at all, operator++ operator-- must be defined somewhere else for it too work. Perhaps it has to do with this line (regarding making `collection_size` a strong typedef) being commented   
https://github.com/boostorg/serialization/blob/develop/include/boost/serialization/collection_size_type.hpp#L18  
  
but it has been this way for a long time.  
  
There is a clue here: https://github.com/freeorion/freeorion/issues/2795#issuecomment-601179213

---

## Comment 2

> Username: robertramey  
> Created at: 2020-03-20 05:09:40 UTC  
> Url: https://github.com/boostorg/serialization/issues/192#issuecomment-601540898  

this is very interesting.  I used be defined by STRONG_TYPEDEF.  But at one point it was replaced by a (stronger) custom type.  Looking at the code, I can't see why it would work.  So it seems that gcc 10 is correct.  Or not.  I see that there is an implicit conversion operator to the base type which is an unsigned int.  I'm guessing that this or something like this is what makes it work.  I'll look into it.

---

## Comment 3

> Username: correaa  
> Created at: 2020-03-22 05:10:28 UTC  
> Url: https://github.com/boostorg/serialization/issues/192#issuecomment-602149129  

Adding the operators explicitly could help in this case, but I couldn't replicate the original problem with any compiler, including gcc trunk   
https://godbolt.org/z/EkEUnF  
  
It seems that the having the cast operator to a reference is enough to allow increment and decrement. Perhaps it is a bug in GCC 10.0.1.

---

## Comment 4

> Username: robertramey  
> Created at: 2020-03-22 05:56:39 UTC  
> Url: https://github.com/boostorg/serialization/issues/192#issuecomment-602152225  

> typedef std::size_t base_type;  
> base_type t;  
...  
>    // used for text output  
>    operator base_type () const {  
>        return t;  
>    }  
  
This is the "problem".  I'm guessing that it's correct C++.  But it certainly do what one would expect by looking at at.  Actually I'd like to permit ++ and -- and input/output but prohibit other operations.  This would require some thought to figure out how do this in a way that's pretty transparent.  I'm guess that eliminating the implicit cast above and just use implement the required operations in terms of the class we're defining - that is - collection_size_type.  The current code is so clever, we and the compiler need to spend a lot of time figuring out what it does - it's just not obvious by looking at it.  I would like to see it fixed, but currently I'm swamped.  If you want to fix it and test it, I'll merge it in.

---

## Comment 5

> Username: correaa  
> Created at: 2020-03-22 06:12:14 UTC  
> Updated at: 2020-03-22 22:24:36 UTC  
> Url: https://github.com/boostorg/serialization/issues/192#issuecomment-602153171  

Hi Robert,   
  
Actually the important line is this other one.  
```  
 operator base_type& ()  
```  
(with the ampersand). It seems that the compiler is able to look for conversions in which `--` and `++` make sense. It is as if `operator++(int&)` (or `--`) is defined somewhere and the collection type is converted to this reference type (so far).  
  
(see here https://godbolt.org/z/_ecqp5)

---

## Comment 6

> Username: robertramey  
> Created at: 2020-03-22 06:15:26 UTC  
> Url: https://github.com/boostorg/serialization/issues/192#issuecomment-602153622  

Right

---

## Comment 7

> Username: Romain-Geissler-1A  
> Created at: 2020-03-24 19:27:49 UTC  
> Url: https://github.com/boostorg/serialization/issues/192#issuecomment-603459677  

Hi,  
  
Indeed the boost code is a bit weird and somehow I would have expected explicit operators for increment/decrement, however this seems like a gcc regression. Note that it happens only when in a template context, hinting more at a gcc bug. I have reported it in gcc: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=94309  
  
Cheers,  
Romain
