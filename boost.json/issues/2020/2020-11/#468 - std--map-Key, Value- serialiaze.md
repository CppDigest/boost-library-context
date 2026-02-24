# #468 - std::map<Key, Value> serialiaze [Closed]

> Username: heretic13  
> Created at: 2020-11-22 08:59:43 UTC  
> Updated at: 2023-08-25 17:09:04 UTC  
> Closed at: 2023-08-25 17:09:04 UTC  
> Url: https://github.com/boostorg/json/issues/468  

Hello.  
  
Maybe I didn't read the documentation carefully, but I didn't see an opportunity to serialize the following entity:  
  
std :: map <Key, Value>;  
where  
struct Key {...};  
struct Value {...};  
  
Your implementation works if Key is a "simple entity" such as a string or an integer.  
  
In general, you should provide programmers with the ability to write a function to convert a custom class / structure to and from a string. Your implementation should use this function to convert the Key element to a string and use the resulting string in a json object.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-11-22 19:18:33 UTC  
> Updated at: 2020-11-22 19:26:33 UTC  
> Url: https://github.com/boostorg/json/issues/468#issuecomment-731824717  

> you should provide programmers with the ability to write a function to convert a custom class / structure to and from a string  
  
That function already exists, it is a conversion operator. e.g. `T::operator std::string()` for your `T`.

---

## Comment 2

> Username: heretic13  
> Created at: 2020-11-23 08:09:42 UTC  
> Url: https://github.com/boostorg/json/issues/468#issuecomment-731998586  

I have comments:  
  
1. Is this documented in your library?  
2. What about the reverse transformation? std::string -> "Key" object?  
3. This does not work in general. Imagine that there are structures or classes written by another person / company (for example, this is the Windows SDK) and you have no right to modify header files. For this case, external functions (not class members) are needed for converting Type->std::string ang std::string-> Type.  
A couple of years ago I already had to write something similar for jsoncpp and then I used external functions.  
  
Of course, you can make your classes inherited from foreign ones (Windows SDK) and define operator std::string () const; for a new class, but I think it's ugly.

---

## Comment 3

> Username: grisumbras  
> Created at: 2023-08-25 17:09:04 UTC  
> Url: https://github.com/boostorg/json/issues/468#issuecomment-1693682692  

`value_to` converts maps  to objects when the key is `is_string_like`.  If not, it is converted to array.  
  
If your key type is not `is_string_like`, but you can "stringize" it and you want the map to be converted to object, then you can go for [contextual conversions](https://www.boost.org/doc/libs/release/libs/json/doc/html/json/conversion/contextual_conversions.html#json.conversion.contextual_conversions.contexts_and_composite_types). The linked section has an example of exactly such conversion.  
  
So, I'm closing this for now. If you feel like your case isn't addressed still, please reopen and explain why.
