# #647 - [feature request] Domain specific conversions [Closed]

> Username: klemens-morgenstern  
> Created at: 2021-11-05 08:02:27 UTC  
> Updated at: 2023-06-02 04:28:41 UTC  
> Closed at: 2023-06-02 04:28:41 UTC  
> Url: https://github.com/boostorg/json/issues/647  

I mentioned that to Vinnie already, but I'd profit from a context object for json, and after some thinking I'd just go for an additional parameter getting passed by const ref and propagated to all the tag_invokes. I don't know if the current overload resolution would work with this though.  
  
```cpp  
json::value_from(T, some_tag{});  
  
void  
tag_invoke(json::value_from_tag, json::value &target, const U &src, const some_tag &);  
  
json::value_to<T>(json::value, some_tag{});  
  
void  
tag_invoke(json::value_to_tag<U>, json::value &target, const some_tag &);  
```  
  
but that would allow me to attach a context, e.g. to turn all the std::chrono types into ISO8601 string including proper adl for that:  
  
```cpp  
namespace my_date_library  
{  
struct my_date_tag;  
  
void  
tag_invoke(json::value_from_tag, json::value &target, const U &src, const my_date_tag &);  
  
void  
tag_invoke(json::value_to_tag<U>, json::value &target, const my_date_tag &);  
  
}  
```  
  
and then I can combine those into my own tag:  
  
```cpp  
struct klemens_tag: my_date_library::my_date_tag {};  
```
