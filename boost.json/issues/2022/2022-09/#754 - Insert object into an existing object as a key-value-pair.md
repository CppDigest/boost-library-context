# #754 - Insert object into an existing object as a key-value-pair [Closed]

> Username: Tabascl  
> Created at: 2022-09-20 07:54:00 UTC  
> Updated at: 2022-09-21 09:29:49 UTC  
> Closed at: 2022-09-21 06:46:22 UTC  
> Url: https://github.com/boostorg/json/issues/754  

Boost Version 1.80  
I have a class hierarchy that looks like this: class A is composed of classes B and C.  
B and C each have a function that returns boost::json::object, which looks like this in case of B: `{"B": "someValue"}`  
  
A is supposed to compose each of these objects into a structure like this:  
```  
{  
  "A": {  
    "B": "someValue",  
    "C": "someOtherValue"  
  }  
}  
```  
  
The problem I'm facing is, that the resulting object always looks like this:  
```  
{  
  "A": [  
    { "B": "someValue" },  
    { "C": "someOtherValue" }  
  ]  
}  
```  
As you can see, the objects from the subclasses are not added as key-value pairs, but as objects, each containing a single pair, into an array.  
  
Here's a snippet to reproduce the issue:  
```  
#include <iostream>  
#include <boost/json.hpp>  
  
using namespace boost::json;  
  
object makeObject(std::string key, int value)  
{  
    object obj;  
    obj[key] = value;  
    return obj;  
}  
  
int main() {  
    object obj;  
    obj["main"] = {makeObject("test1", 123), makeObject("test2", 456)};  
  
    std::cout << obj << std::endl;  
    return 0;  
}  
```  
  
According to the Quick Look documentation, I would have expected this to not create an array with single-value objects in it.  
What am I doing wrong here?

---

## Comment 1

> Username: grisumbras  
> Created at: 2022-09-20 14:05:12 UTC  
> Url: https://github.com/boostorg/json/issues/754#issuecomment-1252405524  

The correct incantation in this case should probably be  
```c++  
{ {"test1", 123}  
, {"test2", 456}  
}  
```

---

## Comment 2

> Username: Tabascl  
> Created at: 2022-09-20 14:16:20 UTC  
> Updated at: 2022-09-20 14:17:34 UTC  
> Url: https://github.com/boostorg/json/issues/754#issuecomment-1252421467  

Maybe I should have been more clear here, I sadly can't do the incantation like you said. The makeObject function is here to enable a very simple example that shows the problem.  
Here's a more complete example, that hopefully outlines my problem a bit better:  
```  
#include <boost/json.hpp>  
#include <iostream>  
  
using namespace boost::json;  
  
class B  
{  
public:  
    object makeObject()  
    {  
        object obj;  
        obj["B"] = {{"property1", 1}};  
        return obj;  
    }  
};  
  
class C  
{  
public:  
    object makeObject()  
    {  
        object obj;  
        obj["C"] = {{"property1", 1}, {"property2", {1, 2, 3}}};  
        return obj;  
    }  
};  
  
class A  
{  
public:  
    object makeObject()  
    {  
        B b;  
        C c;  
  
        object obj;  
        obj["A"] = {b.makeObject(), c.makeObject()};  
        return obj;  
    }  
};  
  
int main()  
{  
    A a;  
    std::cout << a.makeObject() << std::endl;  
  
    return 0;  
}  
```  
Hopefully the purpose of the makeObject-function in the original example is clearer now. I somehow need to insert objects created elsewhere directly into another one.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2022-09-21 04:02:49 UTC  
> Url: https://github.com/boostorg/json/issues/754#issuecomment-1253175520  

So what are you expecting here, a 2-element array?  
```  
 obj["A"] = { b.makeObject(), c.makeObject() };  
```

---

## Comment 4

> Username: vinniefalco  
> Created at: 2022-09-21 04:08:01 UTC  
> Url: https://github.com/boostorg/json/issues/754#issuecomment-1253178524  

It sounds like perhaps you are asking to merge the key/value pairs in one object, into another object? so you want `obj` to contain three elements, with keys "A", "B", and "C"?  Does this issue describe your problem?  
https://github.com/nlohmann/json/issues/1807

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-09-21 04:28:38 UTC  
> Url: https://github.com/boostorg/json/issues/754#issuecomment-1253189557  

Is this what you are looking to do?  
https://github.com/vinniefalco/json/blob/f07024e2dd7019d6f504602a45a552f9eaabbfa8/example/merge.cpp#L125

---

## Comment 6

> Username: Tabascl  
> Created at: 2022-09-21 06:46:22 UTC  
> Url: https://github.com/boostorg/json/issues/754#issuecomment-1253276654  

> Is this what you are looking to do? https://github.com/vinniefalco/json/blob/f07024e2dd7019d6f504602a45a552f9eaabbfa8/example/merge.cpp#L125  
  
This is exactly what I was looking for. Thanks a lot!

---

## Comment 7

> Username: grisumbras  
> Created at: 2022-09-21 09:29:48 UTC  
> Url: https://github.com/boostorg/json/issues/754#issuecomment-1253446817  

I think, this should be equivalent to what @vinniefalco suggested  
```c++  
object&  
merge_objects(  
    object& dest,  
    object src)  
{  
    dest.insert(  
        std::make_move_iterator(src.begin()),  
        std::make_move_iterator(src.end()));  
    return dest;  
}  
...  
object obj;  
obj["A"] = object();  
merge_objects(obj, make_B());  
merge_objects(obj, make_C());  
```
