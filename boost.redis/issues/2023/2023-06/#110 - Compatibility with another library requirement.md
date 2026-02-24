# #110 - Compatibility with another library requirement... [Open]

> Username: cyboy22  
> Created at: 2023-06-07 18:20:40 UTC  
> Updated at: 2023-06-08 00:51:17 UTC  
> Url: https://github.com/boostorg/redis/issues/110  

Hi - I am building an application which uses Redis as the cache and persists data to postgreSQL. The postgreSQL library (ozo) requires a macro definition:  
  
BOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT  
  
which causes an exhaustive stream of errors that appear to be connected to your redis library. Could you suggest what might be the problem?- please find the output attached.  
  
[allout.txt.gz](https://github.com/boostorg/redis/files/11680722/allout.txt.gz)

---

## Comment 1

> Username: mzimbres  
> Created at: 2023-06-07 20:48:30 UTC  
> Url: https://github.com/boostorg/redis/issues/110#issuecomment-1581486150  

I can reproduce and will investigate soon. As a workaround, instead of using `boost::redis::connection` you can use an executor other that the default, for example  
  
```cpp  
using executor_type = net::io_context::executor_type;  
using connection = boost::redis::basic_connection<executor_type>;  
```  
  
This might have cascading effect, for example, you might have to redefine awaitable type as `net::awaitable<void, executor_type>` instead of `net::awaitable<void>`.

---

## Comment 2

> Username: mzimbres  
> Created at: 2023-06-07 20:53:29 UTC  
> Url: https://github.com/boostorg/redis/issues/110#issuecomment-1581493439  

By the way, boost has a MySql connector: https://www.boost.org/doc/libs/1_82_0/libs/mysql/doc/html/index.html which might be useful if you would like to avoid OZO.

---

## Comment 3

> Username: cyboy22  
> Created at: 2023-06-08 00:51:17 UTC  
> Url: https://github.com/boostorg/redis/issues/110#issuecomment-1581720141  

Really appreciate the time you have taken to look at the problem with try/investigate your suggestions.
