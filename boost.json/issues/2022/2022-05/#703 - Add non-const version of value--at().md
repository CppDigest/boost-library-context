# #703 - Add non-const version of value::at() [Closed]

> Username: Yosh31207  
> Created at: 2022-05-22 09:27:58 UTC  
> Updated at: 2022-05-23 19:00:18 UTC  
> Closed at: 2022-05-23 19:00:18 UTC  
> Url: https://github.com/boostorg/json/issues/703  

`value::at()` returns value with const qualifier even if the value is non-const data.  
  
### Version of Boost  
  
Boost 1.79  
  
### Example  
  
```c++  
value jv1 = {{"key", 1}};  
auto& a1  = jv1.as_object().at("key");  // returns value&  
auto& a2  = jv1.at("key");              // returns const value&  
value& a3 = jv1.at("key");              // compile error  
  
value jv2 = {1, 2, 3};  
auto& b1  = jv2.as_array().at(0);       // returns value&  
auto& b2  = jv2.at(0);                  // returns const value&  
value& b3 = jv2.at(0);                  // compile error  
```  
  
Note: `value.at_pointer()` has no such issue. It returns const value& if the data is const-qualified and returns value& otherwise.  
  
```c++  
value jv1 = {{"key", 1}};  
auto& a1  = jv1.as_object().at("key");  // returns value&  
auto& a2  = jv1.at_pointer("/key");     // returns value&  
  
const value jv2 = {{"key", 1}};  
auto& b1  = jv1.as_object.at("key");    // returns const value&  
auto& b2  = jv1.at_pointer("/key");     // returns const value&  
```
