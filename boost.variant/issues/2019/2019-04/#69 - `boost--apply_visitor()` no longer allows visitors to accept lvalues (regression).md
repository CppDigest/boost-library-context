# #69 - `boost::apply_visitor()` no longer allows visitors to accept lvalues (regression) [Closed]

> Username: ras0219-msft  
> Created at: 2019-04-22 03:15:43 UTC  
> Updated at: 2019-08-09 18:06:09 UTC  
> Closed at: 2019-05-01 07:17:14 UTC  
> Url: https://github.com/boostorg/variant/issues/69  

As part of updating vcpkg to use boost 1.70[1], I keep running into issues around the use of `boost::apply_visitor`. Specifically, it looks like in 1.70, it is no longer valid to visit by lvalue reference:  
```  
#include <boost/variant.hpp>  
  
struct T{};  
struct U{};  
  
struct V {  
        void operator()(T&) const {}  
        void operator()(U&) const {}  
};  
  
int main()  
{  
    V v;  
    boost::variant<T,U> var(U{});  
  
    boost::apply_visitor(v, var);  
    return 0;  
}  
```  
  
The commit that broke this is https://github.com/boostorg/variant/commit/4b37f9b804ee39e96184224447eecc0ad4dc4254; can someone help to confirm if this was intended to break or whether this is an unintended regression?  
  
@Kojoley as the commit author  
  
[1] https://github.com/Microsoft/vcpkg/pull/6141

---

## Comment 1

> Username: apolukhin  
> Created at: 2019-05-01 07:17:14 UTC  
> Url: https://github.com/boostorg/variant/issues/69#issuecomment-488225075  

Fixed in PR #68

---

## Comment 2

> Username: yurivict  
> Created at: 2019-06-16 16:49:08 UTC  
> Updated at: 2019-06-16 16:49:24 UTC  
> Url: https://github.com/boostorg/variant/issues/69#issuecomment-502467903  

@apolukhin Please make a boost release with this fix. This problem was apparently a regression that broke the facebook's ```fuzz``` project. See here: https://github.com/facebookincubator/fizz/issues/33

---

## Comment 3

> Username: apolukhin  
> Created at: 2019-06-21 20:26:59 UTC  
> Url: https://github.com/boostorg/variant/issues/69#issuecomment-504562209  

The fix will be in Boost 1.71. Until then you can manually apply the patch from https://github.com/boostorg/variant/pull/68 to the 1.70 version

---

## Comment 4

> Username: agnosticdev  
> Created at: 2019-08-09 18:06:09 UTC  
> Url: https://github.com/boostorg/variant/issues/69#issuecomment-520012355  

Thank you for this patch I was able to apply it to `Boost 1.70` and get the `fizz` project building on Mojave 10.14.5.
