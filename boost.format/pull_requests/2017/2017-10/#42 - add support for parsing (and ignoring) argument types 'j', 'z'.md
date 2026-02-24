# #42 add support for parsing (and ignoring) argument types 'j', 'z' [Merged]

> Username: jeking3  
> Created at: 2017-10-24 16:28:17 UTC  
> Updated at: 2017-10-24 19:50:11 UTC  
> Merged at: 2017-10-24 19:50:08 UTC  
> Closed at: 2017-10-24 19:50:08 UTC  
> Url: https://github.com/boostorg/format/pull/42  

Argument types in format specifications are ignored by boost::format because the argument type is carried through the conversion by `operator %`.  Type `'t'` is already used as a conversion specifier for tabulation therefore we only added what was missing, which was `'j'` and `'z'`.  
  
With-Commit values are from msvc-14.1.  
  
| String | Data | Pre-Commit | With-Commit |  
| -: | :- | :- | :- |   
| ``%jd`` | ``std::numeric_limits<intmax_t>::max() - 12345`` | boost::bad_format_string: format-string is ill-formed | ``9223372036854763462`` |  
| ``%zu`` | ``std::numeric_limits<size_t>::max() - 12345`` | boost::bad_format_string: format-string is ill-formed | ``4294954950`` |  
  
(Remember that size_t on Windows is 32-bit)  
  
This closes #29

---

## Comment 1

> Username: jeking3  
> Created_at: 2017-10-24 19:49:52 UTC  
> Url: https://github.com/boostorg/format/pull/42#issuecomment-339110205  

The build has been in the queue for 3 hours so I ran it on my fork and it's fine.  Moving on..

---
