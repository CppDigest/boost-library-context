# #98 - Interoperability with std::optional<bool> [Closed]

> Username: pfee  
> Created at: 2021-11-19 12:24:12 UTC  
> Updated at: 2021-12-27 09:27:00 UTC  
> Closed at: 2021-12-27 09:27:00 UTC  
> Url: https://github.com/boostorg/optional/issues/98  

I have encountered unexpected results when assigning from an empty `std::optional<bool>` to a `boost::optional<bool>`.  Instead of getting an empty `optional`, I find that the result is an `optional` populated with a value of `false`.  
  
In the reverse scenario, assignment from `boost::optional<bool>` to `std::optional<bool>` fails to compile.  Compilation failure may be preferable to unexpected results at runtime.  
  
This is illustrated with the following example:  
  
```  
#include <optional>  
#include <boost/optional.hpp>  
#include <cassert>  
  
boost::optional<bool> getB()  
{  
    return {};  
}  
  
std::optional<bool> getS()  
{  
    return {};  
}  
  
int main()  
{  
    std::optional<bool> s;  
    boost::optional<bool> b;  
    assert(!s);  
    assert(!b);  
  
    // s = getB(); // Fails to compile (good)  
    b = getB();  
    assert(!s);  
    assert(!b);  
  
    s = getS();  
    b = getS(); // Want compile time error here  
    assert(!s);  
    assert(!b); // Runtime failure, bad  
}  
```  
  
I was able to patch my local installation (Boost 1.77) to get the desired compilation error.  
  
```  
--- optional.hpp.orig   2021-10-29 21:50:41.000000000 +0100  
+++ optional.hpp        2021-11-18 16:58:15.021204511 +0000  
@@ -997,6 +997,12 @@  
     BOOST_DEDUCED_TYPENAME boost::enable_if<optional_detail::is_optional_val_init_candidate<T, Expr>, optional&>::type  
     operator= ( Expr&& expr )  
       {  
+        static_assert(!(  
+            std::is_same<T, bool>() && std::is_same<Expr, std::optional<bool>&>()  
+            ), "Assignment from std::optional<bool> not permitted");  
+        static_assert(!(  
+            std::is_same<T, bool>() && std::is_same<Expr, std::optional<bool>>()  
+            ), "Assignment from std::optional<bool> not permitted");  
         this->assign_expr(boost::forward<Expr>(expr),boost::addressof(expr));  
         return *this ;  
       }  
```  
  
Types other than `bool` already fail to compile.  It's only `optional<bool>` interoperability that's an issue.  
  
Would it be appropriate to adopt this change in boost::optional?  It would be an API break.  Alternatively, generating a warning in this situation may be useful and would avoid an API break.  Is there a `static_warn` to complement `static_assert`?

---

## Comment 1

> Username: akrzemi1  
> Created at: 2021-11-19 12:40:47 UTC  
> Url: https://github.com/boostorg/optional/issues/98#issuecomment-974038396  

Thank you for the report. It looks like type trait `is_optional_val_init_candidate` does not work correctly. Le me investigate.

---

## Comment 2

> Username: akrzemi1  
> Created at: 2021-11-19 13:46:09 UTC  
> Url: https://github.com/boostorg/optional/issues/98#issuecomment-974086237  

It delegates to `boost::is_constructible`: https://github.com/boostorg/optional/blob/develop/include/boost/optional/optional.hpp#L803  
It should delegate to `boost::is_convertible` instead.

---

## Comment 3

> Username: akrzemi1  
> Created at: 2021-11-20 01:11:31 UTC  
> Url: https://github.com/boostorg/optional/issues/98#issuecomment-974565441  

Should be fixed in `develop` branch.
