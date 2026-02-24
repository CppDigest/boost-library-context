# #19 - Poll: Should Hana provide MPL-like metafunctions for type-only computations? [Closed]

> Username: ldionne  
> Created at: 2015-03-07 16:37:25 UTC  
> Updated at: 2015-05-08 20:13:52 UTC  
> Closed at: 2015-05-08 20:13:52 UTC  
> Url: https://github.com/boostorg/hana/issues/19  

As expressed on the [Boost mailing list](http://article.gmane.org/gmane.comp.lib.boost.devel/258100), there seems to be some interest from the community in having a `meta` namespace in which a type-level interface (like MPL and `<type_traits>`) for a subset of the library is provided. I am reluctant to add it because I see it as encouraging non-idiomatic Hana, but I want to know how badly people want it. Please write a comment saying whether you would like that, and ideally why.  
  
For an example of what's being proposed, we would be able to write  
  
``` cpp  
static_assert(  
    meta::transform<meta::tuple<int, char const, void>,  
        meta::quote<std::add_pointer>>{} ==  
    meta::tuple<int*, char const*, void*>{}  
 , "");  
  
using Xs = meta::transform<  
    meta::tuple<int, char const, void>,   
    meta::quote<std::add_pointer>  
>;  
```  
  
instead of  
  
``` cpp  
static_assert(  
    hana::transform(hana::tuple_t<int, char const, void>,  
        hana::metafunction<std::add_pointer>) ==  
    hana::tuple_t<int*, char const*, void*>  
, "");  
  
using Xs = decltype(hana::transform(  
    hana::tuple_t<int, char const, void>,   
    hana::metafunction<std::add_pointer>  
))::type;  
```

---

## Comment 1

> Username: viboes  
> Created at: 2015-03-07 18:45:32 UTC  
> Url: https://github.com/boostorg/hana/issues/19#issuecomment-77703401  

My suggestion was not exactly to have an MPL-like interface, but an interface more in line with the current C++ standard that works with variadic types and integral constants.  Something that could be proposed to the C++ standard committee.

---

## Comment 2

> Username: ldionne  
> Created at: 2015-03-07 18:52:41 UTC  
> Url: https://github.com/boostorg/hana/issues/19#issuecomment-77703714  

I'm sorry I misunderstood. I updated the wording to reflect better what I think you meant. Also, are you saying that the whole library could be proposed to the standard, or only that subset?

---

## Comment 3

> Username: ldionne  
> Created at: 2015-05-08 20:13:52 UTC  
> Url: https://github.com/boostorg/hana/issues/19#issuecomment-100346706  

I have grown more and more unfavourable to this idea. The main reason for using Hana is because value-level syntax makes it much easier to metaprogram. Providing a classic interface for type-level computations is making the library more complex for the sake of what I consider to be bad practice. If someone can point out to me at least one use case in which it would be significantly better to use the classic syntax, I'll reconsider this. For the moment, closing.
