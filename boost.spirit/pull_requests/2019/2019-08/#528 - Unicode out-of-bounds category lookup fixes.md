# #528 Unicode out-of-bounds category lookup fixes [Merged]

> Username: cmazakas  
> Created at: 2019-08-25 18:18:32 UTC  
> Updated at: 2019-08-26 02:32:48 UTC  
> Merged at: 2019-08-25 23:59:56 UTC  
> Closed at: 2019-08-25 23:59:56 UTC  
> Url: https://github.com/boostorg/spirit/pull/528  

Fixes https://github.com/boostorg/spirit/issues/524  
  
Due to a bug in the condition evaluated by:  
`((sizeof(Char) <= sizeof(char_type)) || encoding::ischar(ch))`  
invalid Unicode literals would test `true` which is erroneous and when used with `x3::unicode::alpha` would create an out-of-bounds access in the `category_lookup(::boost::uint32_t ch)` function.

---

## Comment 1

> Username: djowel  
> Created_at: 2019-08-25 22:01:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/528#issuecomment-524667913  

@Kojoley should we remove Visual Studio 2015, ADDRMDL=64, TOOLSET=msvc-14.0 from the tests? or should we provide workarounds for that compiler, perhaps skipping some tests?

---

## Comment 2

> Username: djowel  
> Created_at: 2019-08-25 23:59:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/528#issuecomment-524675290  

OK, I'm going to merge this one with other char related stuff I am working on.

---

## Comment 3

> Username: djowel  
> Created_at: 2019-08-26 00:24:04 UTC  
> Url: https://github.com/boostorg/spirit/pull/528#issuecomment-524677118  

Merged yours with my work. Could you run the fuzzing sessions again?

---

## Comment 4

> Username: cmazakas  
> Created_at: 2019-08-26 01:53:50 UTC  
> Url: https://github.com/boostorg/spirit/pull/528#issuecomment-524687253  

Fuzzing is all good! The issue has been successfully resolved!

---

## Comment 5

> Username: djowel  
> Created_at: 2019-08-26 02:32:48 UTC  
> Url: https://github.com/boostorg/spirit/pull/528#issuecomment-524693236  

Wonderful!

---
