# #110 - Documentation/code clash on value::value(kind) [Closed]

> Username: AeroStun  
> Created at: 2020-06-13 14:55:30 UTC  
> Updated at: 2020-08-12 17:11:34 UTC  
> Closed at: 2020-08-12 17:11:34 UTC  
> Url: https://github.com/boostorg/json/issues/110  

On `devel` (`ef48831` at the time of writing)  
  
Docs say  
```cpp  
// json/doc/qbk/02_1_values.qbk:50  
value jv( kind::object );  
```  
and  
```cpp  
// json/doc/qbk/02_1_values.qbk:64  
jv = value( kind::array )  
```  
however the code does not allow for these statements, as no ctor for `value` takes a `kind`  
Instead the code supports the values `object_kind` and `array_kind` (which are validated by tests)  
  
Same applies to `kind::string` and `string_kind`  
  
Ran into that issue while converting an exitsting codebase from rapidjson to Boost.JSON

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-06-13 17:31:47 UTC  
> Url: https://github.com/boostorg/json/issues/110#issuecomment-643653668  

Thanks for pointing this out! I should note that Boost.JSON, is not officially part of Boost yet, it is awaiting a review manager and then a review. And it is still beta software. That said, it has extensive tests and coverage, and the API is mostly stable. We plan on supporting optional comments and optional utf-8 validation of input. The documentation still needs some refinement. However the library should work well for you and it has been extensively optimized. Don't hesitate to ask further questions or raise new issues, it will help us polish this thing!

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-06-23 21:48:40 UTC  
> Url: https://github.com/boostorg/json/issues/110#issuecomment-648446009  

Code snippets in the docs are compiled, `value( kind::array )` should not compile I wonder what is going on...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-06-23 21:51:34 UTC  
> Url: https://github.com/boostorg/json/issues/110#issuecomment-648447094  

Ah, I see. The code in 02_1_values.qbk needs to be turned into snippets which are compiled. Then, this kind of mistake will be detected.
