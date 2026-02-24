# #7 Fix escape misbehavior on basic_string<char>. [Closed]

> Username: berkus  
> Created at: 2014-11-11 23:26:06 UTC  
> Updated at: 2015-01-21 12:58:10 UTC  
> Closed at: 2015-01-21 12:57:57 UTC  
> Url: https://github.com/boostorg/property_tree/pull/7  

Signed entity used for byte comparison, which fails for signed char.  
Due to this JSON serialization spits \uXXXX monstrosity instead of  
proper UTF-8.

---

## Comment 1

> Username: maxim-ky  
> Created_at: 2015-01-13 17:05:50 UTC  
> Url: https://github.com/boostorg/property_tree/pull/7#issuecomment-69779407  

Nice patch, thanks :) I cannot wait for boost release so I plan to use it just now

---

## Comment 2

> Username: berkus  
> Created_at: 2015-01-16 09:30:14 UTC  
> Url: https://github.com/boostorg/property_tree/pull/7#issuecomment-70228524  

Beware that property_tree doesn't use proper unicode _deserialization_ either and may fail.

---

## Comment 3

> Username: maxim-ky  
> Created_at: 2015-01-19 13:38:02 UTC  
> Url: https://github.com/boostorg/property_tree/pull/7#issuecomment-70493072  

Thanks for the caution, but deserialization works well with the used unicode subset (unlike serialization)

---

## Comment 4

> Username: CornedBee  
> Created_at: 2015-01-21 12:57:57 UTC  
> Updated_at: 2015-01-21 12:58:10 UTC  
> Url: https://github.com/boostorg/property_tree/pull/7#issuecomment-70832075  

Pulled into develop branch with a fix for MSVC, which really doesn't handle non-ASCII characters in narrow string literals well. Thank you for the patches.

---
