# #102 - A small difference between `boost::pfr::get` and `std::get` [Open]

> Username: denzor200  
> Created at: 2022-08-27 05:23:08 UTC  
> Updated at: 2023-01-04 06:24:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/102  

As we know, `std::get` overloads for a `std::tuple` can't return values, it always returns a reference(https://en.cppreference.com/w/cpp/utility/tuple/get).  
This library shows itself as "tuple-like interface" and i suppose that `boost::pfr::get` must be fit to `std::get` in every detail.  
But i find that `boost::pfr::get` in some cases can return a value, and make a copy: https://godbolt.org/z/1GWaqsah4  
Can we fix it? Or is it better not to touch it, to prevent a breaking change?

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-08-31 18:24:58 UTC  
> Url: https://github.com/boostorg/pfr/issues/102#issuecomment-1233272264  

Looks like a bug. Could you please fix it and transform the godbolt snippet into a test?

---

## Comment 2

> Username: denzor200  
> Created at: 2022-09-01 07:58:35 UTC  
> Url: https://github.com/boostorg/pfr/issues/102#issuecomment-1233894316  

> Looks like a bug. Could you please fix it and transform the godbolt snippet into a test?  
  
Ok. I will fix it in the nearest future.

---

## Comment 3

> Username: denzor200  
> Created at: 2023-01-04 06:24:44 UTC  
> Url: https://github.com/boostorg/pfr/issues/102#issuecomment-1370531557  

yet more in msvc: https://godbolt.org/z/rjEcdqssK  
it also spoils const references.
