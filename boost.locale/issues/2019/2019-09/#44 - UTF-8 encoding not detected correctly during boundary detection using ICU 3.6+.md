# #44 - UTF-8 encoding not detected correctly during boundary detection using ICU 3.6+ [Closed]

> Username: josh-stoddard-tanium  
> Created at: 2019-09-11 19:16:20 UTC  
> Updated at: 2023-03-08 16:43:10 UTC  
> Closed at: 2023-03-08 16:43:10 UTC  
> Url: https://github.com/boostorg/locale/issues/44  

ICU supports direct, in-place boundary detection for a c-strings encoded in UTF-8.  `boost::locale::boundary::impl_icu::do_map< char >()` takes advantage of this when this check succeeds: https://github.com/boostorg/locale/blob/develop/src/icu/boundary.cpp#L142  
  
This check only succeeds when the encoding is literally `"UTF-8"` and fails for similar strings, such as `"utf-8"`.  I submit that this is a bug, and this check should pass when the encoding is `"utf-8"` or any other string value meaning but not exactly equal to `"UTF-8"`.  
  
Moreover, when generating a locale, `boost::locale::util::locale_data::parse_from_encoding()` normalizes the encoding string to lowercase: https://github.com/boostorg/locale/blob/develop/src/util/locale_data.cpp#L86  
  
This means that generating locale `"en_US.UTF-8"` results in an encoding value of `"utf-8"`, which will be affected by this issue.  This value is not exposed to the user, so there is not an easily accessible workaround.

---

## Comment 1

> Username: Flamefire  
> Created at: 2023-03-07 19:27:57 UTC  
> Url: https://github.com/boostorg/locale/issues/44#issuecomment-1458705815  

This was indeed a bug which I just came by. Thanks!  
  
> Moreover, when generating a locale, boost::locale::util::locale_data::parse_from_encoding() normalizes the encoding string to lowercase:  
  
This was the actual bug, as it even was documented otherwise. It should have been uppercase not lowercase. I fixed that with https://github.com/boostorg/locale/commit/f6f897b413f7c8a67f2781a5f196ecb984dcd375  
  
Allowing other spellings (e.g. omitting the dash) will also be possible with #154
