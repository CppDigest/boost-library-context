# #662 - Adding [] operator to json::value? [Closed]

> Username: byeonghyeonyou  
> Created at: 2021-12-21 07:41:42 UTC  
> Updated at: 2021-12-24 05:48:50 UTC  
> Closed at: 2021-12-24 05:48:50 UTC  
> Url: https://github.com/boostorg/json/issues/662  

It would be great if json::value had [] operator.  
If there is the [] operator in json::value, it will be far more convenient to access subelements.  
  
Currently we can access a subelement with at() such as `value.at("key1").at(0).at("key2")`. It is more verbose and less readable than `value["key1"][0]["key2"]`.  
Proxy example provides [] operator, but using Proxy causes another difficulty. We need to construct Proxy object and then dereference it with json::value just to access the element(`(*Proxy(value)["key1"][0]["key2"]).as_int64()`).  
  
If json::value had [] operator, we can handle this more easily: `value["key1"][0]["key2"].as_int64()`.  
  
Would it be okay if I try adding it?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-12-21 16:00:42 UTC  
> Updated at: 2021-12-21 16:02:39 UTC  
> Url: https://github.com/boostorg/json/issues/662#issuecomment-998899611  

That's never going to happen. We are doing JSON Pointer though.

---

## Comment 2

> Username: grisumbras  
> Created at: 2021-12-21 16:02:32 UTC  
> Url: https://github.com/boostorg/json/issues/662#issuecomment-998901001  

We're planning to add support for JSON Pointer in the near future, if that's any consolation.
