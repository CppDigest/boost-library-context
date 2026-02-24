# #2120 - beast::basic_stream does not have an assignement operator [Closed]

> Username: AlexandreBossard  
> Created at: 2020-11-10 16:12:58 UTC  
> Updated at: 2021-05-29 17:10:55 UTC  
> Closed at: 2021-05-29 17:10:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2120  

### Version of Beast  
boost 1.73 - not really relevant  
  
### Steps necessary to reproduce the problem  
The following code does not compile on msvc 19  
```  
#include <future>  
#include <boost/beast/core/tcp_stream.hpp>  
struct S  
{  
    boost::beast::tcp_stream stream;  
    S() : stream(boost::asio::system_executor())   
    {}  
  S(S&& s) : stream(std::move(s.stream)) {}  
};  
int main()  
{  
    std::promise<S> p;  
    p.set_value(S());  
    auto f = p.get_future();  
}  
```  
https://godbolt.org/z/fxYehz  
  
This arise when I wrote a composed operation and use it with `net::use_future`, the `self.complete(ec, std::move(obj))` failed to comiple with MSVC.  
  
MSVC`s `std::future` implementation requires a default constructible object and an assignment operator (copy or move). `beast::basic_stream` fails to provide that, but `asio::basic_stream_socket` seems ok.  
  
It is surprising to have a movable constructor but not a move assignment constructor.

---

## Comment 1

> Username: kervinpierre  
> Created at: 2020-11-11 15:13:33 UTC  
> Url: https://github.com/boostorg/beast/issues/2120#issuecomment-725479244  

https://developercommunity.visualstudio.com/content/problem/123622/stdasyncs-stdfuture-doesnt-support-types-that-are.html

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-12-25 12:12:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2120#issuecomment-751240899  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 17:10:50 UTC  
> Url: https://github.com/boostorg/beast/issues/2120#issuecomment-850866711  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
