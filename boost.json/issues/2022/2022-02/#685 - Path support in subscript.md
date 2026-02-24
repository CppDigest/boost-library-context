# #685 - [Feature request] Path support in subscript. [Closed]

> Username: kilasuelika  
> Created at: 2022-02-24 03:50:17 UTC  
> Updated at: 2022-04-30 08:14:47 UTC  
> Closed at: 2022-04-30 08:14:47 UTC  
> Url: https://github.com/boostorg/json/issues/685  

I'm wondering if there will be path support in subscript:  
```  
//Json object  
boost::json::object obj;  
boost::json::object sub;  
sub["name"] = "child";  
obj["child"] = sub;  
  
//subscript.  
obj["child"].as_object()["sub"];  //Current.  
obj["child/sub"];  //What I'm suggest.  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-02-24 05:35:22 UTC  
> Url: https://github.com/boostorg/json/issues/685#issuecomment-1049511209  

With the syntax that you suggest, there would be a cost with checking for a forward slash that would be paid for all users - even those that are not using the path syntax. Have you had a look at these examples?   
  
https://github.com/boostorg/json/blob/56eaebf1cb1f6ff767cf9a2baa6920c0d2db0022/example/path.cpp#L49  
  
or  
  
https://github.com/boostorg/json/blob/56eaebf1cb1f6ff767cf9a2baa6920c0d2db0022/example/proxy.cpp#L61  
  
?

---

## Comment 2

> Username: grisumbras  
> Created at: 2022-02-24 13:30:48 UTC  
> Url: https://github.com/boostorg/json/issues/685#issuecomment-1049863394  

Note, that we are working on JSON Pointer implementation (https://tools.ietf.org/html/rfc6901). #480

---

## Comment 3

> Username: grisumbras  
> Created at: 2022-04-20 11:58:18 UTC  
> Url: https://github.com/boostorg/json/issues/685#issuecomment-1103848199  

@kilasuelika is JSON Pointer enough for you?  
Documentation is  here: https://www.boost.org/doc/libs/1_79_0/libs/json/doc/html/json/dom/nested_access.html

---

## Comment 4

> Username: kilasuelika  
> Created at: 2022-04-30 08:14:47 UTC  
> Url: https://github.com/boostorg/json/issues/685#issuecomment-1113946621  

> @kilasuelika is JSON Pointer enough for you? Documentation is here: https://www.boost.org/doc/libs/1_79_0/libs/json/doc/html/json/dom/nested_access.html  
  
It's exactly what I want. Thank you.
