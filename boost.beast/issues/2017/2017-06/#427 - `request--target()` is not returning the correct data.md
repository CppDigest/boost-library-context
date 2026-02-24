# #427 - `request::target()` is not returning the correct data [Closed]

> Username: cmannett85  
> Created at: 2017-06-06 09:07:41 UTC  
> Updated at: 2017-06-08 08:35:10 UTC  
> Closed at: 2017-06-08 08:35:10 UTC  
> Url: https://github.com/boostorg/beast/issues/427  

### Version of Beast  
commit ac5bc4f62c318812104ad2cef13b2d3a19ada8bb  
Author: Vinnie Falco <vinnie.falco@gmail.com>  
Date:   Sun Jun 4 21:15:04 2017 -0700  
  
    Set version to 49  
  
### Steps necessary to reproduce the problem  
I was writing some unit tests for an app I'm working on and I noticed that one was failing oddly.  After further investigation it appears that the `request::target()` method isn't storing the target `string_view`.  The trivial test below demonstrates it:  
  
```  
...  
#include <beast/core.hpp>  
#include <beast/http.hpp>  
  
#include <boost/test/unit_test.hpp>  
...  
using namespace std::string_literals;  
  
BOOST_AUTO_TEST_SUITE(scratch)  
BOOST_AUTO_TEST_CASE(target_test)  
{  
    const auto port = std::uint16_t{1234};  
    const auto tgt = "/hello"s;  
    const auto host_str = "my_computer:" + std::to_string(port);  
  
    auto fields = beast::http::fields{};  
    fields.insert("Content-Type", "application/json");  
    fields.insert("Host", host_str);  
  
    auto req = beast::http::request<beast::http::empty_body>{};  
    req.method(beast::http::verb::get);  
    req.target(tgt);  
    req.version = 11;  
    req.fields = fields;  
  
    beast::http::prepare(req);  
    BOOST_TEST_MESSAGE(req);  
  
    BOOST_CHECK(req.is_request);  
    BOOST_CHECK_EQUAL(req.method(), beast::http::verb::get);  
    BOOST_CHECK_EQUAL(req.target(), tgt);  
    BOOST_CHECK_EQUAL(req.version, 11);  
  
    auto fIt = fields.find("Content-Type");  
    BOOST_REQUIRE(fIt != fields.end());  
    BOOST_CHECK_EQUAL(fIt->value(), "application/json"s);  
  
    fIt = fields.find("Host");  
    BOOST_REQUIRE(fIt != fields.end());  
    BOOST_CHECK_EQUAL(fIt->value(), host_str);  
}  
BOOST_AUTO_TEST_SUITE_END()  
```  
Outputs:  
```  
Entering test case "scratch"  
GET  HTTP/1.1  
Content-Type: application/json  
Host: my_computer:1234  
  
  
error: in "scratch/target_test": check req.target() == tgt has failed [ != /hello]  
```  
Putting a breakpoint at `BOOST_TEST_MESSAGE(..)` via gdb reveals:  
```  
(gdb) p req.method()  
$2 = beast::http::verb::get  
(gdb) p req.target()  
$3 = {static npos = <optimized out>, ptr_ = 0x0, len_ = 0}  
(gdb)   
```  
  
Or am I just doing something stupid?  
  
### All relevant compiler information  
g++ (GCC) 6.3.1 20161221 (Red Hat 6.3.1-1)

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-06 10:20:25 UTC  
> Updated at: 2017-06-06 10:49:19 UTC  
> Url: https://github.com/boostorg/beast/issues/427#issuecomment-306444039  

_"Never underestimate the ability of users to abuse your interface in ways you hadn't thought of."_  
  
Thanks for the detailed report. I'm quite impressed actually, you have managed to break my code within mere hours of the merge! Here's the problem:  
  
>req.fields = fields;  
  
Beast now stores the strings for method, target, and reason in the **Fields** in hidden variables. By assigning the whole container, the previous value for target is overwritten The reason I made this change is because before, they were being stored as `std::string` which bypasses the allocator. You can read the motivation behind this change in this new doc section:  
http://vinniefalco.github.io/beast/beast/design/http_message.html  
  
You could have just written  
```  
req.fields.insert("Content-Type", "application/json");  
```  
  
If you avoid assigning the entire fields container, you'll find that it works.  
  
Just a heads up but the interface will be changing (I am trying to polish this up as much as I can ahead of the July 1 Boost review). `header` will derive from **Fields**, instead of having a member named `fields`. Your could would be written thusly:  
```  
    auto req = beast::http::request<beast::http::empty_body>{};  
    req.version = 11;  
    req.method(beast::http::verb::get);  
    req.target(tgt);  
    req.insert(beast::http::field::content_type, "application/json");  
    req.insert(beast::http::field::host", host_str);  
    req.prepare();  
```  
  
Alternatively if I can get it to work, you might write  
```  
    ...  
    req[beast::http::field::content_type] = "application/json";  
    req[beast::http::field::host] = host_str;  
```  
  
There's quite a few changes as you can see.

---

## Comment 2

> Username: cmannett85  
> Created at: 2017-06-06 10:29:55 UTC  
> Url: https://github.com/boostorg/beast/issues/427#issuecomment-306446147  

Thank you!  
  
The required change also demonstrated that my field tests weren't testing what I thought they were...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-06-06 11:05:03 UTC  
> Url: https://github.com/boostorg/beast/issues/427#issuecomment-306453433  

What do you think about these changes? Feedback welcomed! Now is the time to complain, before July 1st!

---

## Comment 4

> Username: cmannett85  
> Created at: 2017-06-06 14:30:08 UTC  
> Url: https://github.com/boostorg/beast/issues/427#issuecomment-306504087  

I'm not familiar with Beast enough to comment with any authority, but in this particular example, I'm perfectly happy with `header` deriving from `fields`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-06-08 05:38:28 UTC  
> Url: https://github.com/boostorg/beast/issues/427#issuecomment-307003810  

Is this issue resolved?
