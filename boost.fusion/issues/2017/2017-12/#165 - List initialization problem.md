# #165 - List initialization problem [Open]

> Username: Kojoley  
> Created at: 2017-12-23 15:11:15 UTC  
> Updated at: 2017-12-23 15:11:15 UTC  
> Url: https://github.com/boostorg/fusion/issues/165  

```cpp  
    boost::fusion::vector<int>{ 123 }; // ok  
    boost::fusion::vector<int>{ { 123 } }; // fail  
    boost::optional<int>{ 123 }; // ok  
    boost::optional<int>{ { 123 } }; // ok  
    boost::fusion::vector<boost::optional<int>>{ 123 }; // ok  
    boost::fusion::vector<boost::optional<int>>{ { 123 } }; // fail  
    boost::fusion::vector<boost::optional<int>>{ { { 123 } } }; // fail  
  
    boost::fusion::vector<std::string>{ "hello" }; // ok  
    boost::fusion::vector<std::string>{ { "hello" } }; // fail  
    boost::optional<std::string>{ "hello" }; // ok  
    boost::optional<std::string>{ { "hello" } }; // ok  
    boost::fusion::vector<boost::optional<std::string>>{ "hello" }; // fail  
    boost::fusion::vector<boost::optional<std::string>>{ { "hello" } }; // fail  
    boost::fusion::vector<boost::optional<std::string>>{ { { "hello" } } }; // fail  
```
