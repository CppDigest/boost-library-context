# #56 - FEATURE: Store all error objects when verbose_diagnostic_info is in try_handle_all handlers [Closed]

> Username: Snarpix  
> Created at: 2023-08-07 17:02:46 UTC  
> Updated at: 2023-09-16 00:39:13 UTC  
> Closed at: 2023-09-16 00:39:13 UTC  
> Url: https://github.com/boostorg/leaf/issues/56  

Currently verbose_diagnostic_info is storing only strings received from applying operator<< to object, and only first time the type has encountered.  
From the docs:  
  
> The message printed by operator<< includes the message printed by error_info, followed by information about error objects that were communicated to LEAF (to be associated with the error) for which there was no storage available in any active (these error objects were discarded by LEAF, because no handler needed them).  
  
If I have some error context that is manipulating error object context using following .load overload:  
  
> f it is a function that takes a single argument of some type E &, that function is called with the object of type E currently associated with *this. If no such object exists, a default-initialized object is associated with *this and then passed to the function.  
  
In that case verbose_diagnostic_info will print the string from deepest on_error in the stack, because to string conversion is happening on MyError creation.   
`auto load = leaf::on_error([](MyError& e) { e.append("Ctx1")} );`  
  
Since verbose_diagnostic_info is used for debug purposes, It looks like it may allocate storage for each error object on heap and apply operator<< to object only when user calls operator<<  on verbose_diagnostic_info.  
  
If this is not suitable for some reason, may be we can add heap_diagnostic_info that will allow to store error object dynamically, and inspect them.  
  
This will be very useful, in application development, because you may never know what error object the underlying lib is using.  
  
Another option is to add something like try_handle_all_heap, that receives only one handler as an argument that allows runtime inspection of error objects.

---

## Comment 1

> Username: zajo  
> Created at: 2023-08-09 01:28:11 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1670523957  

I'm not sure I understand the use case of .load and on_error, and which is the deepest on_error. It would be very helpful if you provide a short working program that illustrates the problem. Thanks.

---

## Comment 2

> Username: Snarpix  
> Created at: 2023-08-09 06:37:26 UTC  
> Updated at: 2023-08-09 17:54:30 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1670755692  

Here is the code:  
https://godbolt.org/z/7fq5jfnEe  
<details>  
  <summary><b>CODE</b></summary>  
    
```  
#include <vector>  
#include <string>  
#include <iostream>  
  
#include <boost/leaf.hpp>  
  
namespace leaf = boost::leaf;  
  
struct MyError  
{  
    void append(std::string str)  
    {  
       vec.push_back(str);  
    }  
  
    friend std::ostream & operator<<(std::ostream &os, const MyError& e)  
    {  
        for(auto& s: e.vec)  
        {  
            os << s << std::endl;  
        }  
        return os;  
    }  
  
    std::vector<std::string> vec;  
};  
  
leaf::result<int> foo()  
{  
    auto ctx_ = leaf::on_error([](MyError& e) {e.append("ctx1");});  
    return leaf::new_error("123");  
}  
  
leaf::result<int> bar()  
{  
    auto ctx_ = leaf::on_error([](MyError& e) {e.append("ctx2");});  
    return foo();  
}  
  
leaf::result<int> foo_return()  
{  
    return leaf::new_error(MyError{{"ctx1"}});  
}  
  
leaf::result<int> bar_return()  
{  
    auto ctx_ = leaf::on_error([](MyError& e) {e.append("ctx2");});  
    return foo_return();  
}  
  
int main()  
{  
    std::cout << "Not working" << std::endl;  
    leaf::try_handle_all([]() -> leaf::result<int> {  
        return bar();  
    },  
    [](const leaf::verbose_diagnostic_info& e)  
    {  
        std::cout << e;  
        return 1;  
    }  
    );  
  
    std::cout << "Working, but I may not know that I should catch MyError" << std::endl;  
    leaf::try_handle_all([]() -> leaf::result<int> {  
        return bar();  
    },  
    [](const leaf::verbose_diagnostic_info& e, MyError*)  
    {  
        std::cout << e;  
        return 1;  
    }  
    );  
  
    std::cout << "Only shows deepest MyError" << std::endl;  
    leaf::try_handle_all([]() -> leaf::result<int> {  
        return bar_return();  
    },  
    [](const leaf::verbose_diagnostic_info& e)  
    {  
        std::cout << e;  
        return 1;  
    }  
    );  
}  
```  
  
</details>  
  
verbose_diagnostic_info will print deepest on_error in rare case when you `return leaf::new_error(MyError{"ctx1"});`  
If you use lambda overload of on_error then nothing gets printed.  
  
If I use verbose_diagnostic_info I expect that leaf will allocate, but I will have all diagnostic info possible, which is not what I get, unfortunately.

---

## Comment 3

> Username: zajo  
> Created at: 2023-08-14 02:35:40 UTC  
> Updated at: 2023-08-14 02:38:51 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1676586922  

Please confirm that I understand your proposal correctly: iff an error handler uses `verbose_diagnostic_info`, at the time errors that aren't otherwise handled are reported, allocate these error objects on the heap and proceed as usual, which now enables the conversion to string to happen in the error handler.  
  
I think this is doable, it'll require some type erasure magic a-la Boost Exception. And I see it as an improvement anyway, I reason it's more efficient to allocate memory when errors are reported rather than use `stringstream`.

---

## Comment 4

> Username: Snarpix  
> Created at: 2023-08-14 12:06:34 UTC  
> Updated at: 2023-08-14 12:08:25 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1677198907  

Yes, you've got it right.  
Maybe also add a way to inspect those type-erased objects in handler with verbose_diagnostic_info

---

## Comment 5

> Username: zajo  
> Created at: 2023-08-14 20:37:23 UTC  
> Updated at: 2023-08-14 21:30:17 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1678024283  

I'm not sure I'm on board with dynamic inspection. I don't want to implement it until I know what's the use case. Consider that by definition, the stuff that ends up in `verbose_diagnostic_info` is the stuff you don't care about.

---

## Comment 6

> Username: Snarpix  
> Created at: 2023-08-15 09:30:45 UTC  
> Updated at: 2023-08-15 09:31:24 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1678655889  

Yes, actually there is not a lot of things you can do to type-erased object, if you don't know it's type. And if you know it's type you can just add it to handler. So just postponing to-string-conversion until the error handler is actually called will do the trick.

---

## Comment 7

> Username: zajo  
> Created at: 2023-08-20 01:13:04 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1685145951  

See https://github.com/boostorg/leaf/pull/57, let me know if this works for you or if you have other feedback.

---

## Comment 8

> Username: Snarpix  
> Created at: 2023-08-21 17:13:32 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1686723413  

No, unfortunately behavior haven't changed from my original example.  
Here is a code with expected behavior:  
<details>  
  <summary>CODE</summary>  
  
```  
#include <vector>  
#include <string>  
#include <iostream>  
  
#include "./include/boost/leaf.hpp"  
  
namespace leaf = boost::leaf;  
  
struct MyError  
{  
    void append(std::string str)  
    {  
       vec.push_back(str);  
    }  
    std::vector<std::string> vec;  
};  
  
leaf::result<int> foo()  
{  
    // On error MyError is allocated inside verbose_diagnostic_info. Contains "ctx1"  
    auto ctx_ = leaf::on_error([](MyError& e) {e.append("ctx1");});  
    return leaf::new_error("123");  
}  
  
leaf::result<int> bar()  
{  
    // On error MyError was already created, so append is called on existing object. Contains "ctx1" , "ctx2"  
    auto ctx_ = leaf::on_error([](MyError& e) {e.append("ctx2");});  
    return foo();  
}  
  
int main()  
{  
    leaf::try_handle_all([]() -> leaf::result<int> {  
        return bar();  
    },  
    [](const leaf::verbose_diagnostic_info& e)  
    {  
        // Here verbose_diagnostic_info finally prints MyError  
        // Expected: "123", "ctx1", "cxt2"  
        // Actual results: "123"  
        std::cout << e;  
        return 1;  
    }  
    );  
}  
```  
</details>

---

## Comment 9

> Username: zajo  
> Created at: 2023-08-21 17:16:16 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1686726924  

I'll turn this into a unit test. Also, I came up with a much more elegant implementation, so I'll rewrite it anyway.

---

## Comment 10

> Username: zajo  
> Created at: 2023-08-29 19:19:31 UTC  
> Updated at: 2023-08-29 19:19:51 UTC  
> Url: https://github.com/boostorg/leaf/issues/56#issuecomment-1698000929  

It's fixed, but if you want to confirm,, try the develop branch.
