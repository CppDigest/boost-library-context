# #502 side-cast(?) from [object,array,string] to value [Closed]

> Username: stream009  
> Created at: 2021-02-17 09:37:51 UTC  
> Updated at: 2021-02-22 15:30:36 UTC  
> Closed at: 2021-02-22 15:04:35 UTC  
> Url: https://github.com/boostorg/json/pull/502  

We can cast ```json::value``` to array, object, string with ```get_array()```, ```get_object()```, ```get_string()``` member functions, but we can't cast them back to ```json::value```.  
  
Suppose we write a function that take ```json::value``` as argument and return reference to sub-value:  
```c++  
json::value&       query(json::value&); // (1)  
json::value const& query(json::value const&); // (2)  
```  
Suppose everything works fine as long as we pass ```json::value``` as interface stated.   
  
Things go sideway when we call ```query``` with ```json::object``` as argument. The object is converted to temporary ```json::value``` and bound to ```json::value const&``` on (2) then function will return reference to (most-likely destroyed) sub-value of temporary.  
  
Situation is more serious if functions are modifier:  
```c++  
json::value& modify(json::value&); // (3)  
json::value&& modify(json::value&&); // (4)  
  
json::array a1 { 100, 200 };  
auto&& a2 = modify(a1);  
```  
Client code expect ```a1``` to be changed after function call, but that is not what is going to happen. ```a1``` is converted to temporary ```json::value```, then bound to argument of (4) and ```modify``` change this temporary and return reference to it.   
  
To avoid this fiasco, we need to supply overload for ```json::[array,object,string]``` to each functions.  
```c++  
json::value&       query(json::array&);  
json::value const& query(json::array const&);  
json::value&       query(json::object&);  
json::value const& query(json::object const&);  
```  
It seems fine but it soon become ridiculous when we have multiple ```json::value``` argument.  
  
All of these problems go away if reference to ```json::[array,object,string]``` can be implicitly casted to reference to ```json::value```.   
  
So I fork the repository. Everything is working fine except conversion to rvalue-ref ```json::value&&```. It has to be explicit otherwise it cause ambiguity issue with ```json::value const&```.  
  
Are there any issue that make you to decide not support this in the first place?  
Supporting this might seems odd from normal C++ OO perspective because ```json::[array,object,string,value]``` are distinct types, but it is intuitive considering they are modeled after JavaScript value. I thought you were worrying about use-case like:  
```c++  
json::array a1 { 100, 200 };  
static_cast<json::value&>(a1) = "yahoo";  
a1[1]; // KABOOM  
```  
but this problem already exist:  
```c++  
json::value v1 { 100, 200 };  
auto& a1 = v1.get_array();  
v1 = "yahoo";  
a1[1]; // KABOOM  
```

---

## Comment 1

> Username: stream009  
> Created_at: 2021-02-17 09:43:15 UTC  
> Url: https://github.com/boostorg/json/pull/502#issuecomment-780432485  

BTW I wrote [algorithms](https://gitlab.com/libstream9/json/-/blob/master/include/stream9/json/algorithm.hpp) that support JSON pointer upon boost JSON.

---

## Comment 2

> Username: cppalliance-bot  
> Created_at: 2021-02-17 10:48:43 UTC  
> Url: https://github.com/boostorg/json/pull/502#issuecomment-780471184  

![pullrequest](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/502/pullrequest-condensed-2a253d8.png)  
Benchmark test results. More info at [https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/502/pullrequest.html](https://benchmark.cppalliance.org/jsonbenchmarks-pullrequests/502/pullrequest.html)

---

## Comment 3

> Username: grisumbras  
> Created_at: 2021-02-22 14:08:08 UTC  
> Url: https://github.com/boostorg/json/pull/502#issuecomment-783400663  

First of all `json::value& query(json::value&)` will never be called "by accident", because a temporary `value` won't bind to lvalue reference. This leaves us with  
  
```cpp  
json::value const& query(json::value const&);  
json::value&& modify(json::value&&);  
```  
These two can indeed be called by accident with `array`, `string` and `object` instances and return dangling references. This is a known error related to returning references, non-owning pointers, "views" etc. to objects or their parts. E.g. compare with  
  
```cpp  
std::string_view find_substring(std::string text, std::string_view sub);  
find_substring("this is some text", "some");  
```  
  
While this is unfortunate, the problem usually has no satisfying solution (usually, the only solution is to not permit implicit conversions).  
  
Finally while your solution seems to work, it appears to rely on undefined behaviour. Just because a `value` that stores an `array` has the same layout as an `array`, doesn't mean you can cast an `array&` to `value&`.

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2021-02-22 15:30:36 UTC  
> Url: https://github.com/boostorg/json/pull/502#issuecomment-783457034  

Casting an `object`, `array`, or `string` back to a `value&` or `value const&` is not a supported function of this library, and never will be.

---
