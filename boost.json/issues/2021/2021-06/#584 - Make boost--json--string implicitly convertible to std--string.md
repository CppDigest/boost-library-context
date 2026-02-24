# #584 - Make boost::json::string implicitly convertible to std::string [Closed]

> Username: accelerated  
> Created at: 2021-06-20 14:31:16 UTC  
> Updated at: 2021-06-23 05:18:00 UTC  
> Closed at: 2021-06-20 17:41:56 UTC  
> Url: https://github.com/boostorg/json/issues/584  

### Version of Boost  
1.75  
  
This is more of a would-be-nice type of issue.  
  
Because the `boost::json::string` is not implicitly convertible to `std::string` you have to always do:  
```c++  
std::string s = {jv.as_string().data(), jv.as_string().size()}; //ok but unyieldy  
//or  
std::string s = value_to<std::string>(jv); //via tag dispatch. ok but not user friendly  
//ideally  
std::string s = jv.as_string();  
```  
  
Alternatively remove the `boost::json::string` type altogether, unless it has some special built-in features which I'm not aware of.  
  
Same goes for `boost::string_view`.   
  
I understand that under JSON_STANDALONE these types are aliased to STL types, but from a user perspective is highly undesirable. If I want to switch tomorrow from standalone to boost+json or vice-versa, my code will no longer compile. Ideally consider not using any other boost types unless you have < C++17, in which case you simply use std::string instead of string_view on your public interfaces.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-06-20 17:16:57 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-864585388  

There would be significant harmful consequences for users of the library if we adopt these changes.  
  
> `boost::json::string` is not implicitly convertible to `std::string`  
  
This is by design. Construction of `std::string` is expensive, as it requires allocating memory. The library requires that you opt-in to such constructions explicitly, to prevent unintentional expensive operations. Consider this function:  
  
```  
void f( std::string );  
```  
  
If `json::string` were implicitly convertible to `string_view` then someone might write:  
  
```  
json::string js;  
...  
f( js ); // js is implicitly converted to std::string  
```  
  
This code will compile and work, but there is now an expensive, unnecessary copy taking place which might not be what the user intended. The caller can opt-in to the conversion by using `json::string::subview()`, which returns a `string_view`:  
  
```  
f( js.subview() ); // converted to string_view, then std::string  
```  
  
A better solution is to change the function `f` to accept a `string_view` instead, and rely on `json::string::operator string_view` to perform an implicit conversion:  
  
```  
void f( string_view );  
...  
    json::string js;  
    f( js ); // js is implicitly and cheaply converted to string_view  
```  
  
> remove the `boost::json::string` type altogether  
  
`json::string` differs from `std::string` in significant ways:  
  
* It supports the `json::storage_ptr` allocation model  
* `sizeof(json::string)` is optimized for JSON library performance  
* `json::value_stack` uses `json::string` implementation details, for performance  
  
Using `std::string` instead is not a realistic option.  
  
> Same goes for `boost::string_view`  
  
We can't get rid of `boost::string_view`, as it is needed for C++11 and C++14 users. However, in the next version or so of Boost we will be rolling out changes which makes `boost::string_view` interoperate better with `std::string_view`.  
  
> If I want to switch tomorrow from standalone to boost+json or vice-versa,  
> my code will no longer compile.   
  
If you want to be able to switch without changing your code, then you can create your own type aliases and use them in your program:  
  
```  
namespace my_program {  
using string_view = boost::json::string_view;  
...  
```  
  
> Ideally consider not using any other boost types unless you have < C++17  
  
We could have done this, but then users of Boost.Asio and C++17 would be using two different error codes, as Asio always uses `boost::system::error_code`. The same goes for Boost.Beast. Switching the string view type based on the version of C++ being compiled with brings with it other problems, such as the possibility of link errors when libraries being linked were compiled with different versions of C++.   
  
Note that Boost.JSON is really two completely separate libraries in terms of symbols. The standalone JSON linker names are different from the Boost JSON linker names. See:  
  
https://github.com/boostorg/json/blob/351603c9b2dee7a80c8433841b50f7294131b25a/include/boost/json/detail/config.hpp#L141  
  
> in which case you simply use std::string instead of string_view on your public interfaces.  
  
This is not really a good choice due to performance considerations (construction of `std::string` is expensive) and for the other reasons listed above.

---

## Comment 2

> Username: accelerated  
> Created at: 2021-06-20 17:41:56 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-864588255  

Ok thanks for the good explanations! I'll close this issue for now. In my use case I have to use the serialized string value (or even the json string values) to build other custom types, which eventually will require the "expensive conversion". I will have to see if there's a way to avoid these w/o exposing too much the underlying json boost types.

---

## Comment 3

> Username: accelerated  
> Created at: 2021-06-21 15:25:27 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-865124479  

BTW...it might be worth aliasing `json::string` to `stl::pmr::string` which could the same memory resource that's passed-in when building a json::value. If the resources are compatible, then you also benefit from faster allocations, hence getting the best of both worlds i.e. API compatibility with STL vocabulary and performance.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-06-21 16:26:12 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-865175072  

> ...it might be worth aliasing json::string to stl::pmr::string   
  
But we can't do that, because `sizeof(std::pmr::string)` is too large and doesn't give the same performance guarantees on all platforms.  
  
> which could use the same memory resource that's passed-in when building a json::value.   
  
It already works that way. `value`, `object`, `array`, and `string` all use `storage_ptr`, which is constructible from `memory_resource`.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2021-06-21 23:50:35 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-865420702  

> any idea how to get partial specialization for tag dispatch to work  
  
Please open a new issue

---

## Comment 6

> Username: accelerated  
> Created at: 2021-06-22 00:51:21 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-865440644  

I figured it out. It needs to be within boost::json namespace or the global namespace. If it's within the project's namespace ADL does not work. Should probably be more explicitly stated in the examples online.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2021-06-22 00:53:13 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-865441180  

Yeah, although it isn't intended that users are supposed to customize std types. If you open an issue, we can make sure Boost.JSON does it for you.

---

## Comment 8

> Username: accelerated  
> Created at: 2021-06-22 11:54:03 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-865918171  

Ok, thanks. If you think it's worth adding explicit support for `std::optional<T>` (for C++17 and beyond) I can open an issue. Since nullable values can be converted to optional (and vice-versa), there could be a use-case where this conversion could be used.

---

## Comment 9

> Username: vinniefalco  
> Created at: 2021-06-22 13:14:58 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-865973610  

Well I think it already works in the develop branch

---

## Comment 10

> Username: grisumbras  
> Created at: 2021-06-22 15:51:31 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-866108538  

I'm not so sure about `optional` working in develop.

---

## Comment 11

> Username: vinniefalco  
> Created at: 2021-06-22 16:15:08 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-866127871  

> I'm not so sure about optional working in develop.  
  
Did we have plans to make it work? I can't remember

---

## Comment 12

> Username: grisumbras  
> Created at: 2021-06-23 04:21:11 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-866510111  

We didn't exactly have plans, but we discussed that it should work.

---

## Comment 13

> Username: vinniefalco  
> Created at: 2021-06-23 05:17:59 UTC  
> Url: https://github.com/boostorg/json/issues/584#issuecomment-866529733  

Please open a new issue
