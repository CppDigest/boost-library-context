# #1086 - BOOST_DESCRIBE_STRUCT doesn't work with derived structs [Closed]

> Username: nikitabuida  
> Created at: 2025-05-30 18:27:17 UTC  
> Updated at: 2025-06-25 13:15:02 UTC  
> Closed at: 2025-06-25 13:15:02 UTC  
> Url: https://github.com/boostorg/json/issues/1086  

Simple code (modified) to_json example gives me error static_assert failed: 'No suitable tag_invoke overload found for the type'.  
Such behaviour occurs only if using derived structs. I expected BOOST_DESCRIBE_STRUCT to provide tag_invoke in such cases.  
  
```  
#include <boost/describe.hpp>  
#include <boost/mp11.hpp>  
#include <boost/json.hpp>  
#include <boost/version.hpp>  
#include <type_traits>  
#include <vector>  
#include <map>  
  
namespace app  
{  
    struct A  
    {  
        int x;  
    };  
  
    BOOST_DESCRIBE_STRUCT(A, (), (x))  
  
    struct B : A  
    {  
        int c;  
    };  
  
    BOOST_DESCRIBE_STRUCT(B, (A), (c))  
  
} // namespace app  
  
#include <iostream>  
  
int main()  
{  
    app::B b;  
    b.x = 1;  
    b.c = 7;  
  
    std::cout << boost::json::value_from(b) << std::endl;  
}  
```  
  
Is it possible with current implementation?

---

## Comment 1

> Username: pdimov  
> Created at: 2025-05-31 00:45:31 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-2923837923  

As explained in the Boost.JSON documentation:  
  
https://www.boost.org/doc/libs/1_88_0/libs/json/doc/html/json/ref/boost__json__is_described_class.html  
  
structs with base classes don't get an implementation by default. To enable it, you have to specialize `boost::json::is_described_class` like this:  
  
```  
template<>  
struct boost::json::is_described_class<app::B>: std::true_type {};  
```  
  
Maybe we need to reconsider and enable it by default even with base classes; this could be another case where we've been too conservative. I'll move this issue to Boost.JSON where we can discuss this.

---

## Comment 2

> Username: grisumbras  
> Created at: 2025-05-31 08:12:24 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-2924654583  

I was trying to avoid opinionated defaults. The current behaviour leaves the decision to the user. Although, on the other hand, it's not like we provide options to select from. It's either "work this way" or "fails to build". So, maybe maybe allowing base classes shouldn't be a problem.  
  
Also, as far as I know it is actually the standard way class hierarchies are done in JSON representations.

---

## Comment 3

> Username: pdimov  
> Created at: 2025-05-31 08:46:38 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-2924704598  

Yes, I think that we should just make it work.

---

## Comment 4

> Username: grisumbras  
> Created at: 2025-06-19 09:24:26 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-2987382253  

So, I think we should enable it by default for classes with no non-public bases. This would be similar to how  we treat members.

---

## Comment 5

> Username: grisumbras  
> Created at: 2025-06-19 09:41:49 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-2987440337  

Actually, I don't think we can support non-public bases at all, because they are inaccessible bases for non-member code.

---

## Comment 6

> Username: grisumbras  
> Created at: 2025-06-24 10:24:14 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-2999709505  

I have encountered a problem. If a member shadows a base's member or if several bases have members with the same names, only the first such encountered member will be considered. This technically works (it succeeds and round-trips), but sounds very arbitrary. I also don't want to add a compile-time check for uniqueness of names. Do you still think we should support classes with bases by default?

---

## Comment 7

> Username: pdimov  
> Created at: 2025-06-24 10:31:15 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-2999730571  

If a member shadows another member, and you're using mod_inherited, the shadowed member is not returned by default (because it's mod_hidden.)  
  
If a member is ambiguous and not hidden, this would have to be checked and statically asserted, yes.

---

## Comment 8

> Username: grisumbras  
> Created at: 2025-06-24 11:36:34 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-2999983307  

Something like this? https://godbolt.org/z/GEsc4eezf

---

## Comment 9

> Username: grisumbras  
> Created at: 2025-06-24 15:36:07 UTC  
> Url: https://github.com/boostorg/json/issues/1086#issuecomment-3000991433  

https://godbolt.org/z/eWGE3dW66  
  
Clang < 7.0 doesn't like this. I guess, I can disable the assert for older clangs.
