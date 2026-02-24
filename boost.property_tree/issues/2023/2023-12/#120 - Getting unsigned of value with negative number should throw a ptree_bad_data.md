# #120 - Getting unsigned of value with negative number should throw a ptree_bad_data [Open]

> Username: Pokawa  
> Created at: 2023-12-05 13:34:31 UTC  
> Updated at: 2023-12-05 17:15:41 UTC  
> Url: https://github.com/boostorg/property_tree/issues/120  

As of now ptree will not complain about a negative value and will happily cast it to unsigned type. If that's expected then I think it's highly unintuitive.  
  
```cpp  
    boost::property_tree::ptree pt;  
    pt.put("foo", "-1");  
    std::cout << pt.get<unsigned>("foo") << std::endl; // 4294967295  
```

---

## Comment 1

> Username: ashtum  
> Created at: 2023-12-05 17:15:40 UTC  
> Url: https://github.com/boostorg/property_tree/issues/120#issuecomment-1841261762  

The `ptree` stores the string representation of values and uses [stream_translator](https://github.com/boostorg/property_tree/blob/8080ecd14f2d952a4bb7ae869fc0f54f54f5a31f/include/boost/property_tree/stream_translator.hpp#L194) for retrieving and setting values. In your example, the process resembles the following:  
  
```C++  
std::stringstream ss{"-1"};  
unsigned i;  
ss >> i;  
std::cout << i << std::endl;  
```  
  
You can provide your custom translator by utilizing this overload of `ptree::get`: https://github.com/boostorg/property_tree/blob/8080ecd14f2d952a4bb7ae869fc0f54f54f5a31f/include/boost/property_tree/ptree.hpp#L381
