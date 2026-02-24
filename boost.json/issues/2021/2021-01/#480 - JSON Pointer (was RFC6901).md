# #480 - JSON Pointer (was RFC6901) [Closed]

> Username: jano42  
> Created at: 2021-01-06 12:00:10 UTC  
> Updated at: 2022-03-14 12:09:07 UTC  
> Closed at: 2022-03-14 12:09:07 UTC  
> Url: https://github.com/boostorg/json/issues/480  

I'm wondering if accessing sub element is a feature that you could implement ?  
  
```json  
{  
     "foo1": "bar",  
     "foo2": "bar2",  
     "foo3": {  
            "sfoo1" : 1,  
            "sfoo2" : "42"  
     }  
}  
```  
  
The idea is to get a direct access to a sub-element like this:  
  
```cpp  
boost::json::object data;  
//parse data  
auto &magicNumber = data["foo3.sfoo2"];  
```  
  
The path separator would be configurable ("." or "/",...)  
  
This is [RFC6901](https://tools.ietf.org/html/rfc6901), as it's done by [rapidjson ](https://rapidjson.org/md_doc_pointer.html)for example

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-01-06 12:07:46 UTC  
> Url: https://github.com/boostorg/json/issues/480#issuecomment-755262835  

Have a look at:  
https://github.com/boostorg/json/blob/develop/example/path.cpp  
and  
https://github.com/boostorg/json/blob/develop/example/proxy.cpp  
?

---

## Comment 2

> Username: hadrielk  
> Created at: 2021-01-06 18:39:03 UTC  
> Url: https://github.com/boostorg/json/issues/480#issuecomment-755494883  

If this was added natively, how would it distinguish this scenario?:  
```  
{  
     "foo1": "bar",  
     "foo2": "bar2",  
     "foo3": {  
            "sfoo1" : 1,  
            "sfoo2" : "42"  
     },  
    "foo3.sfoo2" : "can't touch me!"  
}  
```  
Would you expect the user to escape the "`.`" before passing it to `Boost.Json`? That would be annoying for using string literals and other inputs, not to mention require `Boost.Json` to do more work to unescape it it for matching.  
  
Normally for Json-Pointer support, libraries have differently named functions/methods so that they know the argument is a JSON Pointer, or have a lightweight class-type to denote it.  
  
(Also, minor point but that isn't the JSON Pointer syntax - "`/foo3/sfoo2`" is, I believe)

---

## Comment 3

> Username: vinniefalco  
> Created at: 2021-01-06 18:54:21 UTC  
> Url: https://github.com/boostorg/json/issues/480#issuecomment-755514455  

JSON Pointer support is a planned feature and it will follow the RFC (avoiding the problems that @hadrielk mentioned).

---

## Comment 4

> Username: grisumbras  
> Created at: 2022-03-14 12:09:03 UTC  
> Url: https://github.com/boostorg/json/issues/480#issuecomment-1066709586  

Implemented in #575
