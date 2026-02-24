# #95 - Add support for boost::json::value_from, boost::json::value_to [Open]

> Username: pdimov  
> Created at: 2022-10-14 11:28:14 UTC  
> Updated at: 2023-01-08 02:51:25 UTC  
> Url: https://github.com/boostorg/system/issues/95  

* `result<T, E>`: `{ "value": t }` or `{ "error": e }`;  
* `error_code`: `{ "category": "system", "value": 5, "message": "Access is denied" }`;  
* `error_condition`: same;  
* `error_code` wrapping `std::error_code`: `"category": "std:system"`;  
* for `value_to`, when the category name is unrecognized (not one of "system", "generic", "std:system", "std:generic", "std:iostream"?, "std:future"?): deserialize to `unknown_category`, same value;  
* maybe use the unknown category instead of "interop", too, without modifying the value.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-01-07 04:01:39 UTC  
> Url: https://github.com/boostorg/system/issues/95#issuecomment-1374370029  

The [JSON-RPC specification](https://www.jsonrpc.org/specification) uses `{ "result": t }` for the value case, instead of `"value"`; it also uses `{ "code": 5 }` for the error code, instead of `"value"`.

---

## Comment 2

> Username: pdimov  
> Created at: 2023-01-08 02:51:24 UTC  
> Url: https://github.com/boostorg/system/issues/95#issuecomment-1374693293  

The category in the `std::error_code` case should probably also be just `"system"` or `"generic"`, so as to match the eventual `value_from` for `std::error_code` provided by Boost.JSON.
