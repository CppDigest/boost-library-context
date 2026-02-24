# #121 - n-ary transform function [Open]

> Username: grisumbras  
> Created at: 2024-04-11 10:51:38 UTC  
> Updated at: 2026-01-15 19:15:05 UTC  
> Url: https://github.com/boostorg/system/issues/121  

The way I see it, it should be something like `system::invoke` that invokes a callable with N (0+) arguments of which some can be results. In addition, for convenience it should invoke the callable similar to what `std::invoke` does.  
  
Example  
  
```c++  
Foo  
Processor::compose_abc(A const&, B const&, C const&);  
  
system::result<Foo>  
read_config(Processor& p, Config const& config)  
{  
    auto a = maybe_get_a(config);  
    auto b = maybe_get_b(config);  
    auto c = maybe_get_c(config);  
    return system::invoke(&Processor::compose_abc, p, a, b, c);  
}  
```  
  
One interesting question is whether `result<void>` arguments should be allowed. The semantics would be to ignore them when invoking the callable, but that would complicate implementation.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-04-11 11:05:53 UTC  
> Url: https://github.com/boostorg/system/issues/121#issuecomment-2049443257  

I assume the error types would need to match?

---

## Comment 2

> Username: grisumbras  
> Created at: 2024-04-11 11:16:19 UTC  
> Url: https://github.com/boostorg/system/issues/121#issuecomment-2049458663  

Yes, definitely.

---

## Comment 3

> Username: pdimov  
> Created at: 2024-05-21 16:44:42 UTC  
> Url: https://github.com/boostorg/system/issues/121#issuecomment-2123033624  

https://godbolt.org/z/qencc7cnz

---

## Comment 4

> Username: grisumbras  
> Created at: 2024-05-21 21:40:22 UTC  
> Url: https://github.com/boostorg/system/issues/121#issuecomment-2123485716  

Looks pretty good. It doesn't do the invoke thing that allows using pointers to members. Is that planned?  
  
Also, I requested nullary function support, but now I don't remember what for.

---

## Comment 5

> Username: pdimov  
> Created at: 2024-05-21 23:30:58 UTC  
> Url: https://github.com/boostorg/system/issues/121#issuecomment-2123591496  

Member function support is something I need to add to the "monadic" operators as well. I'll figure something out.

---

## Comment 6

> Username: pdimov  
> Created at: 2025-12-01 19:56:11 UTC  
> Url: https://github.com/boostorg/system/issues/121#issuecomment-3598590975  

It strikes me that in addition to `unwrap_and_invoke` it would also be nice to have `unwrap_and_construct`, to avoid the need to obtain the `construct<T>` helper from somewhere.  
  
This enables things like  
```  
    friend result<X> tag_invoke( boost::json::try_value_to_tag<X> const&, boost::json::value const& jv )  
    {  
        return unwrap_and_construct<X>(  
            boost::json::try_value_to< decltype(X::a) >( jv.at("a") ),  
            boost::json::try_value_to< decltype(X::b) >( jv.at("b") )  
        );  
    }  
```

---

## Comment 7

> Username: pdimov  
> Created at: 2026-01-15 19:15:04 UTC  
> Url: https://github.com/boostorg/system/issues/121#issuecomment-3756452158  

https://godbolt.org/z/c7vjj4oE5
