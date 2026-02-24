# #957 - Segmentation fault when a JSON string with whitespaces is passed to boost::json::parse() function [Closed]

> Username: Elnee  
> Created at: 2023-11-27 21:14:07 UTC  
> Updated at: 2023-11-28 12:18:21 UTC  
> Closed at: 2023-11-28 12:18:21 UTC  
> Url: https://github.com/boostorg/json/issues/957  

### Description  
As described in the title.  
  
**Version of Boost:** 1.80.0  
**Operating system:** Ubuntu 22.04.3 LTS (64-bit)  
**Compiler:** gcc (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0  
**CMake:** 3.22.1  
  
### Link to the ready-to-build project with reproduction example (just run `./run.sh`):  
https://github.com/Elnee/minimal-reproduction-for-boost-issue  
  
### Minimal reproduction example:  
  
```C++  
#include <bits/stdc++.h>  
#include <boost/json.hpp>  
  
int main() {  
  
  const std::string json = R"({"example": "foo"})"; // NOTE: The whitespace here between colon and quotation mark.  
  const auto &object = boost::json::parse(json).as_object();  
  
  try {  
    // Crash appears on the next line while executing the `at()` function.  
    std::cout << object.at("example").as_string().c_str() << std::endl;  
  } catch (const std::exception &ex) {  
    std::cout << "Exception: " << ex.what() << std::endl;  
  }  
  
  return 0;  
}  
```  
  
### Output:  
```  
Segmentation fault (core dumped)  
```

---

## Comment 1

> Username: grisumbras  
> Created at: 2023-11-28 07:58:11 UTC  
> Url: https://github.com/boostorg/json/issues/957#issuecomment-1829286310  

`json::parse` returns a `json::value`, which you don't store anywhere. You instead get a reference to its subobject of type `json::object`. At the end of that statement the `value` goes out of scope and destroys its internal `object`. After that you use a dangling reference. In order to fix this you should store either the `value` or the `object`:  
  
```c++  
const auto value = boost::json::parse(json);  
const auto& object = value.as_object();  
// or  
const auto object = boost::json::parse(json).as_object();  
```

---

## Comment 2

> Username: Elnee  
> Created at: 2023-11-28 11:04:12 UTC  
> Url: https://github.com/boostorg/json/issues/957#issuecomment-1829594516  

@grisumbras Thank you, it works. But I don't understand why using a `const-ref` doesn't expand the lifetime of that object.  
As I see `boost::json::value` has a union returned by `as_object()`. Is it because its lifetime is bound to the instance of `boost::json::value`?

---

## Comment 3

> Username: Elnee  
> Created at: 2023-11-28 11:21:58 UTC  
> Url: https://github.com/boostorg/json/issues/957#issuecomment-1829625211  

And also, isn't it a bit misleading that I should think about `as_object()` method in such a way? I thought about it as getting the same object but in a different representation (due to the `as` prefix). It turned out to return some internals to me.  
  
Doesn't it force me to know some internal library implementation details aside from the interface as a user?

---

## Comment 4

> Username: sdkrystian  
> Created at: 2023-11-28 12:14:35 UTC  
> Updated at: 2023-11-28 12:15:01 UTC  
> Url: https://github.com/boostorg/json/issues/957#issuecomment-1829723630  

@Elnee Per [[class.temporary] p4](https://eel.is/c++draft/class.temporary#4.sentence-3), temporary objects are destroyed after evaluating the full-expression in which they appear (which in this case is the initialization of `object`). When a temporary object is bound to a reference, its lifetime will be extended to that of the reference _if, and only if_, one of the conditions specified by  [[class.temporary] p6](https://eel.is/c++draft/class.temporary#6) are met. Binding a reference to the result of calling a non-static member function of a temporary object does not satisfy any of these conditions, so the lifetime is not extended.

---

## Comment 5

> Username: Elnee  
> Created at: 2023-11-28 12:18:21 UTC  
> Url: https://github.com/boostorg/json/issues/957#issuecomment-1829729255  

@sdkrystian Many thanks for the detailed explanation.
