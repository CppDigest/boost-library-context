# #763 - x3::double_ failed to parse 1.D-3 [Closed]

> Username: xiaowen64  
> Created at: 2023-06-09 18:46:17 UTC  
> Updated at: 2025-05-09 22:53:18 UTC  
> Closed at: 2025-05-09 22:53:16 UTC  
> Url: https://github.com/boostorg/spirit/issues/763  

it is pretty common to have double precision floating point numbers to be written as something like 1.D-3. I believe many Fortran run-time libs does that. I traced it to x3/numeric/real_policies.hpp where a small change of below fixed it. should an option be provided to cope with this situation?  
  
```  
        template <typename Iterator>  
        static bool  
        parse_exp(Iterator& first, Iterator const& last)  
        {  
            if (first == last || (*first != 'e' && *first != 'E' && *first != 'd' && *first != 'D'))  
                return false;  
            ++first;  
            return true;  
        }  
```

---

## Comment 1

> Username: Kojoley  
> Created at: 2023-06-09 19:16:13 UTC  
> Url: https://github.com/boostorg/spirit/issues/763#issuecomment-1585028708  

The function you are citing is customizable via [RealPolicies](https://www.boost.org/doc/libs/1_82_0/libs/spirit/doc/html/spirit/qi/reference/numeric/real.html#spirit.qi.reference.numeric.real._code__phrase_role__identifier__realpolicies__phrase___code__specializations).

---

## Comment 2

> Username: xiaowen64  
> Created at: 2023-06-09 19:32:20 UTC  
> Url: https://github.com/boostorg/spirit/issues/763#issuecomment-1585043617  

yeah, I thought about that as a work-around. Given that this notation is pretty common, actually just found out it's stipulated by [Fortran Standard](https://docs.oracle.com/cd/E19957-01/805-4939/6j4m0vn6p/index.html#c400041361f4), it'd be more convenient to support it (and the 'Q' notation) natively.

---

## Comment 3

> Username: Kojoley  
> Created at: 2023-06-09 19:38:53 UTC  
> Url: https://github.com/boostorg/spirit/issues/763#issuecomment-1585048947  

These `D` and `Q` are special in a way that they denote a type, it seems wrong to me to allow `Q` for `x3::double_` or `D` for `x3::float_`. Anyway `atof` doesn't support them so I don't see a need to change the default behavior. Again, that's the situation RealPolicies are designed for.

---

## Comment 4

> Username: xiaowen64  
> Created at: 2023-06-09 20:09:55 UTC  
> Url: https://github.com/boostorg/spirit/issues/763#issuecomment-1585076693  

You are right, 'D' for x3::float_ sounds odd. How about allowing 'D' for x3::double_? It sounds natural.

---

## Comment 5

> Username: Kojoley  
> Created at: 2023-06-09 20:26:08 UTC  
> Url: https://github.com/boostorg/spirit/issues/763#issuecomment-1585090395  

> It sounds natural.  
  
No, it's not. It's a Fortran language syntax thing, `std::atof` and Spirit parsers don't support C and C++ language syntax things like `1.0f`, `1.0d`, `1.0f64` and etc., why Fortran's should be? Why also not support Fortran's `1.345E-10_8`?

---

## Comment 6

> Username: saki7  
> Created at: 2025-05-09 22:53:17 UTC  
> Url: https://github.com/boostorg/spirit/issues/763#issuecomment-2868014293  

Closing as stale. `real_policies` can be used for this purpose.
