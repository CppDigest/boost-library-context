# #233 - Missing implementation for std::vector<std::optional<std::string>> [Closed]

> Username: gelldur  
> Created at: 2025-02-05 08:12:44 UTC  
> Updated at: 2025-02-20 11:19:06 UTC  
> Closed at: 2025-02-20 11:19:06 UTC  
> Url: https://github.com/boostorg/redis/issues/233  

When want to receive multiple keys and keys may be not present.  
If I use `vector<string>` then it will fail if at least 1 key is missing.  
  
```cpp  
	std::map<std::string, std::string> result;  
	boost::redis::request req;  
  
	req.push_range("MGET", keys.begin(), keys.end());  
	boost::redis::response<std::vector<std::optional<std::string>>> res;  
  
	exec(req, res);  
```  
  
```  
error: no matching function for call to 'boost_redis_from_bulk'  
include/boost/redis/adapter/detail/adapters.hpp:387:7: note: in instantiation of function template specialization 'boost::redis::adapter::detail::vector_impl<std::vector<std::optional<std::basic_string<char>>>>::operator()<std::basic_string_view<char>>' requested here  
```  
  
When using: `boost::redis::response<std::optional<std::vector<std::string>>> res;`  
It will mismatch number of expected responses.

---

## Comment 1

> Username: mzimbres  
> Created at: 2025-02-08 19:45:43 UTC  
> Url: https://github.com/boostorg/redis/issues/233#issuecomment-2645917709  

Thanks for reporting, I will have a look at it soon.
