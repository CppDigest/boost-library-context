# #482 - value::to_number could be better documented in the exposition [Closed]

> Username: pmconrad  
> Created at: 2021-01-08 16:06:46 UTC  
> Updated at: 2022-08-01 05:18:38 UTC  
> Closed at: 2022-08-01 05:18:38 UTC  
> Url: https://github.com/boostorg/json/issues/482  

*(This is just my opinion of course.)*  
  
I was quite happy to read about `boost::json` after seeing it mentioned in the `boost-1.75` release notes, and I'm really impressed by your work. Thanks a lot!  
  
After spending quite some time on replacing the built-in serialization code of a bigger project with boost::json, I have come to the conclusion that the distinction of uint64, int64 and double as the numeric backend for JSON numbers is probably the biggest (hopefully single) design flaw of the `boost::json` API.  
  
As a user of the library, I do not care about the internal representation of numbers. I expect to be able to query certain properties of a number value (e. g. `v < 0`, or `v.is_integral()`). And I expect to be able to retrieve it as a specific type where the internal representation is automatically converted into that type, if possible.  
  
Moreover, the distinction also breaks the goal mentioned in the README:  
> Any value which you build can be serialized and then deserialized, guaranteeing that the result will be equal to the original value.  
  
Specifially,  
* after serializing and deserializing a numeric value, the type of the result can differ from the original type  
* when parsing externally generated valid JSON, numbers may lose precision (I suppose)  
  
Note that the JSON standard only has a general "number" type with no limitations on the precision or magnitude.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2021-01-08 16:17:23 UTC  
> Url: https://github.com/boostorg/json/issues/482#issuecomment-756846819  

> I expect to be able to retrieve it as a specific type where the internal representation is automatically converted into that type, if possible.  
  
This is accomplished using `value::to_number`, which has overloads for exceptions and error codes:  
  
https://www.boost.org/doc/libs/develop/libs/json/doc/html/json/ref/boost__json__value/to_number.html  
  
> I expect to be able to query certain properties of a number value (e. g. v < 0, or v.is_integral())  
  
The comparison will not work because `v` (which I assume has type `json::value`) is a variant. You have to convert it to a number first, for example `v.to_number<std::int64_t>() < 0`.  
  
`value::is_integral()` does not yet exist. We could add it to the library (it would appear in the next release of Boost). For now you can write your own utility function:  
  
```  
bool is_integral( boost::json::value const& jv )  
{  
  return  
    jv.kind() == boost::json::kind::int64 ||  
    jv.kind() == boost::json::kind::uint64;  
}  
```

---

## Comment 2

> Username: pmconrad  
> Created at: 2021-01-08 19:35:14 UTC  
> Url: https://github.com/boostorg/json/issues/482#issuecomment-756954060  

> This is accomplished using value::to_number, which has overloads for exceptions and error codes:  
  
Thanks, I missed that.
