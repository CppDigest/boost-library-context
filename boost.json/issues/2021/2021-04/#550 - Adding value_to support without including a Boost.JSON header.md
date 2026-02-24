# #550 - Adding value_to support without including a Boost.JSON header [Closed]

> Username: pdimov  
> Created at: 2021-04-27 14:47:58 UTC  
> Updated at: 2024-04-08 16:56:17 UTC  
> Closed at: 2024-04-08 16:56:17 UTC  
> Url: https://github.com/boostorg/json/issues/550  

Similar to #549, but in the other direction; it should be possible to add `value_to` support without including a Boost.JSON header. A minimal example is in https://godbolt.org/z/WsG33GrMP. Unlike the `value_from` case, this compiles, and the only reason it doesn't link is that the signature of `value_to` does not match the documented one.  
  
I'm not sure why it's necessary for `value_to` to disappear from overload resolution when `T` is a reference. `value_to` is not intended to be an overload set. References can be disallowed by a static_assert in the body.  
  
Not taking `value` in order to disallow implicit conversions is I suppose legitimate, but it can be achieved by adding a deleted templated overload (https://godbolt.org/z/Tqonvj9bT).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-04-27 14:50:57 UTC  
> Url: https://github.com/boostorg/json/issues/550#issuecomment-827667559  

These declarations should have comments explaining the rationale for their signatures

---

## Comment 2

> Username: beached  
> Created at: 2021-04-27 15:34:01 UTC  
> Url: https://github.com/boostorg/json/issues/550#issuecomment-827703634  

Have you considered not doing this and then they put their value_to into a header that includes their type and the boost.json header.  That accomplishes the same thing of firewalling boost into a TU, no?

---

## Comment 3

> Username: pdimov  
> Created at: 2021-04-27 16:15:51 UTC  
> Url: https://github.com/boostorg/json/issues/550#issuecomment-827734470  

The easiest fix here is to change the specification of `value_to` as follows:  
```  
template<class T> T value_to( value const& v );  
template<class T, class U> T value_to( U const& v ) = delete;  
```

---

## Comment 4

> Username: EikeAtOT  
> Created at: 2022-07-27 15:16:19 UTC  
> Url: https://github.com/boostorg/json/issues/550#issuecomment-1196894622  

Unfortunately the changes in 31dd29523292e579497158b79703a4e7e3f56a3c (see the above PR) make `value_to` available to global ADL. For our codebase that posed a breaking change causing ambiguous calls (we have a `value_to` in our own namespace). Before the above changes that problem did not occur.

---

## Comment 5

> Username: grisumbras  
> Created at: 2022-07-27 15:38:22 UTC  
> Url: https://github.com/boostorg/json/issues/550#issuecomment-1196922957  

Can you provide a minimal example of what exactly was broken? We only have value_to overloads in namespace boost::json.

---

## Comment 6

> Username: EikeAtOT  
> Created at: 2022-07-28 08:05:07 UTC  
> Updated at: 2022-07-28 08:07:44 UTC  
> Url: https://github.com/boostorg/json/issues/550#issuecomment-1197805416  

```  
#include <type_traits>  
#include <iostream>  
  
namespace A  
{  
    class Arg {};  
  
    template<typename T, typename U = std::enable_if<std::is_same_v<T, Arg>, void>>  
    void foo(const T&)  
    {  
        std::cout << "A::foo" << std::endl;  
    }  
  
    void bar(const Arg&)  
    {  
        std::cout << "A::bar" << std::endl;  
    }  
}  
  
namespace B::detail  
{  
      
inline void foo(const A::Arg&)   
{  
    std::cout << "B::detail::foo" << std::endl;  
}  
  
inline void bar(const A::Arg&)  
{  
    std::cout << "B::detail::bar" << std::endl;  
}  
  
int client()  
{  
    A::Arg x;  
    foo(x);  
  
    bar(x);  
    return 0;  
}  
  
}  
  
int main(int argc, char** argv)  
{  
    B::detail::client();  
    return 0;  
}  
```  
  
In `client()` the unqualified call to `foo` is well defined pointing to `B::detail::foo` while the unqualified call to `bar` is regarded ambiguous. The namespace `A` is basically `boost::json`. So it looks like when the argument is inferred through a template the overload resolution has lower precedence than the nearest overload in the current enclosing namespace.

---

## Comment 7

> Username: grisumbras  
> Created at: 2022-11-16 12:17:41 UTC  
> Url: https://github.com/boostorg/json/issues/550#issuecomment-1316921345  

@EikeAtOT can you provide an example with `value_to` that has become broken after 31dd29523292e579497158b79703a4e7e3f56a3c, so that I can evaluate if this is something we can and should fix?
