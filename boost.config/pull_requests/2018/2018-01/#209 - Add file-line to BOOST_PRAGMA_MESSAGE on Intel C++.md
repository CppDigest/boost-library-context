# #209 Add file/line to BOOST_PRAGMA_MESSAGE on Intel C++ [Merged]

> Username: pdimov  
> Created at: 2018-01-19 03:05:30 UTC  
> Updated at: 2019-11-02 11:17:05 UTC  
> Merged at: 2018-01-25 10:13:36 UTC  
> Closed at: 2018-01-25 10:13:36 UTC  
> Url: https://github.com/boostorg/config/pull/209  



---

## Comment 1

> Username: eldiener  
> Created_at: 2018-01-19 03:46:38 UTC  
> Url: https://github.com/boostorg/config/pull/209#issuecomment-358858940  

Since Intel C++'s __pragma(message...) is documented as being completely compatible with VC++, would it not be better just to change:  
  
#elif defined(_MSC_VER)  
\# define BOOST_PRAGMA_MESSAGE(x) __pragma(message(__FILE__ "(" BOOST_STRINGIZE(__LINE__) "): note: " x))  
  
to  
  
#elif defined(_MSC_VER) || defined(__INTEL_COMPILER)  
\# define BOOST_PRAGMA_MESSAGE(x) __pragma(message(__FILE__ "(" BOOST_STRINGIZE(__LINE__) "): note: " x))  
  
rather than code a separate case for Intel C++ which is the same as that for VC++ ?

---

## Comment 2

> Username: pdimov  
> Created_at: 2018-01-19 03:53:19 UTC  
> Url: https://github.com/boostorg/config/pull/209#issuecomment-358859755  

Intel C++ for Linux defines `__GNUC__`. I could test for `__INTEL_COMPILER` in both, I suppose. A matter of taste.

---

## Comment 3

> Username: eldiener  
> Created_at: 2018-01-19 04:02:46 UTC  
> Url: https://github.com/boostorg/config/pull/209#issuecomment-358860879  

I think it is better to test for Intel twice than to duplicate code but I understand the reason for your choice.

---
