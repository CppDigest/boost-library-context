# #636 Unicode/char32 t [Merged]

> Username: theodelrieu  
> Created at: 2021-01-04 09:54:36 UTC  
> Updated at: 2021-03-15 23:56:43 UTC  
> Merged at: 2021-01-06 12:00:53 UTC  
> Closed at: 2021-01-06 12:00:53 UTC  
> Url: https://github.com/boostorg/spirit/pull/636  

Following up #634

---

## Comment 1

> Username: theodelrieu  
> Created_at: 2021-01-04 12:30:58 UTC  
> Url: https://github.com/boostorg/spirit/pull/636#issuecomment-753948952  

@djowel Time for Windows now, unfortunately I don't have one right now, do you have an idea on why it fails with 2017 only?

---

## Comment 2

> Username: djowel  
> Created_at: 2021-01-04 16:31:49 UTC  
> Url: https://github.com/boostorg/spirit/pull/636#issuecomment-754077276  

> @djowel Time for Windows now, unfortunately I don't have one right now, do you have an idea on why it fails with 2017 only?  
  
The other fails too (2015, 2017 and 2019). Type mismatch, it seems.

---

## Comment 3

> Username: theodelrieu  
> Created_at: 2021-01-04 16:34:35 UTC  
> Url: https://github.com/boostorg/spirit/pull/636#issuecomment-754078879  

>The other fails too (2015, 2017 and 2019).  
  
Indeed... I should open my eyes before posting.  
  
Anyway, I'll try to get it working tonight.

---

## Comment 4

> Username: theodelrieu  
> Created_at: 2021-01-04 20:53:14 UTC  
> Updated_at: 2021-01-04 20:56:46 UTC  
> Url: https://github.com/boostorg/spirit/pull/636#issuecomment-754212409  

@djowel This is because of the `__cplusplus`, MSVC returns `199711` by default. Is there an existing macro within boost (e.g. `BOOST_HAS_CXX11` or something) that deals with this kind of things already?  
  
EDIT: I guess `BOOST_NO_CXX11_CHAR32_T` will do!

---

## Comment 5

> Username: djowel  
> Created_at: 2021-01-06 12:01:00 UTC  
> Url: https://github.com/boostorg/spirit/pull/636#issuecomment-755259822  

merged

---

## Review 6 [Commented]

> Username: Kojoley  
> Created_at: 2021-03-15 15:29:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/spirit/pull/636#pullrequestreview-612325234  

📁 include/boost/spirit/home/x3/support/traits/print_attribute.hpp

```diff
  15 | #include <boost/spirit/home/x3/support/traits/attribute_category.hpp>
  16 | #include <boost/spirit/home/x3/support/traits/is_variant.hpp>
  17 |+ #include <boost/spirit/home/support/char_encoding/unicode.hpp>
```

> Username: Kojoley  
> Created_at: 2021-03-15 15:25:25 UTC  
> Updated_at: 2021-03-15 15:29:24 UTC  
> Url: https://github.com/boostorg/spirit/pull/636#discussion_r594434708  

I blazed through changes for the release and stuck on this line. It unconditionally includes the header which we previously only included when `BOOST_SPIRIT_X3_UNICODE` is defined.

> Username: djowel  
> Created_at: 2021-03-15 23:49:39 UTC  
> Updated_at: 2021-03-15 23:49:40 UTC  
> Url: https://github.com/boostorg/spirit/pull/636#discussion_r594766363  

Oh. That slipped through. We'll need to fix this for the 1.76.0 Beta candidate. @theodelrieu ?

> Username: Kojoley  
> Created_at: 2021-03-15 23:56:43 UTC  
> Url: https://github.com/boostorg/spirit/pull/636#discussion_r594768808  

I already fixed it in develop. Beta is already out, and master branch is closed, but we will have an opportunity to merge it for the release later on.


---
