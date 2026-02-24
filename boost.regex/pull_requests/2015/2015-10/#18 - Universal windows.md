# #18 Universal windows [Merged]

> Username: mauve  
> Created at: 2015-10-08 08:50:16 UTC  
> Updated at: 2015-10-08 10:02:13 UTC  
> Merged at: 2015-10-08 09:31:53 UTC  
> Closed at: 2015-10-08 09:31:53 UTC  
> Url: https://github.com/boostorg/regex/pull/18  

Add support for universal windows (aka winrt, windows store).  
  
Instead of just disabling all Win32 "extensions" by just defining BOOST_REGEX_NO_W32 I opted to disable _just_ the parts which need disabling. This way BOOST_REGEX_HAS_MS_STACK_GUARD will still be defined when targeting uwp.  
  
w32_regex_traits has been disabled because it uses LCID which has been deprecated since Vista and LCID no longer available in windows store (somewhat true).  
  
However an interesting fact is that the c++ locale actually works with the locale-names supported since Vista as can be seen from this dirty little test program:  
  
https://gist.github.com/mauve/fd5feb08333bda97f06b

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2015-10-08 09:31:48 UTC  
> Url: https://github.com/boostorg/regex/pull/18#issuecomment-146471666  

Thanks!

---

## Comment 2

> Username: mauve  
> Created_at: 2015-10-08 10:02:13 UTC  
> Url: https://github.com/boostorg/regex/pull/18#issuecomment-146480154  

AWESOMESAUCE! Thanks!

---
