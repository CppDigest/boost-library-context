# #756 - Boost json as_uint64() raise exception. [Open]

> Username: TyeolRik  
> Created at: 2023-03-23 05:31:38 UTC  
> Updated at: 2023-07-18 12:23:09 UTC  
> Url: https://github.com/boostorg/boost/issues/756  

Hello, I think I found the bug in `boost::json`.  
  
```cpp  
boost::json::object test;  
test["quota"] = 12345;  
std::string key("quota");  
uint64_t quota;  
try {  
    quota = test.at(key).as_uint64();  
} catch(const std::out_of_range& e) {  
    std::cout << "Cannot found the key: <" << key << "> in Body!" << std::endl;  
} catch(const std::invalid_argument& e) {  
    std::cout << "We found key: <" << key << "> in Body but it is " << e.what() << std::endl;  
}  
```  
  
When I run above code, `std::invalid_argument` exception raised.  
  
```bash  
We found key: <quota> in Body but it is not a uint64  
```  
  
When I change `as_uint64()` to `as_int64()`, there is no raised exception. Is this bug? or my fault? Please guide me to solve this problem :( <br>  
\* Of course, I know the difference between uint64 and int64. But, I need `as_uint64()` for safe. (value should be larger than 0)<br>  
\** When I change the code 12345 to 12345UL, It works well without exception....  
  
My enviornment is  
  
- OS: CentOS 8 Stream  
- G++: g++ (GCC) 11.2.1 20220127 (Red Hat 11.2.1-9)  
- Boost 1.80.0

---

## Comment 1

> Username: yaojiadong  
> Created at: 2023-07-18 12:23:09 UTC  
> Url: https://github.com/boostorg/boost/issues/756#issuecomment-1640111816  

`as_uint64()` does not convert a signed int to an unsigned int.  
It does the same as `get_uint64()` with exception.   
The value stored in the object is a signed value, that is why it throws when you try to get it as an unsigned int.  
  
consider: `quota = static_cast<uint64_t>(test.at(key).as_int64());`
