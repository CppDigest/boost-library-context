# #7 MSVC 9.0 does not allow the member template keyword [Closed]

> Username: bjoernthiel  
> Created at: 2014-02-11 12:50:44 UTC  
> Updated at: 2014-08-12 12:51:22 UTC  
> Closed at: 2014-02-12 08:52:24 UTC  
> Url: https://github.com/boostorg/config/pull/7  

Working with MSVC 9.0 and 11.0 I had to change this to have the BOOST_NESTED_TEMPLATE macro in place.

---

## Comment 1

> Username: bjoernthiel  
> Created_at: 2014-02-11 12:55:07 UTC  
> Url: https://github.com/boostorg/config/pull/7#issuecomment-34751340  

See http://msdn.microsoft.com/en-us/library/bb531344(v=vs.110).aspx

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2014-02-11 17:46:20 UTC  
> Url: https://github.com/boostorg/config/pull/7#issuecomment-34782712  

I'm really not sure about this: ever since we first enabled support for member templates with VC8 and onwards we have never set this macro for VC++ and it hasn't caused us any issues that I know of.  It's true that versions prior to VC12 didn't force you to use ::template to disambiguate nested templates, but they were perfectly happy with it's presence as well.  
  
Is this in Boost code that you were finding the need for this?

---

## Comment 3

> Username: bjoernthiel  
> Created_at: 2014-02-12 06:56:04 UTC  
> Url: https://github.com/boostorg/config/pull/7#issuecomment-34843941  

You are absolutely right. VC9 accepts the member template keyword as well.   
There has been a different error (not in boost code) which I corrected later that confused me.  
  
So please drop my merge request.

---
