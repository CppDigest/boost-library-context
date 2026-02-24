# #118 - I can't get numbers as uint64_t because they are int64_t !! [Closed]

> Username: cppdev-123  
> Created at: 2020-06-30 12:36:12 UTC  
> Updated at: 2020-07-20 17:53:16 UTC  
> Closed at: 2020-07-20 17:53:16 UTC  
> Url: https://github.com/boostorg/json/issues/118  

I dropped rapidjson and picked this library due to usage of exceptions which saves me a lot of checking and validation code . but now I'm surprised that I can't get numbers as uint64_t because the library deduced that they are int64_t ! I thought such simple conversion should be handled by the library like rapidjson . also I searched for unsigned integers in json and found that there is no support for unsigned but only signed integers . so if this library will treat all numbers as signed what is the usage of uint64_t !!  
  
smaple json :  
  
`"UpdateWaitTime": { "duration": 5, "unit": "minute" }`

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-30 12:41:34 UTC  
> Url: https://github.com/boostorg/json/issues/118#issuecomment-651765703  

use `json::number_cast<std::uint64_t>(jv)` where `jv` is your value.

---

## Comment 2

> Username: cppdev-123  
> Created at: 2020-06-30 12:48:10 UTC  
> Updated at: 2020-06-30 12:48:19 UTC  
> Url: https://github.com/boostorg/json/issues/118#issuecomment-651768819  

I even could use `static_cast<uint64_t>(jv.as_int64())` but I think it is too verbose and cumbersome for such conversion . I think the problem is that `as_int64` is required to return a reference not a value but it can be worked around by providing something like `as_unum` which returns unsigned number by value

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-06-30 12:57:01 UTC  
> Url: https://github.com/boostorg/json/issues/118#issuecomment-651773143  

`number_cast` is different from `static_cast<uint64_t>(jv.as_int64())`, because `number_cast` will throw an exception if the stored value cannot be exactly represented as the requested integral type:  
https://github.com/CPPAlliance/json/blob/6057a9f76055506012343cf7690690d88f97cb34/include/boost/json/detail/number_cast.hpp#L105

---

## Comment 4

> Username: vinniefalco  
> Created at: 2020-06-30 12:58:34 UTC  
> Url: https://github.com/boostorg/json/issues/118#issuecomment-651773942  

If you want to force the number to unsigned without doing any checking you can write your own simple function:  
```  
std::uint64_t  
as_unum( json::value const& jv )  
{  
    if( jv.is_int64() )  
      return jv.get_int64();  
    ...  
```
