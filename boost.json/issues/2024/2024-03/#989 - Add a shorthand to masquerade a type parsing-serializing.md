# #989 - Add a shorthand to masquerade a type parsing/serializing [Open]

> Username: kiwixz  
> Created at: 2024-03-14 18:06:19 UTC  
> Updated at: 2024-03-14 18:11:08 UTC  
> Url: https://github.com/boostorg/json/issues/989  

Hi, there are two cases where I couldn't avoid implementing one-line tag_invokes.  I'd love boost::json to do it for me!  
  
The first one is an enum that only has ints.  Its an enum class because why not but I don't care about specific values, its just here to help referencing interesting ones.  
```cpp  
enum class Version { _202201 = 202201, _202301 = 202301, _202302 = 202302};  
  
Version tag_invoke(boost::json::value_to_tag<Version>, const boost::json::value& json) {  
    return static_cast<Version>(json.as_int64());  
}  
void tag_invoke(boost::json::value_from_tag, boost::json::value& json, Version a) {  
    json = static_cast<int>(a);  
}  
```  
  
Second case is probably more interesting, following a lot of back and forth the best way I found to handle a type with invariants is to make a described struct.  
```cpp  
struct MyClass {  
    struct Params {  
        ...  
    };  
  
    MyClass(Params params);  
    ...  
};  
```  
And `MyClass c = value_to<MyClass::Params>()` just works (with implicit conversion), but if I want to include it inside a described struct boost::json doesn't know that to decode a MyClass it needs a MyClass::Params.  
  
I also had the same problem when I tried to parse/serialize a type implicitly convertible to and from a std::string.  Even overriding `is_string_like<>` was not enough.  
  
So I made a little helper and it works for me, but I think boost::json should have something similar (or better!) built-in:  
  
```cpp  
template <typename>  
struct JsonAs;  
  
  
namespace boost::json {  
  
template <typename T>  
requires requires { typename JsonAs<T>::type; }  
T tag_invoke(boost::json::value_to_tag<T>, const boost::json::value& json) {  
    return static_cast<T>(boost::json::value_to<typename JsonAs<T>::type>(json));  
}  
  
template <typename T>  
requires requires { typename JsonAs<std::remove_cvref_t<T>>::type; }  
void tag_invoke(boost::json::value_from_tag, boost::json::value& json, T&& a) {  
    json = boost::json::value_from(static_cast<JsonAs<std::remove_cvref_t<T>>::type>(std::forward<T>(a)));  
}  
  
}  
```  
  
```cpp  
template <>  
struct JsonAs<Version> : std::type_identity<int> {};  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2024-03-14 18:11:07 UTC  
> Url: https://github.com/boostorg/json/issues/989#issuecomment-1998049942  

Yeah, we discussed something like this in Slack.
