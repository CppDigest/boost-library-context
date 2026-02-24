# #238 - Boost 1.88 breaks conversions to some integer types [Closed]

> Username: anarthal  
> Created at: 2025-04-04 15:52:21 UTC  
> Updated at: 2025-04-07 20:48:49 UTC  
> Closed at: 2025-04-07 20:48:49 UTC  
> Url: https://github.com/boostorg/redis/issues/238  

Testing with Boost 1.88.0 RC1, Ubuntu 24.04, both gcc-14 and clang-18. The following code used to compile under Boost 1.87:  
  
```cpp  
#include <boost/asio/detached.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/redis.hpp>  
  
namespace redis = boost::redis;  
namespace asio = boost::asio;  
  
int main()  
{  
    asio::io_context ctx;  
    redis::connection conn(ctx);  
    redis::request req;  
    redis::response<long> res;  
    conn.async_exec(req, res, asio::detached);  
}  
```  
  
This is no longer the case, since Boost.Redis seems to think that a `long` is not an integral type.  
  
The offending code is:  
  
https://github.com/boostorg/redis/blob/ac4e6e29b372783b9c11b015ef187f07f2112e46/include/boost/redis/adapter/detail/adapters.hpp#L40-L44  
  
Integer types must be 8, at least, and only 3 are listed there. What's the rationale behind defining a custom type trait for integers, rather than `std::is_integral`? (add a `!std::is_same_v<T, bool>` if you need to exclude booleans).

---

## Comment 1

> Username: mclow  
> Created at: 2025-04-04 18:17:07 UTC  
> Url: https://github.com/boostorg/redis/issues/238#issuecomment-2779456869  

This code was added on 19-February in commit  412f553, so it's definitely new in 1.88 - but was present in the 1.88.0 beta.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-04-04 19:49:35 UTC  
> Url: https://github.com/boostorg/redis/issues/238#issuecomment-2779611507  

On Fri, 4 Apr 2025 at 20:17, Marshall Clow ***@***.***> wrote:  
  
> This code was added on 19-February in commit 412f553  
> <https://github.com/boostorg/redis/commit/412f5535ea52e7b4f165291a9becc3f44ee9a22b>,  
> so it's definitely new in 1.88 - but was present in the 1.88.0 beta.  
>  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/redis/issues/238#issuecomment-2779456869>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AIK2BI6WMECVYOSLWG3B4UT2X3EDVAVCNFSM6AAAAAB2PCEG26VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDONZZGQ2TMOBWHE>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
> [image: mclow]*mclow* left a comment (boostorg/redis#238)  
> <https://github.com/boostorg/redis/issues/238#issuecomment-2779456869>  
>  
> This code was added on 19-February in commit 412f553  
> <https://github.com/boostorg/redis/commit/412f5535ea52e7b4f165291a9becc3f44ee9a22b>,  
> so it's definitely new in 1.88 - but was present in the 1.88.0 beta.  
>  
  
I know. Unfortunately, I only remembered to build this project now - should  
have done it for the beta, too. I only built the libraries back then, and  
since Redis is header-only, it looks like the problem went unnoticed. My  
apologies.  
  
> —  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/redis/issues/238#issuecomment-2779456869>,  
> or unsubscribe  
> <https://github.com/notifications/unsubscribe-auth/AIK2BI6WMECVYOSLWG3B4UT2X3EDVAVCNFSM6AAAAAB2PCEG26VHI2DSMVQWIX3LMV43OSLTON2WKQ3PNVWWK3TUHMZDONZZGQ2TMOBWHE>  
> .  
> You are receiving this because you authored the thread.Message ID:  
> ***@***.***>  
>

---

## Comment 3

> Username: anarthal  
> Created at: 2025-04-04 19:52:25 UTC  
> Url: https://github.com/boostorg/redis/issues/238#issuecomment-2779615826  

I know. Unfortunately, I only remembered to build my servertech project now (I only did the regular b2 build for the beta). My bad. I'm just a user of this library, so I thought better to report it late than never.
