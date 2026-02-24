# #43 Add support for std::boolalpha through new conversion specifier "%b" [Merged]

> Username: jeking3  
> Created at: 2017-10-24 19:52:42 UTC  
> Updated at: 2017-10-24 20:28:15 UTC  
> Merged at: 2017-10-24 20:28:13 UTC  
> Closed at: 2017-10-24 20:28:13 UTC  
> Url: https://github.com/boostorg/format/pull/43  

With-Commit-String values are from msvc-14.1 using default en_US locale.  
  
| String | Data | Pre-Commit | With-Commit-String |  
| -: | :- | :- | :- |   
| ``%b`` | ``false`` | boost::bad_format_string: format-string is ill-formed | ``false`` |  
| ``%b`` | ``true`` | boost::bad_format_string: format-string is ill-formed | ``true`` |  
  
Rules and techniques applicable to `std::boolalpha` apply in customizing the result.  
  
This closes #31

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-24 20:27:46 UTC  
> Url: https://github.com/boostorg/format/pull/43#issuecomment-339120635  

This passes all CI on my fork which is the same as boostorg, and travis is REALLY slow today.  So, moving on...

---
