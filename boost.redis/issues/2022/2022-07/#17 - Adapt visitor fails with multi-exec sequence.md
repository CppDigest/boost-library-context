# #17 - Adapt visitor fails with multi/exec sequence [Closed]

> Username: Eddie-cz  
> Created at: 2022-07-25 14:18:28 UTC  
> Updated at: 2022-07-26 08:02:11 UTC  
> Closed at: 2022-07-26 08:02:11 UTC  
> Url: https://github.com/boostorg/redis/issues/17  

Version: latest master  
  
With request sequence  
req.push("MULTI");	  
req.push("SET", "set-key1", "1");  
req.push("SET", "set-key2", "2");  
req.push("EXEC");		  
  
and resp as  
  
std::tuple<				  
aedis::ignore, // multi  
aedis::ignore, // set1  
aedis::ignore, // set2  
std::tuple\<std::string,std::string\> // exec  
\> resp;  
  
I get assert in adapt visitor in adapt.hpp, BOOST_ASSERT(i < adapters_.size());  
i is 4, adapters size is 4. When I add some other command after exec, it works.

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-07-25 16:52:03 UTC  
> Url: https://github.com/boostorg/redis/issues/17#issuecomment-1194345504  

I can reproduce this and will give feedback soon. Thanks for reporting.

---

## Comment 2

> Username: mzimbres  
> Created at: 2022-07-25 20:21:42 UTC  
> Url: https://github.com/boostorg/redis/issues/17#issuecomment-1194580417  

Actually, I can't reproduce the problem, there was an error in my initial try. For example, the code below runs without errors  
  
```  
#include <tuple>  
#include <string>  
#include <boost/asio.hpp>  
#include <aedis.hpp>  
#include <aedis/src.hpp>  
  
namespace net = boost::asio;  
  
using aedis::adapt;  
using aedis::resp3::request;  
using connection = aedis::connection<>;  
  
auto exec_handler = [](auto ec, auto)  
   { std::cout << ec.message() << std::endl; };  
  
auto run_handler = [](auto ec)  
   { std::cout << ec.message() << std::endl; };  
  
int main()  
{  
   request req1;  
   req1.push("HELLO", 3);  
  
   request req2;  
   req2.push("MULTI");  
   req2.push("SET", "set-key1", "1");  
   req2.push("SET", "set-key2", "2");  
   req2.push("EXEC");  
  
   request req3;  
   req3.push("QUIT");  
  
   std::tuple<  
      aedis::ignore, // multi  
      aedis::ignore, // set1  
      aedis::ignore, // set2  
      std::tuple<std::string, std::string> // exec  
   > resp2;  
  
   net::io_context ioc;  
  
   connection db{ioc};  
   db.async_exec(req1, adapt(), exec_handler);  
   db.async_exec(req2, adapt(resp2), exec_handler);  
   db.async_exec(req3, adapt(), exec_handler);  
   db.async_run("127.0.0.1", "6379", run_handler);  
   ioc.run();  
  
   std::cout << std::get<0>(std::get<3>(resp2)) << std::endl;  
   std::cout << std::get<1>(std::get<3>(resp2)) << std::endl;  
}  
  
```  
Make sure the number of commands in the request (`request::command()`) is equal to the size of the tuple.

---

## Comment 3

> Username: Eddie-cz  
> Created at: 2022-07-26 08:02:11 UTC  
> Url: https://github.com/boostorg/redis/issues/17#issuecomment-1195143017  

It was my bad reuse of request, I thought after async exec finishes it gets cleared automatically, but it's not that case and thats why i got visitor assert, there were more commands in request than in resp.
