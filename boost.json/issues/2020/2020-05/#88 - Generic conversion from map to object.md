# #88 - Generic conversion from map to object [Closed]

> Username: vinniefalco  
> Created at: 2020-05-12 01:33:18 UTC  
> Updated at: 2020-07-01 04:12:17 UTC  
> Closed at: 2020-07-01 04:12:17 UTC  
> Url: https://github.com/boostorg/json/issues/88  

`to_value` could use an internal overload that converts a map-like object (key/value pairs) to a `json::object`.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-05-13 21:41:38 UTC  
> Url: https://github.com/boostorg/json/issues/88#issuecomment-628260981  

There are a few separate concerns here IMHO:  
  
1. represent an object as JSON (without necessarily _converting_ it)  
2. parse an object from JSON representation (without necessarily going through a value object)  
3. actual conversion to/from json

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-05-13 22:55:48 UTC  
> Url: https://github.com/boostorg/json/issues/88#issuecomment-628287845  

"object" here refers specifically to `json::object` while "map" refers specifically to any _ForwardRange_ whose value type is convertible to `json::key_value_pair`.

---

## Comment 3

> Username: sdkrystian  
> Created at: 2020-07-01 04:12:17 UTC  
> Url: https://github.com/boostorg/json/issues/88#issuecomment-652178191  

Added in #103
