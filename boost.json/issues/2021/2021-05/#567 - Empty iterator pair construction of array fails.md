# #567 - Empty iterator pair construction of array fails [Closed]

> Username: vinniefalco  
> Created at: 2021-05-07 15:49:02 UTC  
> Updated at: 2021-05-22 12:46:56 UTC  
> Closed at: 2021-05-22 12:46:56 UTC  
> Url: https://github.com/boostorg/json/issues/567  

This snippet fails  
```  
std::vector<std::string> v;  
auto  jv    = json::value();  
auto& obj   = jv.emplace_object();  
obj["debs"] = json::array(v.begin(), v.end());  
auto msg = json::serialize(jv);  
```  
  
The problem is in the constructor which takes a pair of iterators. When the range is empty it should not allocate.
