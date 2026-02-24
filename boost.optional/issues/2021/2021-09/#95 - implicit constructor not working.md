# #95 - implicit constructor not working [Open]

> Username: vinniefalco  
> Created at: 2021-09-05 18:35:12 UTC  
> Updated at: 2024-09-18 16:15:24 UTC  
> Url: https://github.com/boostorg/optional/issues/95  

calls to `f3` and `f4` do not compile:  
https://godbolt.org/z/K4c1edTK3  
  
```  
#include <optional>  
#include <string_view>  
#include <boost/optional.hpp>  
#include <boost/utility/string_view.hpp>  
  
void f1( std::optional< std::string_view > ) {}  
void f2( std::optional< boost::string_view > ) {}  
void f3( boost::optional< std::string_view > ) {}  
void f4( boost::optional< boost::string_view > ) {}  
  
int main()  
{  
    f1("hello");  
    f2("hello");  
    f3("hello");  
    f4("hello");  
}  
```

---

## Comment 1

> Username: akrzemi1  
> Created at: 2021-09-05 19:36:53 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-913215265  

This is by design. The constructor of `boost::optional<T>` from `U` is declared `explicit`. And I believe this decision is better than the one taken for `std::optional`. It avoids gotchas like this one: https://twitter.com/berglerma/status/1338918610652319745.

---

## Comment 2

> Username: pdimov  
> Created at: 2021-09-05 21:00:49 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-913226723  

I'm not sure why the gotcha applies here. It's caused by `operator==(std::optional<T> const&, std::optional<U> const&)` which is enabled when `T == U` exists; and in this case `T` is `optional<int>` and `U` is `int`, so we get to `operator==(optional<T> const&, U const&)` which is enabled again when `T == U` exists; and it does, because `T` and `U` are both `int`.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2021-09-06 05:48:23 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-913361086  

Yes, you're right: the gotcha is not directly related to the design decision. Sorry. So, let me rephrase my response.    
Making the constructor of `boost::optional<T>` from `U` explicit is a conscious decision, based on the premise that adding too much implicit conversions is potentially bug prone. While `std::optional` comes with far more implicit conversions, it is not clear if it is a superior design. `std::optional` has a number of gotchas that `boost::optional` doesn't that are caused by implicit conversions (albeit not by the one in question, but mainly from `optional<U>` to `optional<T>`).  
One such gotcha is when you convert from `optional<X>` to `optional<optional<X>>`: it can be handled by either `T -> optional<T>` or `optional<U> -> optional<T>` and an arbitrary choice is made, likely not the one that the user may intuitively expect.  
Therefore aiming at 100% compatibility with `std::optional` interface does not seem to be a reasonable goal for `boost::optional`.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-09-06 05:53:03 UTC  
> Updated at: 2021-09-06 05:53:17 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-913362840  

> aiming at 100% compatibility with std::optional interface does not seem to be a reasonable goal for boost::optional.  
  
Yeah, here's the thing though. Is `optional<optional<T>>` really a problem in practice? It is nice when Boost components offer superior functionality to their std equivalents (`boost::variant2` and `boost::error_code`) come to mind. But in this case we are not getting better functionality we are getting something that interoperates with other boost types poorly, and is worse off compared to its standard equivalent. For example:  
  
```  
std::optional< std::string_view > os;  
...  
if( os == "123 " ) // works  
  ...  
```  
  
while  
  
```  
boost::optional< boost::string_view > os;  
if( os == "123 " ) // fails  
   ...  
```  
  
Doesn't work. People already complain about libraries that use boost::error_code instead of std::error_code (e.g. asio) and that use boost::string_view instead of std::string_view. I think there is benefit to enabling practical usage even if it comes at the expense of some esoteric theoretical use-cases.

---

## Comment 5

> Username: akrzemi1  
> Created at: 2021-09-06 07:15:04 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-913404119  

> Is `optional<optional<T>>` really a problem in practice?  
  
When `optional<optional<T>>` appears in your program, you cannot easily see it, because the declaration is spilled across two places:  
  
``` C++  
  
template <typename T>  
void genericFun(const T& v)  
{  
  optional<T> optV = v;  
  assert(bool(optV)); // wil it pass for *every* T?  
}  
  
optional<int> myValue;  
genericFun(myValue);  
```   
  
> But in this case we are not getting better functionality   
  
The definition of "better" is not clear here. With `boost::optional` you get the subset of `std::optional` functionality that is known to have no gotchas. The interface is narrower, but is guaranteed to always (as opposed to 95% of the cases) do what you expect.    
  
> we are getting something that interoperates with other boost types poorly  
  
Can you provide an example of how `boost::optional` interoperates with other boost types poorly?  
  
> For example: [...]  
  
I can see how this is problematic. Is this the only use case that really shows up in practice? Because this one could be addressed in a different way. I will investigate if enabling `U -> optional<T>` doesn't introduce any gotchas (because the most prominent gotchas are introduced by other conversion `optional<U> -> optional<T>`).   
  
> I think there is benefit to enabling practical usage even if it comes at the expense of some esoteric theoretical use-cases.  
  
The way I see it, we are talking here about the trade-off where you get a convenience feature that does what you expect in 95% of the cases, and in the remaining 5% cases it compiles and does something opposite to what you expect at runtime.  This doesn't sound like the right trade-off for me. But I might be wrong about the "does something opposite to what you expect", so, as I said, I will investigate this.

---

## Comment 6

> Username: pdimov  
> Created at: 2021-09-06 17:01:17 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-913772764  

Comparing `optional<T>` to `T` has its own issues. For example, for `optional<string_view>`, `if( opt != "" )` fails when `opt` is empty.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-09-06 17:52:27 UTC  
> Updated at: 2021-09-06 17:52:42 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-913791268  

Thanks to Peter I am now unsure if the comparison should work. Because of this, I am trending away from optional entirely, in my interfaces (working on Boost.URL at the moment). However, I still think there could be value in offering a drop-in replacement for `std::optional` for C++11 and C++14 users.

---

## Comment 8

> Username: RedBeard0531  
> Created at: 2021-09-17 09:12:14 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-921639279  

> Comparing `optional<T>` to `T` has its own issues. For example, for `optional<string_view>`, `if( opt != "" )` fails when `opt` is empty.  
  
@pdimov what do you mean by "fails" here? I would take that word to mean that it either asserts, throws an exception, or is violating some precondition. But that doesn't seem to be the case: https://godbolt.org/z/dTTerTzch. It correctly evaluates to true, because `opt` does not hold the empty string (or any string at all for that matter).  The point of the `optional<string_view>` type is to make an extra empty state distinct from (ie, not equal to) `""`, and that is exactly what you are seeing.  
  
That said, I could see the ordered relop results being surprising, but that is the cost of imposing a total order over all states of a type (assuming that `T` is totally ordered). But that is unrelated to `==` and `!=`, which both seem to have "obviously" correct behavior to me.

---

## Comment 9

> Username: pdimov  
> Created at: 2021-09-17 11:25:41 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-921720491  

"Fails" in the sense that it often behaves in a way that fails to meet the (original or maintenance) programmer's expectation. I know that it's formally correct, because `nullopt != ""`.

---

## Comment 10

> Username: akrzemi1  
> Created at: 2024-09-18 10:01:30 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-2358033556  

This issue is similar to the closed https://github.com/boostorg/optional/issues/96.  
I have now provided a section in the docs that describes the rationale behind the divergence from `std::opitonal` semantics:  
https://www.boost.org/doc/libs/develop/libs/optional/doc/html/boost_optional/design/convenience_conversions_and_deductions.html  
  
For now, it is only in branch `develop`.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2024-09-18 15:54:14 UTC  
> Updated at: 2024-09-18 15:54:41 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-2358846872  

Boost users on C++11 and C++14 have few choices for accessing `optional`:  
  
* Upgrade to C++17  
* Use `boost::optional` and diverge from the standard  
* Use a third-party `optional` library which is identical to the standard  
  
It is useful to have a diverging implementation, but this doesn't serve the need for users who just want to support older versions of C++ by using an identical polyfill.  
  
I think there is value in also having a `boost::std_optional` which is exactly like the C++17 version, for users who simply want to have a working `std::optional` in C++11 or C++14.

---

## Comment 12

> Username: akrzemi1  
> Created at: 2024-09-18 16:15:23 UTC  
> Url: https://github.com/boostorg/optional/issues/95#issuecomment-2358891696  

Strictly speaking, is will never be a drop-in replacement for `std::optional` because of names like `none` vs `nullopt` and `in_place` vs `in_place_init`.
