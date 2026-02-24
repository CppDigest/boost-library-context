# #58 Fix conversion operators of injected primitive types [Merged]

> Username: yuri-kilochek  
> Created at: 2017-07-25 14:46:00 UTC  
> Updated at: 2017-08-11 13:13:39 UTC  
> Merged at: 2017-08-11 13:13:39 UTC  
> Closed at: 2017-08-11 13:13:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/58  

`archive::class_id_type`  conversion operators convert to different types for input (`int_least16_t`) and output (`int`) thus breaking archive pairs that rely on these being identical and do not explicitly define `.{save|load}_override` for `class_id_type`,  forwarding to corresponding primitive overload of `{save|load}` instead.

---
