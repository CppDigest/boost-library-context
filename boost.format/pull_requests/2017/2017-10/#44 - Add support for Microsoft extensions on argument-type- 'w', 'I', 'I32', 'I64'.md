# #44 Add support for Microsoft extensions on argument-type: 'w', 'I', 'I32', 'I64' [Merged]

> Username: jeking3  
> Created at: 2017-10-24 20:30:26 UTC  
> Updated at: 2017-10-24 20:57:16 UTC  
> Merged at: 2017-10-24 20:57:13 UTC  
> Closed at: 2017-10-24 20:57:13 UTC  
> Url: https://github.com/boostorg/format/pull/44  

Argument types in format specifications are ignored by `boost::format` because the argument type is carried through the conversion by `operator %`.  This change allows `boost::format` to parse through format strings with Microsoft specific argument types without throwing an exception.  
  
With-Commit-String values are from msvc-14.1 using default en_US locale.  
  
| String | Data | Pre-Commit | With-Commit-String |  
| -: | :- | :- | :- |   
| ``%wc`` | ``'5'`` | boost::bad_format_string: format-string is ill-formed | ``5`` |  
| ``%Id`` | ``12345`` | boost::bad_format_string: format-string is ill-formed | ``12345`` |  
| ``%I32d`` | ``12345`` | boost::bad_format_string: format-string is ill-formed | ``12345`` |  
| ``%I64d`` | ``1234567890123`` | boost::bad_format_string: format-string is ill-formed | ``1234567890123`` |  
  
This closes #35

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-24 20:55:35 UTC  
> Url: https://github.com/boostorg/format/pull/44#issuecomment-339128221  

Travis is moving at snails pace today and this passes the CI build in my fork, so I'm going to merge it.

---
