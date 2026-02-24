# #590 - json::object ctor not found in boost; works when built as standalone [Closed]

> Username: cuzdav  
> Created at: 2021-06-30 12:26:27 UTC  
> Updated at: 2024-04-08 17:40:01 UTC  
> Closed at: 2024-04-08 17:40:01 UTC  
> Url: https://github.com/boostorg/json/issues/590  

Boost 1.76  
  
When -DBOOST_JSON_STANDALONE is defined (or built against the actual freestanding library), the following code compiles.  When it is built with boost and the macro is not defined, the code fails with an error:  
```  
error: no matching function for call to 'boost::json::object::object(<brace-enclosed initializer list>)'  
         })  
         ^  
```  
  
Here's a minimized example:  
```cpp  
#include <boost/json.hpp>  
using SV = std::string_view;  
  
int main() {  
    boost::json::object foo {  
        std::pair("R", boost::json::object{  
            std::pair<SV, SV>("k", "value")  
        })  
    };  
}  
```  
  
If I change from `string_view` to `string` it also works.  However, I didn't realize there were visible functional differences between the modes like this, and if this cannot be fixed, it might be worthwhile to add to the differences between boost/standalone documentation.  
  
https://godbolt.org/z/sM4jePs4j  
  
This occurs on every version of gcc I've tried from version 7 to current trunk, c++17 or c++20 mode.

---

## Comment 1

> Username: beached  
> Created at: 2021-06-30 13:02:30 UTC  
> Url: https://github.com/boostorg/json/issues/590#issuecomment-871384706  

It think this is the type you need is boost::string_view in non-freestanding mode

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-06-30 13:32:29 UTC  
> Url: https://github.com/boostorg/json/issues/590#issuecomment-871408011  

What you actually need is `boost::json::string_view`, which is an alias to one or the other, depending on whether standalone mode is enabled.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-06-30 14:15:43 UTC  
> Url: https://github.com/boostorg/json/issues/590#issuecomment-871444281  

>  it might be worthwhile to add to the differences between boost/standalone documentation.  
  
These differences are documented here:  
  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/overview.html#json.overview.requirements.without_boost  
  
> Aliases for standard types use their std equivalents

---

## Comment 4

> Username: cuzdav  
> Created at: 2021-06-30 14:46:40 UTC  
> Url: https://github.com/boostorg/json/issues/590#issuecomment-871469555  

Thanks to all.  I clearly made a mistake, but did not realize that direct  
use of the std:: types was discouraged (and that json alias types were  
encouraged.)  I had seen the documentation that said "aliases for standard  
types use their std equivalents" but being new to this library,  it didn't  
indicate what practical ramifications that entails, and I certainly did not  
expect it to result in interface differences that broke some builds but not  
others.  It sounded more of an implementation detail.  
  
Also, for the "Construct from initializer-list" constructor documentation:  
  
object <https://www.boost.org/doc/libs/1_76_0/libs/json/doc/html/json/ref/boost__json__object/object/overload10.html>(  
    std::initializer_list< std::pair< string_view, value_ref >> init,  
    storage_ptr sp = {});  
  *» more... <https://www.boost.org/doc/libs/1_76_0/libs/json/doc/html/json/ref/boost__json__object/object/overload10.html>*  
  
  
It uses "std::initializer_list" and "std::pair", the only namespaces shown  
are "std::" so that's what was in my head.  When I saw "string_view"--I  
*know* that's in the std:: namespace so that's what I used.  I'd suggest  
that for boost-provided types that also appear in the standard library, if  
the boost version is required the documentation and examples should  
explicitly qualify its names with a disambiguating namespace to make it  
clear.  For example, there would have been no frustration or confusion if  
the docs had said:  
  
object <https://www.boost.org/doc/libs/1_76_0/libs/json/doc/html/json/ref/boost__json__object/object/overload10.html>(  
    std::initializer_list< std::pair< *json::*string_view, value_ref >> init,  
    storage_ptr sp = {});  
  *» more... <https://www.boost.org/doc/libs/1_76_0/libs/json/doc/html/json/ref/boost__json__object/object/overload10.html>*  
  
  
Thanks, I'm no longer blocked, but hope this helps someone in the future.  
:)  
Chris  
  
On Wed, Jun 30, 2021 at 9:15 AM Vinnie Falco ***@***.***>  
wrote:  
  
> it might be worthwhile to add to the differences between boost/standalone  
> documentation.  
>  
> These differences are documented here:  
>  
>  
> https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/overview.html#json.overview.requirements.without_boost  
>  
> Aliases for standard types use their std equivalents  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/json/issues/590#issuecomment-871444281>, or  
> unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AEU3D3W7TJ4J4OJJ7R5XETTTVMRJTANCNFSM47SHI2OA>  
> .  
>

---

## Comment 5

> Username: grisumbras  
> Created at: 2021-06-30 15:26:26 UTC  
> Updated at: 2021-06-30 15:26:50 UTC  
> Url: https://github.com/boostorg/json/issues/590#issuecomment-871501767  

[Quick look](https://www.boost.org/doc/libs/1_76_0/libs/json/doc/html/json/quick_look.html) mentions that all code in the docs assumes `using namespace boost::json`. This means all types and functions without an explicit namespace are from namespace `boost::json`.

---

## Comment 6

> Username: vinniefalco  
> Created at: 2021-06-30 16:20:31 UTC  
> Url: https://github.com/boostorg/json/issues/590#issuecomment-871544299  

We will very likely fix this in Boost itself, so that `std::string_view` interoperates better with `boost::string_view`.
