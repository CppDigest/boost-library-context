# #1607 - Library headers use deprecated `buffers` calls [Closed]

> Username: sehe  
> Created at: 2019-05-10 20:16:21 UTC  
> Updated at: 2024-03-03 17:20:34 UTC  
> Closed at: 2019-06-26 17:34:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1607  

Whe compiling code like:  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/http.hpp>  
#include <iostream>  
  
int main() {  
    boost::beast::http::response<boost::beast::http::dynamic_body> res;  
    std::cout << res.base() << std::endl;  
}  
```  
With 1.70.0+ without `BOOST_BEAST_ALLOW_DEPRECATED` defined, you static asserts:  
  
```  
"The function buffers() is deprecated, use make_printable() instead, or define BOOST_BEAST_ALLOW_DEPRECATED to silence this error."  
```  
  
These originate e.g. https://github.com/boostorg/beast/blob/d1dabebd8352ce0e854474837ed11a27cb688edc/include/boost/beast/http/impl/write.hpp#L906  
  
Looks like those uses of `buffers` should be replaced with `make_printable` indeed. (It does work with my test)

---

## Comment 1

> Username: djarek  
> Created at: 2019-05-20 17:07:10 UTC  
> Url: https://github.com/boostorg/beast/issues/1607#issuecomment-494072265  

Should be fixed in v256.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2019-06-19 17:17:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1607#issuecomment-503651283  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2019-06-26 17:34:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1607#issuecomment-505972911  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 4

> Username: Poojabhagat1  
> Created at: 2024-02-29 08:22:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1607#issuecomment-1970635729  

The function buffers() is deprecated, use make_printable() instead   
   how to find the version in which this change happen?

---

## Comment 5

> Username: ashtum  
> Created at: 2024-02-29 08:42:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1607#issuecomment-1970665933  

It happened in Boost 1.70: https://www.boost.org/doc/libs/1_84_0/libs/beast/doc/html/beast/release_notes.html#beast.release_notes.boost_1_70  
This is the specific commit that made the change: https://github.com/boostorg/beast/commit/0006ab3b2bbb2478254eb490679d58a5613c7b0c

---

## Comment 6

> Username: sehe  
> Created at: 2024-03-03 17:18:36 UTC  
> Updated at: 2024-03-03 17:20:34 UTC  
> Url: https://github.com/boostorg/beast/issues/1607#issuecomment-1975235002  

@Poojabhagat1  and to answer the actual question, this is how you can find the version in which this change happens:  
  
![image](https://github.com/boostorg/beast/assets/324097/3b44e9a6-6e0f-493e-b5ec-82cc86dae776)  
  
```bash  
git log -Smake_printable --oneline  
git tag --contains 0006ab3b --column   
```  
  
To try that yourself. As you can see you can also refer to the release notes (e082caae):  
  
```  
+  ['Actions Required]:  
+  * Replace `buffers` with `make_printable`, and include  
+    "make_printable.hpp" instead of "ostream.hpp".  
  
```
